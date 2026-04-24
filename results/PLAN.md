# Implementation Plan

## 1. Scope, Baseline, And Architecture Decisions

Build the personal TV/movie companion described in `docs/prd/` as a Next.js application with Supabase persistence. The application must support collection management, catalog search, show/person detail pages, taste-aware AI discovery, data export, namespace isolation, and explicit user ownership. The plan intentionally does not implement source code.

Primary technical decisions:

- Use Next.js latest stable with TypeScript, React, App Router route files, and server routes/actions as the trusted boundary.
- Use Supabase through official client libraries. Browser code may use only the anon/public key; any service role key is server-only.
- Treat the backend as the source of truth for saved shows, settings that sync, AI Scoop cache, and migration metadata.
- Keep local storage limited to disposable UI preferences such as font size, search-on-launch, last selected filter, and removal confirmation suppression.
- Use a stable `namespace_id` for each benchmark run and a stable opaque `user_id` for every user-owned row. Effective partitioning is always `(namespace_id, user_id)`.
- Use development identity injection for benchmark mode, gated by environment, with a clean path to replace it with OAuth later.
- Place external catalog and AI providers behind adapter interfaces so provider changes do not rewrite product logic.
- Follow the fractal architecture guidance: route/page containers contain feature directories; TSX stays humble; feature logic lives in hooks and local utilities; shared primitives live in `src/components`, `src/hooks`, `src/utils`, `src/config`, and `src/theme`.

Target source layout:

```text
src/
  app/
    page.tsx
    find/page.tsx
    shows/[mediaType]/[id]/page.tsx
    people/[id]/page.tsx
    settings/page.tsx
    api/
      collection/
      catalog/
      ai/
      export/
      test-reset/
  pages/
    CollectionHome/
      CollectionHome.tsx
      features/
    FindDiscover/
      FindDiscover.tsx
      features/
    ShowDetail/
      ShowDetail.tsx
      features/
    PersonDetail/
      PersonDetail.tsx
      features/
    Settings/
      Settings.tsx
      features/
  server/
    identity/
    supabase/
    catalog/
    ai/
    export/
  domain/
    shows/
    collection/
    filters/
    ai/
    settings/
  components/
  hooks/
  utils/
  config/
  theme/
```

Route files should be thin and delegate to the corresponding page component. Feature directories should own their local hooks, constants, tests, and sub-features.

## 2. Environment, Scripts, And Repo Setup

Add the benchmark-required operational surface before feature work:

- `.env.example` with documented variables:
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - `SUPABASE_SERVICE_ROLE_KEY` for server-only benchmark data access and reset operations.
  - `BENCHMARK_NAMESPACE_ID`
  - `DEV_DEFAULT_USER_ID`
  - `ALLOW_DEV_IDENTITY_HEADER`
  - `CATALOG_API_KEY`
  - `CATALOG_REGION`
  - `AI_PROVIDER_API_KEY`
  - `AI_MODEL`
  - optional `AI_PROVIDER_BASE_URL`
- `.gitignore` that excludes `.env*` secrets while keeping `.env.example`.
- Package scripts:
  - `dev`: start the app.
  - `build`: production build.
  - `lint`: lint.
  - `typecheck`: TypeScript checks.
  - `test`: unit and integration tests.
  - `test:e2e`: Playwright user journeys.
  - `db:migrate`: apply Supabase migrations.
  - `test:reset`: delete only rows in the configured namespace, with test guardrails.

Document setup in `README.md`:

- Hosted Supabase is the primary path; Docker/local Supabase is optional only.
- How namespace isolation works.
- How development identity injection works.
- Which API keys are browser-safe and which are server-only.
- How to run destructive tests without global database teardown.

## 3. Supabase Schema And Data Ownership

Create repeatable migrations for the persistent model. Scope all user-owned records by `namespace_id` and `user_id`.

Core tables:

- `show_records`
  - Primary key: `(namespace_id, user_id, show_id, show_type)`.
  - Stores the persisted collection item, including public catalog fields, provider ID data, user overlay fields, AI Scoop, creation date, details update date, and `is_test`.
  - A row is in the collection only when `my_status` is non-null.
- `cloud_settings`
  - Primary key: `(namespace_id, user_id)`.
  - Stores username, AI model choice, settings version, and optional encrypted user-provided provider keys if that extension is implemented.
  - Do not store committed secrets. Environment keys are preferred for benchmark mode.
- `app_metadata`
  - Primary key: `(namespace_id, user_id)`.
  - Stores `data_model_version`.
- Optional `test_fixtures` or `reset_audit`
  - Used only to support safe namespace-scoped reset/debugging if useful.

Important persistence choices:

- Persist saved shows in one denormalized `show_records` table so public metadata and My Data merge as a single object, matching the PRD's `Show` concept.
- Do not persist Ask chat history, Alchemy sessions, Explore Similar result lists, cast, crew, seasons, videos, images collections, similar/recommended strands, or transient AI reasons.
- Store `provider_data` as JSON containing provider IDs by region only.
- Store tags as a string array or JSON array on `show_records`; derive the personal tag library from saved rows.
- Use ISO-8601 timestamps for user field update dates and export.

Access model:

- In benchmark mode, read/write user-owned rows through Next.js server routes/actions that resolve identity and apply `(namespace_id, user_id)` filters on every query.
- Keep Supabase service role usage server-only. If direct browser table access is later added, add RLS policies that enforce authenticated `user_id` and namespace claims.
- Design schema column names so future OAuth maps a provider identity to the same opaque `user_id` without schema redesign.

Reset model:

- `test:reset` deletes only rows matching the configured namespace and, where applicable, `is_test = true` or a requested test user.
- Never require dropping global schema or clearing rows from other namespaces.

## 4. Domain Model And Business Rules

Build domain modules before UI so every surface shares the same behavior.

Show model:

- Represent `showType` as `movie | tv | person | unknown`, with person used only for catalog/person contexts where needed.
- Represent statuses as `active | next | later | done | quit | wait`.
- Represent interest as `interested | excited`, applicable only when status is `later`.
- Treat `my_status != null` as collection membership.

Save/default rules:

- Setting any status saves a show.
- Selecting `Interested` sets `my_status = later` and `my_interest = interested`.
- Selecting `Excited` sets `my_status = later` and `my_interest = excited`.
- Rating an unsaved show saves it as `done`.
- Adding at least one tag to an unsaved show saves it as `later + interested`.
- Saving without explicit status defaults to `later + interested`, except rating-first saves default to `done`.
- Changing away from `later` makes interest irrelevant in UI but can retain the stored value for later use.
- Clearing/reselecting the active status triggers destructive removal confirmation and deletes the saved row, clearing status, interest, tags, rating, and AI Scoop.

Merge rules:

- External catalog refreshes merge into existing saved shows using `selectFirstNonEmpty(newValue, oldValue)` for public fields.
- Never overwrite a non-empty stored string/array with empty data or a non-null stored value with null.
- User fields resolve by their specific update timestamp: status, interest, tags, rating, and AI Scoop each keep the newest version.
- `creationDate` is set only on first save.
- `detailsUpdateDate` updates after catalog merge.
- Re-encountering a saved show anywhere must preserve My Data and refresh public metadata where available.

Display rule:

- Create a single `toShowViewModel` or equivalent merger that combines catalog results with saved overlays.
- Every surface that renders shows must call the overlay lookup by external ID/media type before display.
- Tiles must show in-collection and user-rating indicators when applicable.

Unit test the domain module heavily before UI work:

- All save triggers.
- Default status/interest behavior.
- Removal behavior.
- Timestamp conflict resolution.
- Overlay precedence.
- Tag library derivation.
- Filter predicates.

## 5. Catalog Provider Integration

Implement a provider-neutral catalog adapter with a TMDB-like capability set:

- Search by query across movies and TV.
- Fetch show details with images, videos, providers, credits, seasons, similar, and recommendations.
- Fetch person details, images, and combined credits.
- Resolve recommendations by external ID plus media type, then confirm title case-insensitively.
- Fallback to title/media search if an AI result lacks a usable external ID.

Mapping rules:

- Map catalog ID plus media type to stable `show_id` and `show_type`.
- Prefer movie title or TV name; reject undecodable items with no title.
- Infer media type only when catalog payload omits it and the fields are unambiguous.
- Map genre IDs to display names.
- Parse date values robustly.
- Generate renderable poster, backdrop, and logo URLs.
- Choose one best logo deterministically, preferring English when available.
- Store provider IDs by country and provider category only.
- Keep credits, seasons, videos, image collections, similar/recommended strands, and person filmography transient.

Server routes should validate inputs, normalize provider failures, and return typed responses the UI can render with graceful empty/error states.

## 6. Application Shell, Navigation, And Collection Home

Build the main shell with:

- Persistent filter/navigation panel.
- Main content region.
- Persistent Find/Discover entry.
- Persistent Settings entry.
- Responsive layout that works on desktop and mobile.
- Font-size/readability setting applied globally through theme tokens.

Collection Home features:

- Fetch the user's saved collection from Supabase.
- Apply selected sidebar filter plus media-type toggle.
- Group results into:
  - Active.
  - Excited (`later + excited`).
  - Interested (`later + interested`).
  - Other statuses, collapsed by default: Wait, Quit, Done, and Later without interest.
- Use larger/prominent tiles for Active.
- Show poster, title, in-collection badge, and user rating badge.
- Sidebar filters:
  - All Shows.
  - Tag filters, one per tag.
  - No tags when any saved show has no tags.
  - Genre filters.
  - Decade filters.
  - Community score ranges.
- Media-type toggle: All / Movies / TV, applied on top of all filters.
- Empty states:
  - No collection: prompt to Search/Ask.
  - Filter matches none: show "No results found."

Preserve `lastSelectedFilter` in disposable local UI state. Clearing local storage must not lose saved collection data.

## 7. Find/Discover Hub And Search

Build `FindDiscover` with a clear mode switcher for:

- Search.
- Ask.
- Alchemy.

Search mode:

- Text input by title/keyword.
- Live query through the catalog route with debouncing and loading state.
- Poster grid results.
- Overlay saved My Data and tile badges before render.
- Selecting a show opens Show Detail.
- Respect the `Search on Launch` setting by opening Search on first load when enabled.
- Keep Search tone straightforward with no AI voice.

Mode state should be shareable through URL query params where useful, without making chat/alchemy session state persistent.

## 8. Show Detail Page

Build Show Detail as the single source of truth for public facts, My Data, and discovery launch points.

Data loading:

- Fetch catalog details by media type and external ID.
- Fetch any saved overlay for `(namespace_id, user_id, show_id, show_type)`.
- Merge into a user-overlaid view model.
- When the show is saved, persist safe catalog refreshes using merge rules.

Preserve the narrative hierarchy:

1. Header media carousel with backdrops/posters/logos and trailers when available.
2. Core facts row: year, runtime or seasons/episodes, languages, and community score.
3. My relationship controls in the toolbar, not buried in the scroll body.
4. Tag chips.
5. Overview plus Scoop toggle/stream.
6. "Ask about this show" CTA.
7. Genres and languages.
8. Traditional similar/recommended strand.
9. Explore Similar.
10. Streaming availability.
11. Cast and crew.
12. Seasons for TV.
13. Budget vs revenue for movies.

Relationship controls:

- Toolbar chips: Active, Interested, Excited, Done, Quit, Wait.
- Interested/Excited map to `later + interest`.
- Status setting saves immediately.
- Reselecting the active status asks for destructive confirmation unless the user has suppressed it after repeated removals.
- My Rating bar saves an unsaved item as Done.
- Tags picker saves an unsaved item as Later + Interested.
- All updates write their corresponding timestamp.

Scoop:

- Button copy:
  - No scoop: "Give me the scoop!"
  - Cached scoop: "Show the scoop"
  - Open section title: "The Scoop"
- Stream progressively when supported.
- Freshness expires after about 4 hours.
- Persist generated Scoop only if the show is already in the collection; otherwise keep it ephemeral until saved.
- Preserve the product voice: friendly, opinionated, spoiler-safe, honest about mixed reception, 150-350 words, with the Scoop paragraph as the emotional center.

Explore Similar is planned separately in the AI/concepts section but appears on this page.

Critical UI states:

- Unsaved show.
- No trailers/backdrops/logos.
- No concepts generated.
- TV vs movie facts and sections.
- Catalog failures and provider-empty states.

## 9. AI Provider Layer, Prompt Contracts, And Guardrails

Create an AI server module with:

- Provider adapter for chat/completions and streaming where available.
- Shared persona instructions used by Scoop, Ask, Alchemy, and Explore Similar.
- Surface-specific prompt builders and schema parsers.
- Retry-on-parse-failure support for structured outputs.
- Input context builders that include only relevant user library data.
- Basic rate/error handling and user-visible fallback states.

Shared AI rules:

- Stay within TV/movies and redirect back when needed.
- Spoiler-safe by default unless the user explicitly asks for spoilers.
- Opinionated and honest; do not overpraise weak or mixed titles.
- Specific vibe/structure/craft reasoning over generic genre boilerplate.
- Recommendations should be actionable and map to real catalog items.
- Voice should feel like one consistent "fun, chatty TV/movie nerd friend" across AI surfaces.

Recommendation resolution:

- AI recommendation outputs must include title, external ID when available, media type, and reason.
- Resolve by external ID and media type first.
- Accept only when catalog title matches case-insensitively or through a clearly equivalent title alias if the provider exposes one.
- If unresolved, display as non-interactive or offer handoff to Search.
- Store AI recommendation reasons only in session state.

Testing:

- Golden prompt fixtures for structure and parser behavior.
- Unit tests for the exact Ask `showList` string parser.
- Mock AI responses for Scoop, concepts, Ask mentions, and concept recommendations.
- Human/regression checklist using the discovery quality rubric: voice, taste alignment, surprise, specificity, and real-show integrity.

## 10. Ask Chat

Ask mode features:

- Welcome view with 6 random starter prompts and a refresh action.
- Chat UI with user and assistant turns.
- General Ask launched from Find.
- Ask About a Show launched from Show Detail with seeded show context.
- Assistant responses are brisk by default and use bullets for multiple recommendations.
- Mentioned shows row renders machine-readable show references from the current conversation.
- Selecting a mentioned show opens Detail when resolved, or hands off to Search when not resolved.

Session behavior:

- Keep chat history in memory for the active session only.
- Clear history when leaving/resetting Ask.
- After about 10 messages, summarize older turns into 1-2 sentences while preserving the chat persona.
- Include user library/My Data in context so recommendations are taste-aware.

Structured mention contract:

- AI route returns:
  - `commentary`: user-facing text with no external IDs shown.
  - `showList`: `Title::externalId::mediaType;;Title2::externalId::mediaType;;...`
- Parser must exactly match the format.
- On parsing failure, retry once with stricter formatting.
- If still failing, show commentary and use Search handoff for unresolved titles.

## 11. Concepts, Explore Similar, And Alchemy

Implement the concept system as a shared feature used by Show Detail and Alchemy.

Concept generation:

- Generate 8 concepts by default.
- Concepts are 1-3 words, bullet-list style, evocative, spoiler-free, and not generic.
- Single-show concepts capture the show's core taste ingredients.
- Multi-show concepts must be shared across all selected inputs.
- Order strongest "aha" concepts first and vary axes: structure, tone, emotion, relationships, craft, and genre flavor.

Selection rules:

- Render concepts as selectable chips.
- Require at least 1 selected concept before recommendations.
- Cap selection at 8.
- Unselecting/changing concepts clears downstream recommendations.
- Copy should nudge the user to pick the ingredients they want more of.

Explore Similar:

- Lives on Show Detail.
- Flow: Get Concepts -> select concepts -> Explore Shows.
- Return 5 recommendations per round.
- Each reason explicitly names selected concepts and explains the fit.
- Resolved recommendations render as selectable show tiles with transient reason text.
- Saved overlays appear on recommendation tiles.

Alchemy:

- Lives in Find/Discover.
- Flow:
  1. Select 2 or more starting shows from library and global catalog search.
  2. Tap Conceptualize Shows.
  3. Select up to 8 shared concepts.
  4. Tap ALCHEMIZE!
  5. Review 6 recommendations.
  6. Optionally choose More Alchemy! to chain results as new inputs.
- Changing selected shows clears concepts and results.
- Changing selected concepts clears results.
- Recommendations must be real catalog items when possible and include short, specific reasons.
- Alchemy sessions are not persisted.

Discovery quality acceptance:

- Voice feels warm, playful, opinionated, and spoiler-safe.
- Reasons cite specific shared ingredients.
- At least 1-2 recommendations per result set can be surprising but defensible.
- Real-show integrity is non-negotiable.

## 12. Person Detail

Build Person Detail reachable from cast/crew strands:

- Fetch person profile, image gallery, bio, and credits from the catalog provider.
- Group filmography/credits by year.
- Selecting a credit opens Show Detail.
- Add lightweight analytics:
  - Average project ratings over time or by category where data is available.
  - Top genres.
  - Projects by year.
- Use graceful fallbacks for missing biography, profile images, or sparse credits.

Person data is catalog/transient and does not need user-owned persistence.

## 13. Settings, Preferences, And Data Export

Settings sections:

- App:
  - Font size/readability.
  - Search on Launch.
- User:
  - Username, synced in `cloud_settings`.
- AI:
  - AI model selection, synced in `cloud_settings`.
  - Provider key status. In benchmark mode, prefer environment variables. Optional user-entered key storage must be encrypted/server-handled and never committed.
- Integrations:
  - Catalog provider key status. Environment variable is the benchmark default.
- Your Data:
  - Export My Data.
  - Import/Restore is acknowledged as desired/open, but not required for the first implementation unless time allows.

Export:

- Generate a `.zip` containing JSON backup.
- Include saved shows and all My Data: status, interest, tags, rating, AI Scoop, and update timestamps.
- Include schema/data model version and export metadata.
- Encode dates as ISO-8601.
- Exclude secrets, API keys, transient chat history, Alchemy results, and disposable local UI state unless explicitly useful.

## 14. Migrations, Data Continuity, And Conflict Handling

Data continuity requirements:

- Every migration must preserve saved shows, ratings, tags, statuses, interest, and AI Scoop.
- Include `data_model_version` in app metadata.
- Add migration tests or fixtures that exercise upgrading an older snapshot to the current model.
- Ensure export format is versioned so future import/restore can map old data safely.

Conflict handling:

- User fields resolve by per-field update timestamp.
- Settings resolve by `version` epoch seconds.
- Duplicate show rows should be detected by `(namespace_id, user_id, show_id, show_type)` and merged without user disruption.
- Catalog refresh must never erase user overlay data.

## 15. Testing And Quality Strategy

Automated tests:

- Unit tests:
  - Save/default/removal rules.
  - Merge rules and timestamp conflict resolution.
  - Filter/grouping logic.
  - Show overlay view-model generation.
  - AI structured parsers.
  - Concept selection state resets.
  - Export serialization.
- Integration tests:
  - Supabase CRUD scoped by namespace and user.
  - `test:reset` deletes only the target namespace/test data.
  - Catalog route mapping and fallback handling with mocked provider responses.
  - AI routes with mocked provider responses and retry behavior.
- E2E tests:
  - Build collection from Search.
  - Rate-to-save defaults to Done.
  - Tag-to-save defaults to Later + Interested.
  - Browse Home by status, tag, media type, genre, decade, and score.
  - Remove a show through status confirmation.
  - Ask returns mentioned shows and opens Detail.
  - Scoop generates, caches, expires, and persists only when saved.
  - Explore Similar returns 5 real mapped shows.
  - Alchemy returns 6 real mapped shows and supports chaining.
  - Export creates a valid zip/JSON.

Manual/visual checks:

- Responsive shell, sidebar, grids, detail page, chat, and Alchemy flow.
- Accessibility for keyboard navigation, focus states, labels, color contrast, and reduced motion.
- Discovery quality scored against the rubric in `discovery_quality_bar.md`.
- Confirm clearing browser local storage does not lose saved collection data.

Required CI checks:

- `npm run lint`
- `npm run typecheck`
- `npm test`
- `npm run build`
- `npm run test:e2e` where secrets/test services are configured.

## 16. Implementation Milestones

Milestone 0: Project foundation

- Initialize Next.js/TypeScript app structure.
- Add theme tokens, linting, formatting, test framework, Playwright, env docs, scripts, and README setup.
- Add Supabase client factories for browser-safe and server-only contexts.

Milestone 1: Persistence and identity

- Add Supabase migrations.
- Implement namespace/user resolver.
- Add dev identity injection via `X-User-Id` only when enabled.
- Add collection repository with mandatory namespace/user filters.
- Add namespace-scoped `test:reset`.

Milestone 2: Domain logic

- Implement show types, status/interest constants, save rules, merge rules, filters, grouping, and overlay view models.
- Add focused unit tests before connecting UI.

Milestone 3: Catalog adapter

- Implement search, detail, credits, providers, similar/recommendations, person detail, and person credits behind server routes.
- Add provider response mapping and mocked integration tests.

Milestone 4: Shell and Collection Home

- Build navigation shell, filters/sidebar, media toggle, status grouping, collection tiles, and empty states.
- Connect Home to Supabase and overlay view models.

Milestone 5: Find/Search

- Build Find/Discover mode shell and Search mode.
- Add auto-search-on-launch behavior.
- Ensure saved overlays appear in search results.

Milestone 6: Show Detail and My Data

- Build detail page sections in the specified hierarchy.
- Implement status, interest, rating, tags, removal confirmation, and persistence.
- Add traditional recommendations, provider availability, cast/crew, seasons, and movie finance sections.

Milestone 7: AI foundation and Scoop

- Implement AI provider adapter, shared voice rules, prompt builders, streaming support, parsing utilities, and errors.
- Add Scoop generation, 4-hour cache freshness, unsaved ephemeral behavior, and saved persistence.

Milestone 8: Ask

- Build Ask chat UI, starter prompts, session memory, summarization, Ask About a Show context handoff, structured mentioned shows, and resolution to real catalog items.

Milestone 9: Concepts, Explore Similar, and Alchemy

- Implement concept generation and shared selection state.
- Build Explore Similar on Detail with 5 recommendations.
- Build Alchemy with 2+ input selection, concept generation, 6 recommendations, clearing rules, and chaining.

Milestone 10: Person, Settings, and Export

- Build Person Detail and analytics.
- Build Settings sections and synced cloud settings.
- Implement Export My Data zip/JSON.

Milestone 11: Hardening and release readiness

- Complete e2e tests, visual/accessibility pass, discovery quality review, migration tests, namespace reset validation, and README verification.
- Confirm no source code requires environment-specific edits and no secrets are committed.

## 17. Risks And Mitigations

- AI hallucinated or mismapped recommendations: require external IDs where possible, verify title/media type against catalog, and fall back to non-interactive/Search handoff.
- Provider outages or rate limits: isolate provider calls, cache only safe saved metadata, show graceful errors, and mock providers in tests.
- Namespace leakage: centralize identity resolution and repository filters; add tests that create two namespaces and verify isolation.
- User data loss during catalog refresh: keep merge rules in a tested domain module and never let UI write raw partial show objects directly.
- Development identity becoming production auth: gate header injection behind environment flags and document the OAuth replacement path.
- AI voice drift: centralize shared persona rules and run the discovery rubric against fixed scenarios before release.
- Overwhelming Detail page: preserve narrative hierarchy, keep primary relationship controls early, and push long-tail facts lower.

## 18. Definition Of Done

The implementation is complete when:

- The app runs with `npm run dev` after filling `.env`.
- Supabase schema can be created from migrations.
- All user-owned persisted rows include `namespace_id` and `user_id`.
- Two namespaces cannot read/write/reset each other's data.
- Saved shows preserve My Data everywhere they appear.
- Collection Home, Search, Ask, Alchemy, Show Detail, Person Detail, Settings, and Export are functional.
- AI Scoop, Ask mentions, concepts, Explore Similar, and Alchemy follow the voice, structure, counts, cache, and real-show mapping contracts.
- Clearing local storage does not delete saved user data.
- `npm run lint`, `npm run typecheck`, `npm test`, and `npm run build` pass.
- E2E coverage exercises the key user journeys from the PRD.
