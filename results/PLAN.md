# Implementation Plan

## 1. Product Readout

This product is a personal TV/movie companion with two equally important halves:

1. A durable personal library built around each user's version of a show.
2. Discovery flows that are grounded in that library rather than generic recommendations.

The implementation must preserve these non-negotiables:

- The user-overlaid version of a show wins everywhere once saved.
- Saved state is defined by status, with implicit save rules for rating and tagging.
- Discovery has three distinct modes: Search, Ask, and Alchemy, plus per-show Explore Similar.
- AI behavior must be spoiler-safe, opinionated, domain-limited to TV/movies, and consistent in voice.
- All user-owned persisted data must be partitioned by both `namespace_id` and `user_id`.
- The backend is the source of truth; client storage can improve UX but must be disposable.
- The repo must support environment-only setup, one-command dev/test/reset flows, and namespace-scoped destructive tests.

Planned scope choices based on the PRD:

- Implement `Next` in the data model only, not as a first-class UI status.
- Implement export/backup now; defer import/restore because the PRD labels it desired but not currently implemented.
- Treat AI Scoop on unsaved shows as ephemeral until the user saves the show.
- Keep Ask, mentioned shows, and Alchemy results session-scoped and non-persistent.

## 2. Architecture Strategy

### 2.1 Runtime and app structure

Use `Next.js` App Router for the runtime and request boundary, but keep route files thin. Route entries should mostly compose page-level features from `src/` so the codebase still follows the benchmark's fractal architecture guidance.

Recommended structure:

```text
app/                     # Thin route wrappers, layout, metadata, route handlers
src/
  config/                # Env parsing, constants, feature flags
  theme/                 # Tokens, typography scale, spacing, semantic colors
  components/            # Shared UI primitives
  hooks/                 # Cross-feature hooks
  lib/
    identity/            # namespace/user resolution
    supabase/            # clients, typed access, query helpers
    catalog/             # external catalog adapters + mapping
    ai/                  # provider abstraction, prompt builders, parsing
    export/              # zip/json backup generation
  server/
    repositories/        # DB reads/writes, always namespace/user scoped
    services/            # business workflows and orchestration
  pages/
    Home/
    Find/
    ShowDetail/
    PersonDetail/
    Settings/
```

Design rules:

- TSX stays focused on presentation and event wiring.
- Feature logic lives in hooks and services, not in page components.
- Shared domain rules such as save defaults, merge behavior, and AI parsing must exist once and be reused everywhere.
- Keep server-only code isolated from client components to avoid secret leakage and reduce accidental coupling.

### 2.2 Core subsystems

The build should be organized into six clear subsystems:

1. Identity and isolation.
2. Persistence and merge rules.
3. External catalog access.
4. AI orchestration.
5. Page/feature UI.
6. Testing and reset tooling.

Each subsystem should have a narrow API so future infra swaps do not force UI rewrites.

## 3. Data and Persistence Plan

### 3.1 Persistence model

Use Supabase as the source of truth. The simplest compliant model is a small set of user-owned tables plus optional helper views.

Recommended persisted tables:

1. `users`
2. `saved_shows`
3. `user_settings`

Optional support tables if needed for cleanliness or indexing:

4. `saved_show_tags` if array-based tags become awkward for filtering
5. `schema_migrations` or Supabase migration history artifacts

The first implementation should prefer fewer tables and clearer rules over premature normalization.

### 3.2 `users`

Purpose:

- represent the opaque stable user identity within a namespace
- support future migration to real OAuth without schema redesign

Core fields:

- `namespace_id`
- `user_id`
- `display_name`
- `created_at`
- `updated_at`

Primary key should include both `namespace_id` and `user_id`.

### 3.3 `saved_shows`

This is the core durable entity and should store both cached public metadata and user-owned overlay fields, matching the storage reference and merge behavior from the PRD.

Key columns:

- ownership and identity:
  - `namespace_id`
  - `user_id`
  - `show_id`
  - `media_type`
  - `external_ids` JSONB
- catalog fields:
  - title, overview, genres, tagline, homepage
  - language fields
  - poster/backdrop/logo URLs
  - community score and popularity
  - date fields
  - movie-specific facts
  - TV-specific facts
  - provider data blob
- user overlay:
  - `my_status`
  - `my_interest`
  - `my_tags`
  - `my_score`
  - field-specific update timestamps
- AI:
  - `ai_scoop`
  - `ai_scoop_update_date`
- management:
  - `details_update_date`
  - `creation_date`
  - `is_test`

Primary key:

- composite key on `namespace_id`, `user_id`, `show_id`

Important indexes:

- ownership queries: `namespace_id`, `user_id`
- home sorting/grouping: `my_status`, `my_interest`, update timestamps
- filtering: `genres`, `my_tags`, `media_type`, `vote_average`, decade-derived date ranges

### 3.4 `user_settings`

Store synced settings only:

- `namespace_id`
- `user_id`
- `user_name`
- `ai_model`
- optional `catalog_api_key`
- optional `ai_api_key`
- `version`
- `updated_at`

Local-only UI preferences such as font size, search-on-launch, removal-confirmation suppression, and last-selected filter should live client-side because the technical reference distinguishes them from synced cloud settings. The plan should still centralize access to them behind a client preferences module so a future sync decision is easy.

### 3.5 Business rules encoded in persistence

The persistence layer must enforce or consistently apply these rules:

- A show is in the collection iff `my_status` exists.
- Setting `Interested` or `Excited` maps to `my_status = later` plus `my_interest`.
- Rating an unsaved show creates a saved row with `my_status = done`.
- Tagging an unsaved show creates a saved row with `my_status = later` and `my_interest = interested`.
- Clearing status removes the entire saved show row and clears all My Data, including Scoop.
- Re-encountering a saved show merges fresh catalog data without losing newer user fields.
- My-field conflicts resolve by the most recent field-specific timestamp.
- Non-my catalog fields use "prefer new non-empty, otherwise keep existing" semantics.
- Scoop freshness expires after roughly 4 hours and regenerates on demand.

### 3.6 Namespace isolation and destructive reset

Every user-owned row must carry `namespace_id`. All test reset tooling should delete only records within the active namespace and must never require global database teardown.

Deliverables:

- migration files
- deterministic reset script
- seed strategy if any fixtures are needed for tests

## 4. Identity and Request Context

### 4.1 Namespace resolution

Use a stable namespace value from environment/config for the lifetime of the build. It should be injected into server-side repositories and never inferred from client state.

### 4.2 User resolution

Benchmark mode does not need real auth. The cleanest path is:

- default to a configured dev user
- optionally support a dev-only selector or request header override
- gate any override mechanism so it is clearly non-production

Every request reaching server actions or route handlers should resolve a request context object:

```ts
{
  namespaceId: string,
  userId: string,
  isDevIdentityMode: boolean
}
```

This context becomes mandatory input for repositories and services.

### 4.3 Future auth migration

Do not bake any provider-specific meaning into `user_id`. When real OAuth arrives later, the auth layer should map provider identities onto the existing `users` table and all data relationships should remain valid.

## 5. External Catalog and Domain Mapping

### 5.1 Catalog adapter

Create a dedicated catalog adapter layer so provider-specific payloads never leak into UI or repository code.

Responsibilities:

- search shows by query
- fetch show details
- fetch cast/crew, images, videos, recommendations, similar titles
- fetch person details and credits
- fetch provider availability
- map external payloads into a canonical domain DTO

### 5.2 Canonical show mapper

The mapper should produce one canonical show shape used everywhere in the app. This is critical because the same item appears in search results, AI recommendations, the library, the detail page, and person credits.

Rules:

- map external IDs when available
- normalize media types to movie/tv
- store genres as names, not provider IDs
- preserve transient detail-only fields outside persistence
- create a reusable merge function so all entry points apply the same overlay semantics

### 5.3 Recommendation resolution

AI recommendations must resolve to real catalog items. The resolution service should:

1. prefer external ID lookup
2. verify title match case-insensitively
3. fall back to deterministic search if needed
4. degrade to non-interactive text or Search handoff if resolution still fails

This logic should be shared by Ask, Explore Similar, and Alchemy.

## 6. AI System Plan

### 6.1 Provider abstraction

Implement an AI provider interface that separates:

- prompt/context assembly
- model invocation
- structured parsing and retries
- product-level guardrails

This prevents vendor lock-in and keeps personality logic above the provider boundary.

### 6.2 Shared AI inputs

All AI surfaces should pull from a common context builder capable of assembling:

- saved library with My Data
- current show context when applicable
- selected concepts
- recent conversation turns
- summarized older conversation turns

### 6.3 Surface contracts

Build each AI surface as a small orchestrator around shared infrastructure:

1. `Ask`
   - conversational response
   - structured mentioned-show output
   - session summary after long conversations
2. `Scoop`
   - spoiler-safe mini taste review
   - progressive streaming where feasible
   - cached by saved show for 4 hours
3. `Concept generation`
   - bullet list only
   - 1-3 words
   - specific, evocative, spoiler-free
   - default to 8 concepts
4. `Concept-based recommendations`
   - 5 recs for Explore Similar
   - 6 recs for Alchemy
   - each reason names the matching concept(s)

### 6.4 Parsing and fallback strategy

Because structured AI output is a product requirement, not a best effort, the plan should include:

- strict schema validation of AI outputs
- one automatic retry with stronger formatting instructions
- graceful UI fallback when parsing or resolution fails

### 6.5 Voice and quality enforcement

The AI system prompt stack should explicitly encode:

- TV/movie-only scope
- spoiler-safe defaults
- friend-forward but honest voice
- concrete vibe/craft reasoning
- no generic filler

This belongs in centralized prompt builders, not duplicated inline across components.

## 7. Feature-by-Feature UI Plan

### 7.1 App shell and navigation

Build a persistent shell with:

- left-side filters/navigation area
- main content area
- clear entry points to Find/Discover and Settings

Keep the route structure straightforward:

- `/` or `/home`
- `/find`
- `/shows/[id]`
- `/people/[id]`
- `/settings`

### 7.2 Collection Home

Purpose:

- present the personal library as a living map of the user's relationship to content

Implementation requirements:

- sidebar/filter system with All Shows, tag filters, No Tags when applicable, genre, decade, and community-score filters
- media-type toggle layered on top of any active filter
- grouped sections in this order:
  - Active
  - Excited
  - Interested
  - collapsed Other statuses
- active titles rendered more prominently
- saved/rating badges on tiles
- clear empty states for no library and no filter matches

Key dependencies:

- saved show repository
- filter derivation layer
- tile overlay badges shared with search and recommendations

### 7.3 Search

Purpose:

- fast global catalog search without AI voice

Requirements:

- debounced live search
- poster grid results
- clear saved-state indicators
- direct navigation to detail
- respect search-on-launch preference from settings

### 7.4 Show Detail

This page is the highest-density feature and should be treated as a composition of sub-features rather than one large file.

Required section order:

1. header media
2. core facts and community score
3. tags / relationship controls
4. overview and Scoop
5. Ask-about-this-show CTA
6. genres and languages
7. traditional recommendations
8. Explore Similar
9. providers
10. cast and crew
11. seasons for TV
12. budget/revenue for movies

Critical interaction rules:

- status chips live in the toolbar or other early high-visibility placement
- reselecting the same status triggers removal confirmation
- rating unsaved show auto-saves as Done
- tagging unsaved show auto-saves as Later + Interested
- Scoop can be generated for unsaved shows but only persists if the show is saved
- Ask-about-this-show must enter Ask with seeded show context

### 7.5 Ask

Requirements:

- conversational UI with welcome state
- 6 random starter prompts with refresh
- structured mentioned-shows row
- direct-answer-first responses
- session memory with summarization after roughly 10 messages
- seeded variant when launched from detail

State strategy:

- keep active conversation in client/session state
- send summarized history to the AI service as conversations grow
- clear state when user resets or leaves the flow

### 7.6 Explore Similar

Requirements:

- visible from detail page
- fetch concepts on demand
- concept chips selectable up to the shared cap
- selecting/unselecting concepts clears downstream recs
- `Explore Shows` yields 5 real-show recommendations

### 7.7 Alchemy

Requirements:

- choose 2 or more starting shows from both library and global catalog
- concept generation uses only shared/common ingredients across all inputs
- allow selecting up to 8 concepts
- generate 6 recommendations with concise concept-based reasons
- support chaining via More Alchemy
- if seed inputs change, clear concepts/results downstream

### 7.8 Person Detail

Requirements:

- hero/profile info
- bio and image gallery
- filmography grouped by year
- lightweight analytics:
  - average user ratings across credits when available
  - top genres
  - projects by year
- show credits navigate back to show detail

The analytics should degrade gracefully when the user has little or no rating history.

### 7.9 Settings and Your Data

Required settings:

- font size/readability
- search on launch
- username
- AI model selection
- optional AI API key
- optional catalog API key

Required data tooling:

- Export My Data as a zip containing JSON backup
- ISO-8601 date encoding

Planned deferral:

- import/restore UI and workflows

## 8. Shared Frontend and Service Boundaries

### 8.1 Shared UI primitives

Create a reusable component layer early for:

- poster/show tiles
- status chips
- tag chips and pickers
- section headers and strands
- loading/empty/error states
- dialog/confirmation patterns
- rating controls

This reduces duplication across Home, Search, Detail, Ask recommendations, Alchemy, and Person credits.

### 8.2 Service boundaries

Recommended services:

- `LibraryService`
- `ShowDetailsService`
- `CatalogSearchService`
- `AskService`
- `ScoopService`
- `ConceptService`
- `RecommendationResolutionService`
- `ExportService`

Repositories should remain simple data access helpers; cross-step workflows belong in services.

## 9. Delivery Phases

### Phase 0: Foundation

Deliverables:

- Next.js project scaffolding cleanup
- folder structure aligned with benchmark instructions
- theme tokens, global layout shell, env parsing, `.env.example`, `.gitignore` validation
- Supabase client setup

Exit criteria:

- app boots with config-only setup
- no secrets in source
- architecture skeleton exists without feature logic tangled into routes

### Phase 1: Identity, schema, and reset tooling

Deliverables:

- migrations for `users`, `saved_shows`, `user_settings`
- request context resolution for `namespace_id` and `user_id`
- namespace-scoped reset script and fixtures if needed

Exit criteria:

- destructive reset affects only the active namespace
- all repository APIs require namespace/user scope

### Phase 2: Catalog integration and canonical mapping

Deliverables:

- search/detail/person/provider adapters
- canonical DTOs
- merge logic for catalog refresh + My Data preservation

Exit criteria:

- same show shape can drive Search, Detail, Home overlays, and AI recommendation mapping

### Phase 3: Library flows

Deliverables:

- save/update/remove workflows
- filter derivation logic
- Home page with grouped status sections and badges
- persistent local UI preferences

Exit criteria:

- all implicit save behaviors work
- clearing status removes the full saved row
- filters accurately reflect current library contents

### Phase 4: Detail page and person exploration

Deliverables:

- detail sub-features
- toolbar status controls, rating, tags
- traditional recommendations, providers, cast/crew, seasons, financials
- person detail with analytics and filmography

Exit criteria:

- detail page acts as the single source of truth and discovery launchpad

### Phase 5: AI foundation and Ask

Deliverables:

- AI provider abstraction
- prompt builders and structured parsers
- Ask UI, starter prompts, mentioned-shows rail, summarization
- Ask-about-this-show seeded handoff

Exit criteria:

- Ask feels conversational and consistently on-brand
- mentioned shows reliably map to real catalog items or degrade gracefully

### Phase 6: Scoop, Explore Similar, and Alchemy

Deliverables:

- Scoop generation, caching, persistence rules
- concept generation UI and services
- Explore Similar rec flow
- Alchemy multi-seed flow and chaining

Exit criteria:

- concepts are specific and usable
- recommendation reasons explicitly reflect selected concepts
- result counts match the spec

### Phase 7: Settings, export, and polish

Deliverables:

- settings screens and persistence
- export zip generation
- loading/error/empty-state refinement
- accessibility and responsive polish

Exit criteria:

- users can configure the app and export their data without touching the database directly

### Phase 8: Quality hardening

Deliverables:

- test coverage across domain rules and critical journeys
- AI quality checks
- visual verification of dense pages
- performance cleanup and rate-limit protections

Exit criteria:

- benchmark success criteria and core user journeys are covered by automated or structured manual validation

## 10. Testing Strategy

### 10.1 Unit tests

Highest-value pure logic to cover first:

- save-default rules
- status removal behavior
- merge conflict resolution by timestamp
- catalog-field merge semantics
- filter derivation and grouping
- concept selection caps
- AI parser validation and retry behavior
- export serialization and ISO date formatting

### 10.2 Integration tests

Cover:

- repository behavior against Supabase
- namespace/user scoping
- reset script correctness
- server actions/route handlers for save/rate/tag/remove
- recommendation resolution pipeline

### 10.3 End-to-end tests

Critical user journeys from the PRD:

1. build collection from search
2. rate-to-save
3. tag-to-save
4. maintain collection from detail
5. tag-driven organization
6. Ask discovery to save
7. Explore Similar to save
8. Alchemy round and chained round
9. person deep-dive
10. export backup

### 10.4 AI quality validation

Because AI quality is part of the product, use a small golden-set style review harness for:

- voice adherence
- taste alignment
- surprise without betrayal
- specificity of reasoning
- real-show integrity

Real-show integrity should be treated as non-negotiable.

### 10.5 Visual and UX checks

Prefer visual regression coverage for:

- Home grouping and filter states
- Show Detail section ordering and dense layouts
- Ask conversation layout + mentioned shows
- Alchemy step transitions

## 11. Risks and Mitigations

### Risk 1: Overlay consistency drifts across surfaces

Mitigation:

- one canonical show presenter/mapper
- one shared save/update/remove service
- no feature-specific copies of merge logic

### Risk 2: AI structured output is brittle

Mitigation:

- schema validation
- one retry path
- graceful fallback UI
- test fixtures for parsing edge cases

### Risk 3: Catalog and AI rate limits hurt UX

Mitigation:

- debounce search
- cache short-lived safe responses where appropriate
- stream long AI responses
- keep provider access behind service abstractions for future tuning

### Risk 4: Detail page becomes monolithic

Mitigation:

- build it as a page shell with isolated sub-features
- move logic into hooks and services
- keep section components small and co-located

### Risk 5: Namespace leakage in tests or manual use

Mitigation:

- require namespace in repository constructors
- central request-context helper
- reset tooling that refuses to run without explicit namespace config

## 12. Final Implementation Priorities

If tradeoffs are required, prioritize in this order:

1. Correct persistence, save semantics, and namespace/user isolation.
2. Library and detail flows, because they define the product's core truth.
3. AI contracts and recommendation integrity.
4. Discovery polish and richer secondary surfaces.
5. Optional extensions and deferred features.

The product will only feel correct if the saved-library model, overlay precedence, and real-show AI grounding are solid first. Everything else should build on that foundation rather than work around it.
