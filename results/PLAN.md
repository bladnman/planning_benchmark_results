# Implementation Plan

## 1. Scope and Delivery Goal

Build a benchmark-ready personal TV/movie companion that supports:

- Personal collection management with status, interest, tags, rating, and optional AI Scoop
- Discovery through Search, Ask, Alchemy, and per-show Explore Similar
- Detail and Person pages with catalog metadata and user overlay data
- Server-side persistence with namespace and user isolation
- Export/backup of saved user data

This is a planning-only deliverable. The implementation should cover the required product behaviors from `docs/prd/` and intentionally defer the PRD's optional extensions unless noted:

- Defer first-class `Next` status UI
- Defer import/restore
- Defer saved/shareable Alchemy sessions
- Do not target offline-first behavior

## 2. Core Product Decisions

### 2.1 Runtime and platform

- Use `Next.js` latest stable with the App Router for routing, server rendering, route handlers, and streaming AI responses.
- Use `TypeScript` across app, server, tests, and schema utilities.
- Use `Supabase` via official client libraries for persistence.
- Treat the backend as the source of truth; client cache is disposable.

### 2.2 Architecture style

Use the repo's fractal architecture guidance:

- `app/` contains route entrypoints only.
- `src/pages/...` contains page implementations.
- Each page owns `features/`, and each feature may own nested `features/`, `hooks/`, `utils/`, and tests.
- TSX files stay humble: markup and binding only. Business logic lives in hooks/services.
- Theme tokens and shared styling live in `src/theme`; no inline styles or magic numbers in TSX.

### 2.3 External dependency boundaries

Abstract both external systems behind adapters:

- `CatalogService`: search, details, people, providers, recommendations, image/logo normalization, and external-ID resolution
- `AiService`: Ask, Scoop, Concepts, and Concept Recommendations

This keeps the app compliant with the technology-agnostic PRD while still choosing concrete providers for the benchmark build.

## 3. High-Level System Architecture

### 3.1 Route map

- `/` -> Collection Home
- `/find` -> Find hub with Search / Ask / Alchemy mode switcher
- `/show/[showId]` -> Show Detail
- `/person/[personId]` -> Person Detail
- `/settings` -> Settings and export
- `/api/...` or server actions -> mutation, AI, export, and reset endpoints

### 3.2 Layering

1. UI layer
   - App Router pages import page modules from `src/pages/...`
   - Shared components for tiles, chips, drawers, toolbars, carousels, chat bubbles, and charts

2. Application layer
   - Feature hooks orchestrate fetching, optimistic updates, and transient session state
   - Use case services implement save/remove/merge/filter/export/AI workflows

3. Data layer
   - Repository layer encapsulates Supabase queries and namespace/user scoping
   - Catalog adapter normalizes external payloads into internal `Show` shape
   - AI adapter enforces structured output and retry/fallback behavior

### 3.3 Recommended directory layout

```text
app/
  layout.tsx
  page.tsx
  find/page.tsx
  show/[showId]/page.tsx
  person/[personId]/page.tsx
  settings/page.tsx
  api/
    ask/route.ts
    scoop/route.ts
    concepts/route.ts
    concept-recommendations/route.ts
    export/route.ts
    test/reset/route.ts

src/
  config/
  theme/
  components/
  hooks/
  utils/
  lib/
    auth/
    namespace/
    supabase/
    catalog/
    ai/
    repositories/
    services/
    schemas/
  pages/
    Home/
      Home.tsx
      features/
    Find/
      Find.tsx
      features/
        SearchMode/
        AskMode/
        AlchemyMode/
    ShowDetail/
      ShowDetail.tsx
      features/
        HeaderMedia/
        RelationshipToolbar/
        OverviewAndScoop/
        TraditionalRecommendations/
        ExploreSimilar/
        Providers/
        CastAndCrew/
        Seasons/
        MovieFinance/
    PersonDetail/
      PersonDetail.tsx
      features/
    Settings/
      Settings.tsx
      features/
```

## 4. Persistence and Data Model Plan

### 4.1 Persistence strategy

Persist only data that must survive reloads/devices:

- Saved shows with catalog snapshot + user overlay + persisted AI Scoop
- User/cloud settings
- UI preferences that are explicitly persistent
- Data model metadata

Keep session-only AI state out of persistent storage:

- Ask chat history
- Ask mentioned-shows strip
- Alchemy concepts/results
- Explore Similar concept selections/results
- Unsaved-show Scoop text

### 4.2 Primary tables

Use relational tables keyed by namespace and user. Recommended schema:

1. `users`
   - `namespace_id`
   - `user_id`
   - `user_name`
   - timestamps

2. `saved_shows`
   - composite key: `(namespace_id, user_id, show_id)`
   - public catalog columns mirroring the storage spec where filtering/sorting is needed
   - `external_ids jsonb`
   - `provider_data jsonb`
   - `genres text[]`
   - `spoken_languages text[]`
   - `languages text[]`
   - `episode_run_time int[]`
   - `my_tags text[]`
   - `my_score`, `my_status`, `my_interest`
   - `ai_scoop`
   - all per-field update timestamps required by the PRD
   - `details_update_date`
   - `creation_date`
   - `is_test`

3. `user_settings`
   - `(namespace_id, user_id)`
   - `font_size`
   - `auto_search`
   - `hide_status_removal_confirmation`
   - `status_removal_count`
   - `last_selected_filter jsonb`
   - `ai_model`

4. `app_metadata`
   - `(namespace_id)`
   - `data_model_version`

### 4.3 Why this schema

- It preserves the reference storage model without requiring an exact one-table clone.
- It keeps frequently queried/filterable data first-class instead of buried in JSON.
- It supports field-level timestamps for conflict resolution.
- It keeps namespace/user isolation explicit in every user-owned row.

### 4.4 Save and removal rules

Implement these use cases as server-side domain functions:

- `setStatus(show, status)`
- `setInterest(show, interest)` -> auto-sets `Later`
- `setRating(show, rating)` -> auto-saves unsaved show as `Done`
- `setTags(show, tags)` -> auto-saves unsaved show as `Later + Interested`
- `clearStatus(show)` -> confirm, then delete saved record and all My Data

Removal behavior must delete the saved show record entirely, because the spec defines collection membership by presence of status and requires clearing all My Data.

### 4.5 Merge policy

When catalog data is refreshed for an already-saved show:

- Public fields: `selectFirstNonEmpty(newValue, oldValue)`
- User fields: newer timestamp wins per field
- `creation_date` never changes after first save
- `details_update_date` updates on each successful refresh

This merge logic should live in a pure utility with unit tests because it is central to sync integrity and overlay correctness.

## 5. Identity, Namespace, and Security Plan

### 5.1 Namespace model

Every request resolves a stable `namespace_id` from environment/config for the current build. All persistent reads and writes include it.

Required environment:

- `APP_NAMESPACE_ID`
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY` for server-only admin/reset paths
- provider/API settings for catalog and AI

### 5.2 User identity model

For benchmark mode, support development identity injection:

- Server resolves `user_id` from a gated `X-User-Id` header when enabled
- Fallback to `DEV_DEFAULT_USER_ID` when no header is supplied
- Identity injection is disabled in production mode

All user-owned records use `(namespace_id, user_id)` as the effective partition.

### 5.3 Security and migration path

- Browser code never gets elevated keys.
- All privileged writes, AI calls, export generation, and destructive resets stay server-side.
- The auth boundary is abstracted behind a `getRequestIdentity()` function so real OAuth can replace dev identity injection later without a schema redesign.

## 6. Catalog and Data Fetching Plan

### 6.1 Catalog provider responsibilities

The catalog adapter must support:

- text search
- show detail fetch
- person detail fetch
- credits
- seasons
- videos/images
- providers by region
- traditional similar/recommended titles
- external ID lookup and reverse resolution

### 6.2 Internal show normalization

Normalize catalog payloads into one internal `Show` domain shape consistent with the technical schema docs.

Important mapping behavior:

- infer media type carefully when the upstream payload is ambiguous
- reject unknown/no-title payloads
- map genre IDs to names
- parse date fields defensively
- choose a deterministic best logo
- persist provider IDs only, not full provider objects

### 6.3 Overlay everywhere

Any show shown in:

- Home
- Search results
- AI mentioned rows
- AI recommendations
- detail-page recommendation strands
- person credits

must be passed through an overlay function that merges saved user data onto the latest catalog payload before rendering.

## 7. AI System Plan

### 7.1 Shared AI rules

Build one shared AI prompt/context layer that enforces:

- TV/movie domain only
- spoiler-safe by default
- warm, opinionated, specific voice
- actionable recommendations tied to real catalog items

### 7.2 Ask

Implement Ask as a session-scoped chat surface with:

- conversation turns kept in client/session state
- summarization after roughly 10 messages
- structured response contract:
  - `commentary`
  - `showList` in the exact `Title::externalId::mediaType;;...` format
- one retry on parsing failure, then fallback to commentary + search handoff
- starter prompt carousel with 6 random prompts and refresh
- seeded show context for "Ask about this show"

### 7.3 Scoop

Implement Scoop as an on-demand server-streamed generation:

- button copy changes by state: generate, show cached, open
- progressive streaming instead of blank waiting
- 4-hour freshness window
- persist Scoop only when the show is already saved
- for unsaved shows, hold Scoop in transient UI state only

### 7.4 Concepts

Support two concept-generation modes:

- single-show Explore Similar
- multi-show Alchemy

Rules:

- bullet list only
- 1-3 word concepts
- 8 concepts by default
- specific and diverse across axes
- shared across all shows in multi-show mode

### 7.5 Concept recommendations

Implement a resolver pipeline:

1. AI returns title + external ID + media type + reason
2. server resolves against the catalog
3. first case-insensitive title match for the supplied external ID wins
4. matched results become real selectable shows
5. unmatched results become non-interactive or route users to Search

Counts:

- Explore Similar -> 5 recommendations
- Alchemy -> 6 recommendations

### 7.6 AI provider abstraction

Use strict schema validation for all AI outputs. The provider interface should allow swapping models/providers later while keeping the same surface contracts.

## 8. Page and Feature Implementation Plan

### 8.1 Collection Home

Build Home around user library organization:

- left navigation/filter panel
- main content grouped by status sections in required order
- media-type toggle at top
- dynamic tag filters, `No tags`, genre, decade, and community-score filters
- empty states for empty library and empty filter result
- larger/prominent tiles for `Active`

Key feature modules:

- `FilterSidebar`
- `MediaTypeToggle`
- `StatusSectionList`
- `ShowTile`

### 8.2 Find Hub

Use a single page with a clear mode switcher:

1. Search mode
   - live text search
   - poster grid results
   - in-collection badges

2. Ask mode
   - chat transcript
   - starter prompts
   - mentioned-shows strip
   - session reset

3. Alchemy mode
   - input show picker
   - concept generation
   - concept selection up to 8
   - recommendations + reasons
   - chainable "More Alchemy!" flow

### 8.3 Show Detail

Preserve the intended narrative order:

1. Header media carousel
2. Core facts + community score
3. Tags
4. Overview + Scoop
5. Ask-about-this-show CTA
6. Genres + languages
7. Traditional recommendations
8. Explore Similar
9. Providers
10. Cast and Crew
11. Seasons for TV
12. Budget/Revenue for movies

Relationship controls must remain fast and high in priority:

- toolbar status chips with `Interested`/`Excited` mapping to `Later + Interest`
- rating control with rate-to-save behavior
- tag editing with tag-to-save behavior
- destructive status reselection confirmation with remember-my-choice support

### 8.4 Person Detail

Implement:

- hero with image/name/bio
- analytics summaries/charts:
  - average project ratings
  - top genres
  - projects by year
- filmography grouped by year
- credit tiles opening Show Detail

### 8.5 Settings and Export

Implement:

- font size
- search-on-launch
- username
- AI model selection
- catalog/AI key display or configuration guidance via environment in benchmark mode
- export button that generates a `.zip` containing JSON backup with ISO-8601 dates

Import/restore remains deferred.

## 9. State Management and Interaction Design

### 9.1 Server state

Use a server-state library or equivalent request cache for:

- saved library
- search results
- show/person details
- recommendation strands
- providers

Cache invalidation rules must aggressively refresh any place a show may appear after mutations so the user overlay remains consistent across the app.

### 9.2 Client session state

Keep transient state local to the relevant page/feature:

- Ask chat transcript and summarized context
- Ask mentioned shows
- Explore Similar selected concepts and results
- Alchemy selected inputs, concepts, and chained results
- unsaved Scoop text

### 9.3 Optimistic behavior

Use optimistic UI for:

- status changes
- tag edits
- ratings

but reconcile against the server response so timestamps and overlay data remain authoritative.

## 10. Testing and Quality Plan

### 10.1 Unit tests

Prioritize pure logic with adjacent tests:

- merge policy
- save defaults
- status removal semantics
- filter derivation
- overlay resolution
- concept selection clearing behavior
- AI structured-output parsing and retry
- recommendation resolution rules

### 10.2 Integration tests

Exercise repositories and server handlers against a test namespace:

- CRUD on saved shows
- user settings persistence
- export payload generation
- namespace isolation
- user isolation inside one namespace
- test reset behavior

### 10.3 End-to-end tests

Use Playwright for the main journeys:

1. Search -> save via Interested/Excited/Active
2. Search -> rate-to-save as Done
3. Search -> tag-to-save
4. Home filter and status grouping
5. Ask -> open mentioned show -> save
6. Detail -> Scoop generation
7. Detail -> Explore Similar -> save a result
8. Find -> Alchemy -> chain another round
9. Person detail -> open a credit
10. Settings -> export

### 10.4 Visual protection

Because the PRD is layout-sensitive, add targeted visual tests or snapshot coverage for:

- Home grouped sections
- Find mode switcher
- Detail header + relationship toolbar
- Explore Similar concept chips
- Ask transcript + mentioned row

### 10.5 AI quality validation

Add contract tests with mocked AI responses plus optional live smoke tests behind env flags for:

- Ask structured format
- Scoop section shape and freshness behavior
- Concepts count/format
- recommendation counts and real-show mapping

## 11. Developer Experience and Repo Deliverables

Required repo artifacts:

- `.env.example` with every required variable and short comments
- `.gitignore` excluding secrets while keeping `.env.example`
- migrations for deterministic schema creation
- scripts for:
  - starting the app
  - running tests
  - resetting namespace-scoped test data

Recommended script shape:

- `npm run dev`
- `npm run test`
- `npm run test:reset`

The reset path should delete only rows for the provided namespace, never globally tear down the database.

## 12. Delivery Phases

### Phase 0: Foundation

- Initialize Next.js app structure
- Add theme/config/component scaffolding
- Set up Supabase clients and env validation
- Create namespace + identity resolution utilities
- Add migrations and seed/reset skeleton

### Phase 1: Core persistence and catalog integration

- Implement schema and repositories
- Build catalog adapter and `Show` normalization
- Implement overlay and merge utilities
- Add unit tests for business rules

### Phase 2: Library and detail foundations

- Build Home page, filters, and show tiles
- Build Search mode
- Build Show Detail with status/rating/tag flows
- Ensure auto-save defaults and destructive removal behavior work everywhere

### Phase 3: Detail depth and person navigation

- Add media carousel, providers, cast/crew, seasons, budget/revenue
- Build Person Detail and filmography navigation
- Add search-on-launch and related navigation polish

### Phase 4: Ask and Scoop

- Implement Ask chat, starter prompts, mentions strip, summarization, and seeded show context
- Implement streaming Scoop with 4-hour freshness and persistence rules
- Add AI contract tests and fallbacks

### Phase 5: Explore Similar and Alchemy

- Implement concept generation and selection UX
- Implement Explore Similar recommendation flow
- Implement Alchemy multi-show flow and chaining
- Add recommendation resolution and concept reset behavior

### Phase 6: Settings, export, and hardening

- Build Settings page and export zip flow
- Finalize benchmark-friendly env configuration
- Finalize reset tooling and namespace-safe destructive tests
- Add e2e and visual coverage

### Phase 7: Polish and compliance pass

- Accessibility pass
- Performance pass on search/detail/chat
- Lint and typecheck cleanup
- Final PRD compliance audit against all required journeys and rules

## 13. Risks and Mitigations

### 13.1 AI output variability

Risk:
- malformed structured output or weak recommendations

Mitigation:

- strict schema validation
- retry once on parse failures
- resolver fallback to Search
- golden-scenario smoke tests for quality dimensions from `discovery_quality_bar.md`

### 13.2 Overlay consistency bugs

Risk:
- stale or inconsistent My Data across Home/Search/Detail/AI surfaces

Mitigation:

- one shared overlay function
- mutation responses return the authoritative saved show
- invalidate or refresh all caches keyed by show ID after mutations

### 13.3 Namespace leakage

Risk:
- test/build data collisions

Mitigation:

- composite keys and indexes on `(namespace_id, user_id, show_id)`
- reset tooling requires a namespace parameter
- integration tests explicitly verify cross-namespace isolation

### 13.4 Scope creep from optional features

Risk:
- effort drifts into import/restore, custom lists, or richer auth before core flows are stable

Mitigation:

- keep optional PRD items in a deferred backlog
- finish required journeys and compliance first

## 14. Definition of Done

The implementation is complete when:

- all required pages and discovery modes are present
- save, auto-save, removal, and merge semantics match the PRD
- every user-owned row is scoped by namespace and user
- destructive test reset is namespace-scoped
- AI surfaces follow the documented output contracts and counts
- recommendations resolve to real selectable shows or cleanly fall back
- export produces a zip backup with ISO-8601 dates
- `.env.example`, migrations, and run/test/reset scripts are included
- test coverage exists for critical logic and key user journeys

## 15. Explicit Deferred Items

These should be tracked but not block benchmark completion:

- first-class `Next` status UI
- import/restore from backup zip
- saved/shareable Alchemy sessions
- named custom lists beyond tags
- offline-first/local-only data mode
