# Implementation Plan

## 1. Objective

Build a Next.js application backed by Supabase that lets a user collect, organize, rate, and discover TV shows and movies. The product must preserve a clear separation between public catalog data and the user's personal overlay, use `(namespace_id, user_id)` for all persisted user-owned data, and provide high-quality discovery through Search, Ask, Alchemy, Explore Similar, and AI Scoop.

This plan is implementation-only; it does not include coding the product in this benchmark step.

## 2. Guiding Requirements

- Use Next.js latest stable as the application runtime for UI and server boundaries.
- Use Supabase through official client libraries for persistence.
- Do not require Docker for the primary development or benchmark path.
- Store all durable user data server-side; client caches and local settings must be disposable.
- Scope all user-owned persisted records by `namespace_id` and `user_id`.
- Support destructive test reset by namespace without global database teardown.
- Keep real authentication optional in benchmark mode, but design identity so OAuth can replace dev identity injection later without a schema redesign.
- Treat each saved show as the user's version of a catalog item: user status, interest, tags, rating, and AI Scoop must win everywhere the show appears.
- Make every recommendation actionable by resolving AI outputs to real catalog items when possible.
- Keep all AI surfaces spoiler-safe by default, taste-aware, specific, warm, opinionated, and within TV/movie scope.

## 3. Proposed Architecture

### 3.1 Application Layers

Use a layered Next.js App Router structure:

- `src/app`: route shells, layouts, server components, API route handlers/server actions.
- `src/pages` or route feature folders following the repo guidance: top-level pages contain self-contained features, and features own their hooks, utilities, child features, and tests.
- `src/components`: shared UI primitives only.
- `src/config`: environment parsing, constants, provider keys, namespace defaults, status/interest definitions.
- `src/theme`: design tokens, typography scale, spacing, color tokens, responsive rules.
- `src/server`: server-only Supabase client, identity resolution, catalog provider adapters, AI provider adapters, persistence services.
- `src/lib` or `src/utils`: pure shared utilities such as date parsing, merge helpers, AI output parsing, title normalization, and export serialization.

Keep TSX components humble: route/page components bind data and render markup, while behavior lives in hooks and service functions.

### 3.2 Server Boundary

Use Next.js route handlers or server actions for:

- collection CRUD and merge operations,
- detail refreshes from the external catalog,
- AI calls and structured parsing/retry logic,
- AI recommendation resolution,
- data export zip generation,
- test namespace reset.

Client components should not receive elevated Supabase keys or call AI/catalog providers directly when the call requires secrets. Browser code may use a Supabase anon key only when appropriate and still must be constrained by namespace/user policies.

### 3.3 Provider Abstractions

Create provider interfaces rather than coupling UI to vendors:

- `CatalogProvider`: search, detail lookup, external-id lookup, images/logos/videos, credits, recommendations/similar, watch providers, person details, person credits.
- `AIProvider`: Ask, Ask-with-mentions, Scoop, concepts, concept recommendations, summarization.
- `StorageProvider`: Supabase-backed show/settings access, namespace reset, export snapshot.

The default implementation can use a common entertainment catalog provider and a configurable AI provider, but the rest of the app should depend on provider contracts.

## 4. Environment, Scripts, and Repo Deliverables

Add required benchmark deliverables:

- `.env.example`
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - server-only Supabase elevated key if needed for admin/test reset
  - `APP_NAMESPACE_ID` or equivalent
  - benchmark/dev default `USER_ID`
  - catalog API key
  - AI API key and default AI model
  - production/development identity-injection toggle
- `.gitignore`
  - ignore `.env*` except `.env.example`
- package scripts
  - `dev`: start the app
  - `test`: run unit/integration tests
  - `test:reset`: delete/reset data for the configured namespace
  - `db:migrate`: apply Supabase migrations
  - optional `db:seed`: load deterministic fixtures inside a namespace

Document the benchmark startup path in `README.md`: configure env, run migrations, start app, run tests, reset namespace.

## 5. Persistence Plan

### 5.1 Supabase Schema

Create repeatable migrations for these tables:

- `shows`
  - `namespace_id`
  - `user_id`
  - catalog identity: `id`, `show_type`, `external_ids`
  - catalog metadata fields from `storage-schema.md`
  - user fields: `my_tags`, `my_score`, `my_status`, `my_interest`
  - per-field update timestamps: `my_tags_update_date`, `my_score_update_date`, `my_status_update_date`, `my_interest_update_date`
  - AI fields: `ai_scoop`, `ai_scoop_update_date`
  - management fields: `details_update_date`, `creation_date`, `is_test`, `provider_data`
  - unique constraint on `(namespace_id, user_id, id)`
- `cloud_settings`
  - `namespace_id`, `user_id`
  - username, catalog key if user-entered, AI key if user-entered, AI model, version timestamp
  - unique constraint on `(namespace_id, user_id, id)`
- `app_metadata`
  - `namespace_id`, `user_id`, `data_model_version`
- optional `test_fixtures` or seed metadata if helpful for deterministic benchmark tests.

Do not store transient data such as cast, crew, seasons, videos, image galleries, recommendations, similar shows, or AI session outputs as durable collection records.

### 5.2 Row-Level Security and Isolation

Enable RLS where feasible and enforce:

- user records are filtered by both `namespace_id` and `user_id`,
- service/admin reset operations accept a namespace and cannot delete global data,
- benchmark identity injection is development/test-only,
- production mode requires real auth/session identity.

Even if RLS is limited by the dev identity strategy, all server services must require namespace and user inputs and include them in every query.

### 5.3 Identity Resolution

Implement an identity module that returns:

- `namespace_id` from env, request header, or run configuration,
- `user_id` from dev-only header, dev-only selector cookie, or configured default user,
- production identity from auth session later.

Keep `user_id` opaque and stable. Do not encode provider-specific assumptions in schema or code.

### 5.4 Merge and Save Rules

Implement a central merge service used by all catalog refresh and save flows:

- Non-user catalog fields use `selectFirstNonEmpty(newValue, oldValue)`.
- User fields resolve by per-field timestamp, newest wins.
- `details_update_date` updates on catalog merge.
- `creation_date` is set only at first creation.
- Existing user overlay always takes precedence when a show appears in Search, Ask, recommendations, Alchemy, Explore Similar, Home, or Detail.

Implement collection membership as `my_status` being non-null.

Save triggers:

- setting any status saves the show,
- choosing Interested/Excited saves as `Later` plus the chosen interest,
- rating an unsaved show saves as `Done`,
- adding a first tag to an unsaved show saves as `Later + Interested`.

Removal:

- clearing/reselecting a status prompts confirmation,
- removal deletes the show row for that user/namespace or clears all My Data according to the product semantics,
- all status, interest, tags, rating, and AI Scoop are cleared,
- confirmation suppression/count is local UI state and can be disposable.

## 6. Data Model Details

### 6.1 Show Fields

Represent the schema from `storage-schema.md` with strong TypeScript types:

- show type: `movie`, `tv`, `person`, `unknown`
- status: `active`, `next`, `later`, `done`, `quit`, `wait`
- interest: `interested`, `excited`
- provider data as an opaque region-indexed ID blob
- dates as ISO-8601 strings at API boundaries and database timestamps internally

Keep `next` in the model but do not surface it as a first-class UI status unless later requested.

### 6.2 Local Settings

Persist only disposable UI settings locally:

- `autoSearch`
- `fontSize`
- `hideStatusRemovalConfirmation`
- `statusRemovalCountKey`
- `lastSelectedFilter`

If local storage is cleared, the user's collection, ratings, tags, statuses, settings synced in Supabase, and AI Scoop must remain intact server-side.

### 6.3 Export Snapshot

Create an export service that produces a `.zip` containing JSON:

- saved shows and all My Data,
- cloud settings that are safe to export,
- app metadata,
- local settings and UI state if available,
- ISO-8601 dates.

Do not include secrets by default unless the UI explicitly warns and the product owner approves.

## 7. External Catalog Plan

### 7.1 Search

Implement text search by title/keyword:

- fetch global catalog results live,
- normalize movie/TV results into `Show` objects,
- overlay saved user data by matching catalog ID in the current namespace/user,
- display poster grid with in-collection and user-rating indicators,
- selecting a tile opens Detail.

### 7.2 Detail Fetching

On Detail:

- fetch full catalog details, images/logos/backdrops/videos, credits, providers, recommendations, similar shows, seasons for TV, budget/revenue for movies,
- merge persisted catalog fields into the saved record only when the show is or becomes saved,
- keep transient detail data in request/session state, not durable show records.

### 7.3 Recommendation Resolution

For AI recommendation outputs:

- prefer external ID lookup when provided,
- validate case-insensitive title match,
- require media type where possible,
- fall back to deterministic search handoff or non-interactive display if unresolved,
- preserve AI reason as transient text alongside the resolved Show.

## 8. AI Plan

### 8.1 Shared AI Context

Build a context assembler that can include:

- current user's saved library and My Data,
- current show context for Detail/Ask/Scoop,
- selected concepts,
- recent chat turns,
- summarized older turns,
- provider/model settings.

Limit context size while preserving taste signals: statuses, interest, tags, user rating, titles, types, and AI Scoop snippets when useful.

### 8.2 Shared Guardrails

Every AI surface must:

- stay in TV/movie domain,
- be spoiler-safe by default,
- be warm, chatty, specific, and opinionated,
- avoid generic genre boilerplate,
- acknowledge mixed reception when relevant,
- provide actionable recommendations where appropriate.

### 8.3 Ask

Build a session-only chat surface:

- welcome state with 6 random starter prompts and refresh,
- user/assistant turns in memory for current session,
- summarize older turns after about 10 messages,
- assistant response as concise dialogue with bullets for recommendations,
- structured mentioned-show extraction with `commentary` and `showList`,
- `showList` parser for `Title::externalId::mediaType;;...`,
- retry once with stricter formatting on parse failure,
- render mentioned shows in a horizontal strip after resolving them to catalog records.

Ask About This Show should enter Ask with seeded show context from Detail.

### 8.4 Scoop

Implement Detail AI Scoop:

- on-demand generation,
- progressive streaming UI if provider supports it,
- 150-350 word mini taste review with personal take, stack-up, centerpiece Scoop, fit/warnings, and verdict,
- cache freshness of about 4 hours,
- persist only if the show is in collection,
- otherwise keep generated Scoop ephemeral.

### 8.5 Concepts

Implement single-show and multi-show concept generation:

- bullet list only,
- 1-3 words each,
- no explanation,
- spoiler-free,
- specific and evocative,
- diverse across structure, tone, emotion, relationship dynamics, and craft,
- multi-show concepts must be shared across all selected inputs,
- generate 8 concepts by default for quality-bar parity.

### 8.6 Explore Similar

Detail page flow:

- user taps Get Concepts,
- concepts render as chips,
- selecting/unselecting concepts clears downstream results,
- user selects at least one concept,
- Explore Shows returns 5 resolved catalog recommendations,
- reasons explicitly mention selected concepts.

### 8.7 Alchemy

Find/Discover Alchemy flow:

- user selects 2+ starting shows from library and global catalog,
- Conceptualize Shows fetches shared concept catalysts,
- user selects 1-8 concepts,
- Alchemize returns 6 resolved recommendations with reasons,
- changing starting shows clears concepts/results,
- changing concepts clears results,
- More Alchemy starts a chained round using result shows as candidate inputs,
- session state is not persisted.

## 9. UI and Feature Plan

### 9.1 App Shell

Build a persistent shell with:

- left/navigation panel for filters,
- main content outlet,
- persistent Find/Discover entry,
- persistent Settings entry,
- responsive behavior for mobile and desktop,
- media type toggle applied on top of the selected collection filter.

### 9.2 Collection Home

Home displays saved shows grouped by:

1. Active, prominent/larger tiles
2. Excited, meaning `Later + Excited`
3. Interested, meaning `Later + Interested`
4. Other statuses: Wait, Quit, Done, Later without interest, and any unclassified items

Filters:

- All Shows
- one filter per user tag
- No tags when any tagless saved show exists
- genre
- decade
- community score range
- media type toggle: All / Movies / TV

Empty states:

- no collection: prompt to Search/Ask,
- filter yields none: "No results found."

### 9.3 Find/Discover

Use a clear mode switcher:

- Search
- Ask
- Alchemy

Respect the `Search on Launch` setting by auto-opening Search where configured.

### 9.4 Show Detail

Preserve the intended narrative hierarchy:

1. header media carousel with graceful fallback,
2. core facts and community score,
3. My relationship controls near the top,
4. tag chips,
5. overview and Scoop,
6. Ask About This Show,
7. genres and languages,
8. traditional recommendations strand,
9. Explore Similar,
10. streaming availability,
11. cast and crew,
12. seasons for TV,
13. budget/revenue for movies.

Key interactions:

- status chips include Active, Interested, Excited, Done, Quit, Wait,
- Interested/Excited map to `Later + Interest`,
- rating unsaved saves as Done,
- tagging unsaved saves as Later + Interested,
- reselecting active status triggers removal confirmation,
- Overview remains factual while Scoop carries the personality.

### 9.5 Person Detail

Build person profile pages with:

- image gallery,
- name and bio,
- filmography grouped by year,
- credits that open Show Detail,
- lightweight analytics: average project ratings, top genres, projects by year.

### 9.6 Settings

Settings includes:

- font size/readability,
- Search on Launch,
- username,
- AI API key and model selection if user-entered settings are enabled,
- catalog API key if user-entered settings are enabled,
- Export My Data.

Import/Restore should be represented as a future enhancement unless explicitly included later.

## 10. Status, Tags, and Rating Workflows

Implement a single `useMyShowControls` style hook or equivalent feature service used by Detail and tiles:

- derive current display state from saved overlay plus catalog data,
- expose handlers for status, interest, tags, rating, and removal,
- centralize implicit save defaults and timestamp updates,
- ensure every mutation refetches/invalidates relevant collection and detail views.

Add tag management behavior:

- free-form tag creation,
- implicit tag library derived from saved shows,
- case/whitespace normalization rules,
- "No tags" collection filter when applicable.

Rating behavior:

- support unrated/null state,
- store numeric rating and update timestamp,
- clearing a rating should set nil unless the later optional explicit Unrated state is accepted.

## 11. Testing Plan

### 11.1 Unit Tests

Cover pure business logic:

- status/interest mapping,
- implicit save defaults,
- removal semantics,
- collection grouping,
- filters and media toggle composition,
- tag normalization,
- date parsing,
- `selectFirstNonEmpty`,
- timestamp-based merge conflicts,
- AI `showList` parsing and retry fallback,
- recommendation title/media matching,
- Scoop freshness checks.

### 11.2 Integration Tests

Use Supabase test namespace data:

- CRUD operations scoped by `(namespace_id, user_id)`,
- two namespaces cannot read/write each other's records,
- two users in same namespace remain separated,
- reset command deletes only the target namespace,
- export includes correct saved shows/My Data,
- catalog refresh preserves user fields,
- destructive removal clears all My Data.

### 11.3 UI and E2E Tests

Cover critical journeys:

- Search -> Detail -> Interested save -> Home grouping,
- rating unsaved show saves as Done,
- adding first tag saves as Later + Interested,
- tag filter and media toggle,
- Ask recommendation appears in mentioned strip and opens Detail,
- Detail -> Get Concepts -> Explore Shows -> save recommendation,
- Alchemy from 2+ shows -> concepts -> 6 recs -> chain,
- status reselect removal confirmation,
- Settings -> Export My Data.

### 11.4 AI Quality Validation

Create fixtures and human-reviewable snapshots for:

- Scoop structure and tone,
- Ask directness and mentioned-show extraction,
- concept specificity and 1-3 word rule,
- Explore Similar returns 5 real recs,
- Alchemy returns 6 real recs,
- reasons explicitly tie to concepts,
- spoiler-safety and TV/movie-domain refusal/redirect.

Use the discovery quality rubric:

- voice >= 1,
- taste alignment >= 1,
- real-show integrity = 2,
- total >= 7/10.

## 12. Implementation Sequence

### Phase 0: Foundation

- Initialize Next.js, TypeScript, linting, testing, and style/theme baseline.
- Add `.env.example`, `.gitignore`, scripts, and README setup notes.
- Add Supabase clients, env validation, identity resolver, and namespace handling.
- Create migrations for shows, settings, metadata, indexes, constraints, and RLS policies.
- Add test reset command scoped by namespace.

### Phase 1: Core Data and Catalog

- Implement catalog provider adapter and normalized Show mapping.
- Implement persistence services for show upsert, merge, fetch, remove, and overlay.
- Implement collection filters, grouping, and saved-show selectors.
- Add unit and integration tests for merge/isolation/save rules.

### Phase 2: App Shell, Home, Search, Detail Basics

- Build navigation shell, collection Home, filters, media toggle, empty states.
- Build Search mode with poster grid and saved indicators.
- Build Show Detail with header, facts, overview, rating, status/interest, tags, traditional recommendations, providers, cast/crew, seasons, and movie financials.
- Wire all My Data mutations through shared business logic.

### Phase 3: Person Detail and Settings

- Build Person Detail, filmography, charts, and credit navigation.
- Build Settings for readability, Search on Launch, username, provider/model settings, and export.
- Implement export zip JSON snapshot.

### Phase 4: AI Infrastructure

- Implement AI provider abstraction, prompt/context builders, shared guardrails, and structured output parsers.
- Add Ask session state, starter prompts, summarization, mentioned-show resolution, and Detail handoff.
- Add Scoop generation, streaming state, 4-hour freshness, and persistence rules.

### Phase 5: Concept Discovery

- Add concept generation for single and multi-show inputs.
- Build Explore Similar on Detail with 5 resolved recs.
- Build Alchemy with 2+ input selection, concept selection cap of 8, 6 resolved recs, result reasons, clearing rules, and chaining.

### Phase 6: Hardening and Quality Pass

- Complete E2E coverage for key journeys.
- Validate namespace/user isolation and destructive reset.
- Run AI golden-set/manual quality checks.
- Audit local storage disposability.
- Audit secrets and server/client boundaries.
- Polish responsive layouts, loading states, error states, and accessibility.

## 13. Risks and Mitigations

- AI hallucinated or unresolved recommendations: require external IDs where possible, validate title/media type, retry structured formats once, and fall back to Search/non-interactive display.
- User data loss from catalog refreshes: centralize merge logic and test timestamp resolution thoroughly.
- Namespace leakage: make namespace and user explicit in every service method, add database constraints/RLS, and test cross-namespace isolation.
- Client/server secret leakage: validate env exposure, keep service keys server-only, and isolate provider calls behind server routes.
- AI context bloat: summarize older chat turns, trim library context to taste signals, and cap payload sizes.
- Detail page overload: preserve narrative hierarchy, cluster primary My Data actions early, and push long-tail content lower on the page.
- Future auth migration: keep `user_id` opaque and avoid schema assumptions tied to benchmark identity injection.

## 14. Acceptance Checklist

- App runs with only environment configuration, no source edits.
- Fresh database can be created from migrations.
- Tests can reset only one namespace.
- Every durable user-owned row includes namespace and user scope.
- Clearing local storage does not lose collection data.
- Saved user overlays appear consistently across Home, Search, Detail, Ask, Alchemy, Explore Similar, and traditional recommendations.
- Save defaults and removal semantics match the PRD.
- AI Scoop persists only for saved shows and respects 4-hour freshness.
- Ask chat and Alchemy sessions are session-only.
- Explore Similar returns 5 recs; Alchemy returns 6 recs.
- Concepts are bullet-only, 1-3 words, evocative, spoiler-free, and non-generic.
- Export My Data produces a zip with ISO-8601 JSON backup of saved shows and My Data.
