# Implementation Plan

## 1. Goal and Delivery Approach

Build a personal TV/movie companion in Next.js with Supabase persistence, scoped for benchmark execution. The implementation must preserve three things at the same time:

- the product behaviors in `product_prd.md`
- the AI contracts and quality bar from the supporting docs
- the benchmark isolation model from `infra_rider_prd.md`

This is a planning-only deliverable. The implementation sequence below is optimized to get a fully working benchmark build with clean boundaries, deterministic persistence, and strong test coverage.

## 2. Product Readout

The app has five main user-facing surfaces:

1. Collection Home: a status-grouped personal library with filters and media-type toggle.
2. Find/Discover hub: Search, Ask, and Alchemy modes behind a clear mode switcher.
3. Show Detail: public facts, personal relationship controls, Scoop, traditional recs, Explore Similar, providers, credits, and deeper metadata.
4. Person Detail: bio, gallery, filmography, and lightweight analytics.
5. Settings and Your Data: local preferences, synced settings, and export.

Cross-cutting product rules that must shape the architecture:

- A saved show is defined by `myStatus` existing.
- Status, interest, rating, and tags can all implicitly save a show with different defaults.
- Clearing status removes the show from persistence and clears all My Data, including AI Scoop.
- User-overlaid data always wins over refreshed public catalog data.
- Any show rendered anywhere in the app must hydrate against saved data first so the user's version appears in lists, search results, detail links, and AI recommendation cards.
- All AI recommendations must resolve to real catalog items when possible.
- AI is session-scoped for chat/alchemy, but Scoop can persist if the show is already in the collection.
- Every user-owned record must be partitioned by `(namespace_id, user_id)`.
- Backend persistence is the source of truth; client cache is disposable.

## 3. Assumptions and Source Gaps

- The repo includes the full benchmark PRD set under `docs/prd/`, and that is the source of truth for this build plan.
- `product_prd.md` references companion documents (`where_is_the_heart_opus.md`, `ai_personality_opus.md`, `philosophy_opus.md`) that are not present in this repo. Voice and emotional-design work will therefore be derived from the available supporting docs: `ai_voice_personality.md`, `ai_prompting_context.md`, `concept_system.md`, `detail_page_experience.md`, and `discovery_quality_bar.md`.
- Import/Restore, surfaced `Next` status, named custom lists, and saved Alchemy sessions remain post-MVP unless they become explicit benchmark requirements later.
- Benchmark mode will use environment-provided catalog and AI credentials by default. User-entered API keys are supported in the schema and settings UX, but secure synced storage is optional and should be gated behind a server-side encryption strategy.

## 4. Architecture

### 4.1 Technical Baseline

- Next.js latest stable with App Router and TypeScript.
- Supabase Postgres via official client libraries.
- Route handlers / server actions as the server boundary for persistence, AI, export, and destructive test reset.
- Server-first data loading for initial page state, with client-side query/mutation hooks for interactive features.

### 4.2 Proposed Directory Structure

Follow the benchmark's fractal architecture while still using Next.js App Router:

```text
src/
├── app/
│   ├── (app)/
│   │   ├── page.tsx
│   │   ├── find/page.tsx
│   │   ├── show/[showKey]/page.tsx
│   │   ├── person/[personId]/page.tsx
│   │   └── settings/page.tsx
├── config/
├── theme/
├── components/
├── hooks/
├── utils/
├── pages/
│   ├── CollectionHome/
│   ├── FindHub/
│   ├── ShowDetail/
│   ├── PersonDetail/
│   └── Settings/
├── server/
│   ├── identity/
│   ├── db/
│   ├── catalog/
│   ├── ai/
│   ├── export/
│   └── testReset/
└── types/
```

Rules:

- `src/app` files stay thin and route-oriented.
- Page composition lives under `src/pages/...`.
- TSX components remain humble: markup and bindings only.
- Feature logic goes into co-located hooks, formatters, selectors, and service wrappers.
- Shared constants and theme tokens live in `src/config` and `src/theme`; no magic numbers or inline hex values in TSX.

### 4.3 Runtime Boundaries

- Server Components:
  - route shells
  - initial data fetches
  - auth/identity bootstrap
  - non-interactive metadata rendering where practical
- Client Components:
  - status/interest/rating/tag controls
  - media carousels
  - Ask chat
  - Alchemy and concept selection flows
  - streaming AI surfaces
- Server-only services:
  - Supabase write access
  - catalog provider requests
  - AI provider requests
  - export generation
  - namespace-scoped reset operations

## 5. Data Model and Persistence Plan

### 5.1 Core Persistence Strategy

Use a denormalized `shows` table per `(namespace_id, user_id, show_key)` so each saved row contains:

- stable catalog identity
- public catalog snapshot fields needed for the collection and detail pages
- user overlay fields
- persisted AI Scoop
- per-field update timestamps for merge/conflict resolution

This mirrors the storage reference while keeping destructive reset and export straightforward.

### 5.2 Supabase Schema

1. `shows`
   - partition columns: `namespace_id`, `user_id`
   - identity: `show_key` as a stable string such as `movie:12345` or `tv:98765`
   - catalog identifiers: `external_ids` JSONB, `media_type`
   - public fields: title, overview, genres, tagline, homepage, languages, image URLs, community score, popularity, release/air dates, runtime, season/episode counts, budget/revenue, provider data
   - user fields: `my_status`, `my_interest`, `my_tags`, `my_score`
   - AI fields: `ai_scoop`
   - timestamps: `my_status_update_date`, `my_interest_update_date`, `my_tags_update_date`, `my_score_update_date`, `ai_scoop_update_date`, `details_update_date`, `creation_date`
   - management: `is_test`

2. `cloud_settings`
   - partition columns: `namespace_id`, `user_id`
   - singleton row key: `id = 'globalSettings'`
   - fields: `user_name`, `version`, `catalog_api_key_encrypted` or nullable placeholder, `ai_api_key_encrypted` or nullable placeholder, `ai_model`
   - behavior: create a random initial username on first launch, and resolve settings conflicts by latest `version`

3. `app_metadata`
   - partition column: `namespace_id`
   - fields: `data_model_version`
   - used to support explicit schema-version awareness for future migration/import work

Recommended indexes:

- unique index on `shows(namespace_id, user_id, show_key)`
- btree indexes for `my_status`, `my_interest`, `details_update_date`
- GIN index on `my_tags`
- optional expression indexes for `release_year` / decade and community score filters

### 5.3 Enums and Domain Values

- `my_status`: `active | later | wait | done | quit | next`
- `my_interest`: `interested | excited`
- `media_type`: `movie | tv`

`next` exists in the data model but is not exposed as a first-class status chip in the UI for this benchmark round.

### 5.4 Merge and Save Rules

- Catalog refresh merges use `selectFirstNonEmpty(newValue, oldValue)` for non-user fields.
- User fields resolve per field by latest timestamp.
- `details_update_date` is refreshed after catalog merges.
- `creation_date` is set only on first save.
- Duplicate saves of the same show become upserts into the same `(namespace_id, user_id, show_key)` row.

Critical save/remove behaviors:

- selecting `Interested` or `Excited` creates or updates the row with `my_status = later`
- rating an unsaved show auto-saves with `my_status = done`
- tagging an unsaved show auto-saves with `my_status = later` and `my_interest = interested`
- moving away from `later` leaves `my_interest` stored for future reuse, but it is only behaviorally active when status is `later`
- clearing the active status removes the row entirely and clears all My Data by deletion
- AI Scoop is written only if the show is already persisted in the collection

### 5.5 Local Storage and Disposable Cache

Local-only settings:

- `autoSearch`
- `fontSize`
- `hideStatusRemovalConfirmation`
- `statusRemovalCountKey`
- `lastSelectedFilter`

Client cache:

- use a query cache for performance and optimistic updates
- never rely on browser storage for canonical user-owned data
- clearing local storage must only reset local UI preferences and cache, not the collection

## 6. Identity, Namespace Isolation, and Benchmark Compatibility

### 6.1 Identity Resolver

Implement a single server-side identity resolver that returns:

- `namespace_id` from environment/config for the lifetime of a run
- `user_id` from a benchmark-friendly dev mechanism:
  - first choice: `X-User-Id` header in development/test
  - fallback: `DEFAULT_USER_ID` environment variable

This keeps the schema OAuth-ready by treating `user_id` as an opaque identifier while avoiding any benchmark dependency on real auth.

### 6.2 Isolation Rules

- Every read and write query includes `namespace_id` and `user_id`.
- Namespace-scoped reset deletes only rows for the active namespace.
- Test fixtures and destructive e2e setup write rows with `is_test = true` but still remain namespace-bounded.
- No route or script may perform global teardown.

### 6.3 Required Repo Deliverables

- `.env.example` listing all required variables and brief comments
- `.gitignore` excluding real `.env*` secrets while retaining `.env.example`
- one-command scripts for:
  - app start
  - tests
  - namespace reset
  - lint/typecheck
- committed database migrations and seed/reset helpers

Suggested environment variables:

- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `APP_NAMESPACE_ID`
- `DEFAULT_USER_ID`
- `CATALOG_PROVIDER`
- `CATALOG_API_KEY`
- `AI_PROVIDER`
- `AI_PROVIDER_API_KEY`
- `AI_MODEL`
- `ENABLE_DEV_IDENTITY`

## 7. External Integrations

### 7.1 Catalog Provider Adapter

Create a provider-agnostic server adapter with methods for:

- text search
- fetch show detail
- fetch show credits, images, videos, providers, recommendations, similar items
- fetch person detail and person credits
- resolve a recommended title by external ID or fallback search
- hydrate arbitrary catalog results against persisted saved rows so user overlay data wins everywhere

Implementation detail:

- choose one concrete catalog provider for the initial build, but isolate it behind a stable interface because the PRD intentionally avoids vendor lock-in
- store only provider IDs for streaming availability in `provider_data`; resolve provider display data on read
- AI recommendation resolution should first trust external ID when present, then fall back to title search and accept the first case-insensitive title match; unresolved titles remain non-interactive or hand off to Search

### 7.2 AI Provider Adapter

Create an AI adapter with dedicated methods for:

- Ask chat response with structured mentioned-show output
- Scoop generation
- concept generation
- concept-based recommendations
- conversation summarization

Requirements enforced in code:

- TV/movie-only domain guardrails
- spoiler-safe default behavior
- consistent shared persona
- structured output validation and retry-once fallback paths

## 8. Page and Feature Implementation Plan

### 8.1 Collection Home

Primary responsibilities:

- render the saved library grouped into:
  - Active
  - Excited
  - Interested
  - Other statuses (collapsed bucket for Wait, Quit, Done, and any Later items lacking interest)
- support sidebar filters:
  - All Shows
  - one entry per tag
  - `No tags` when applicable
  - genre
  - decade
  - community score
- apply media-type toggle (`all | movies | tv`) on top of any filter
- mark tiles with in-collection and rating indicators

Implementation notes:

- derive the tag library from distinct `my_tags` values in the saved collection
- render the Active section with more visual prominence than lower-priority groups
- preserve `lastSelectedFilter` locally
- sort each status group by most relevant recent update timestamp
- empty states:
  - no collection -> prompt to Search/Ask
  - empty filtered result -> `No results found`
- all rendered cards should use hydrated saved-show data so badges, rating, and Scoop availability stay consistent with the user's library

### 8.2 Find/Discover Hub

Use one page with a stable mode switcher and three feature trees.

#### Search mode

- debounced live text search
- poster-grid results
- in-collection markers
- detail-page navigation on selection
- optional auto-open on launch from the local setting
- no AI voice in this mode

#### Ask mode

- chat transcript with user/assistant turns
- 6 random starter prompts and refresh action
- AI context includes the user's library, My Data, current seeded show context when applicable, and summarized recent conversation state
- per-turn structured output:
  - `commentary`
  - `showList` string in exact parser format: `Title::externalId::mediaType;;...`
- mentioned-shows row rendered from the parsed list
- unresolved titles become non-interactive or hand off to Search
- after roughly 10 messages, summarize older turns into 1-2 persona-consistent sentences
- `Ask about this show` variant seeds the session with the show context

#### Alchemy mode

- state machine:
  1. select 2+ source shows from saved library and global catalog
  2. request shared concepts
  3. select up to 8 concepts
  4. fetch 6 recommendations with reasons tied to chosen concepts
  5. support `More Alchemy!` chaining
- AI context includes selected input shows plus any saved My Data available for those shows
- changing source shows clears concepts and results
- changing selected concepts clears downstream recommendations
- maintain session-only state; no persistence across route exit

### 8.3 Show Detail Page

Preserve the detail page narrative order from `detail_page_experience.md` while ensuring rating and status controls remain highly visible.

Sections:

1. header media carousel with graceful poster/backdrop/logo fallback and inline trailer playback when available
2. core facts row: year, runtime or season/episode summary, community score
3. sticky or top-toolbar personal controls:
   - status/interest chips
   - my rating
4. tag chips and tag picker
5. overview text
6. Scoop CTA / cached Scoop / streamed Scoop state
7. `Ask about this show` CTA
8. genres and languages
9. traditional recommendation strand
10. Explore Similar concepts and recs
11. streaming availability
12. cast and crew strands
13. seasons for TV
14. budget/revenue for movies

Behavioral requirements to implement exactly:

- status chips include `Active`, `Interested`, `Excited`, `Done`, `Quit`, `Wait`
- `Interested` and `Excited` are UI chips that set `my_status = later` plus the interest value
- reselecting the active status triggers removal confirmation and, on confirm, deletes the row
- Scoop button states:
  - `Give me the scoop!`
  - `Show the scoop`
  - open state titled `The Scoop`
- unsaved show + Scoop:
  - generation allowed
  - content remains ephemeral unless the show is already saved
- Explore Similar:
  - `Get Concepts` -> select 1+ concepts -> `Explore Shows`
  - use the same 8-concept selection cap as Alchemy
  - show 5 recommendations per round
  - selecting/unselecting concepts clears existing recs

### 8.4 Person Detail Page

- show hero identity, bio, and image gallery
- render lightweight analytics:
  - average project ratings/community scores
  - top genres
  - projects by year
- group credits by year
- clicking a credit opens its Show Detail page

Keep person data mostly read-through from the catalog adapter; persistence is not required beyond the user's saved shows.

### 8.5 Settings and Your Data

Settings surface needs four groups:

1. App
   - font size/readability
   - Search on Launch
2. User
   - synced username
3. AI / Integrations
   - AI model selection
   - optional AI API key override
   - optional catalog API key override
4. Your Data
   - `Export My Data`

Export requirements:

- produce a `.zip`
- include a JSON backup of all saved shows and My Data
- encode dates in ISO-8601
- ensure output is scoped to `(namespace_id, user_id)`

Import/Restore stays out of scope for the initial benchmark build because the PRD explicitly marks it as desired but not currently implemented.

## 9. AI Surface Contracts and Prompting Plan

### 9.1 Shared AI Rules

All AI surfaces must:

- stay in TV/movies
- be spoiler-safe by default
- be opinionated and honest
- favor vibe/structure/craft language over generic genre blurbs
- produce actionable outputs that can map to real shows
- use the user's library, saved My Data, selected concepts, and seeded show context where relevant so discovery feels taste-aware rather than generic

### 9.2 Ask

- response tone: conversational friend, not essay, unless the user asks for depth
- put the direct answer inside the first 3-5 lines before expanding
- multi-rec answers use bullets for scanability
- mentioned shows are parsed into a reliable machine-readable row
- if parsing fails:
  - retry once with stricter format instructions
  - otherwise render commentary only and expose Search handoff

### 9.3 Scoop

- stream progressively so the page never feels blank while generating
- target roughly 150-350 words
- include:
  - personal take
  - honest stack-up vs reception
  - the Scoop centerpiece paragraph
  - fit/warnings
  - `Worth it?` gut check
- regenerate on demand after a 4-hour freshness window

### 9.4 Concepts

- output bullet list only
- each concept is 1-3 words
- no explanations, plot, or spoilers
- default quality target:
  - 8 strong concepts for single-show requests
  - a larger pool for multi-show Alchemy requests so users still choose at most 8
- diversify across structure, vibe, emotional palette, relationship dynamics, and craft

### 9.5 Concept-Based Recommendations

- Explore Similar returns 5 recs
- Alchemy returns 6 recs
- each reason explicitly ties back to the selected concepts
- reasons should stay concise, roughly 1-3 sentences each, and avoid synopsis-style filler
- bias somewhat toward recent work without forbidding classics/hidden gems
- each returned title must carry enough identity to resolve to a real catalog item
- if resolution still fails after retry, render the item as non-interactive and expose Search as the fallback path

## 10. Implementation Phases

### Phase 1: Project foundation

- scaffold Next.js app, TypeScript, linting, formatting, test runners
- add theme tokens, typography, layout shell, and responsive baseline
- create `.env.example`, script inventory, and README setup notes
- define shared TypeScript types for show, user overlay, filters, AI contracts, and settings

### Phase 2: Persistence and server services

- create Supabase migrations for `shows`, `cloud_settings`, and `app_metadata`
- implement identity resolver and namespace-aware repositories
- add namespace-scoped reset script and seed helpers
- build catalog adapter and normalized mapping layer
- build AI adapter interface and validation utilities

### Phase 3: Shared UI primitives and collection home

- build poster tile, badge, chip, filter rail, empty-state, and section primitives
- implement saved-library query and filter derivation
- deliver Collection Home with grouping, media toggle, and local filter persistence

### Phase 4: Search and read-only detail/person browsing

- implement Search mode with live query and result grid
- implement detail route with header media, facts, providers, recommendations, and credits
- implement Person Detail with filmography and analytics
- wire navigation between Search, Detail, and Person flows

### Phase 5: Personal data mutation flows

- add status/interest controls
- add rating-to-save behavior
- add tag management and tag-to-save behavior
- implement removal confirmation suppression logic
- add optimistic updates and timestamp-aware upserts

### Phase 6: AI experiences

- add streamed Scoop generation and persistence rules
- add Ask chat, starter prompts, mentions row, and summarization
- add single-show concept generation and Explore Similar recommendations
- add Alchemy multi-show blending, concept selection, and chaining
- add all AI fallback and real-show resolution logic

### Phase 7: Settings, export, and sync polish

- implement local app settings
- implement synced username and model settings
- add optional credential override UX if secure storage path is enabled
- implement `Export My Data` zip generation
- verify cross-device consistency assumptions through server-source-of-truth behavior

### Phase 8: Hardening and benchmark readiness

- add unit, integration, and e2e coverage
- add visual regression coverage for high-value screens/states
- test namespace isolation and destructive reset safety
- confirm Docker is optional, not required
- finalize docs and script ergonomics

## 11. Test and Quality Strategy

### 11.1 Unit Tests

Critical logic to cover:

- auto-save defaults for status, interest, rating, and tags
- timestamp-based merge resolution
- `selectFirstNonEmpty` catalog merge behavior
- Ask mentioned-show parser
- concept selection reducers and Alchemy state machine
- filter derivation and grouping logic
- export payload serialization and ISO-8601 encoding

### 11.2 Integration Tests

- server repository reads/writes with `(namespace_id, user_id)` scoping
- namespace-scoped reset script
- catalog-to-show mapping and merge into existing records
- AI retry/fallback paths when parsing or recommendation resolution fails
- Scoop persistence vs ephemeral unsaved behavior

### 11.3 End-to-End Tests

Required benchmark journeys:

1. Search -> Detail -> set `Interested`
2. Search -> Detail -> rate unsaved show -> saved as `Done`
3. Search -> Detail -> add tag -> saved as `Later + Interested`
4. Home -> filter by tag -> grouped results update correctly
5. Ask -> choose recommendation -> open Detail -> save it
6. Detail -> Get Concepts -> Explore Shows -> save a recommendation
7. Alchemy -> select 3 inputs -> concept selection -> 6 recs -> chain another round
8. Detail -> Person -> credit -> different Detail
9. Settings -> Export My Data
10. namespace reset only removes current namespace data
11. second user in same namespace does not see first user's library

### 11.4 Visual / UX Validation

Add regression coverage for:

- Collection Home grouping and empty states
- Show Detail section order and toolbar controls
- Scoop loading/stream/cached states
- Ask transcript plus mentioned-show strip
- Alchemy concept selection and results states

AI output quality validation should use the discovery quality bar:

- voice >= acceptable
- taste alignment >= acceptable
- real-show integrity = non-negotiable pass

## 12. Risks and Mitigations

- Missing companion "heart" docs:
  - mitigate by encoding the available voice/personality rules into reusable AI prompt builders and regression checks
- AI structured output drift:
  - mitigate with strict schemas, retry-once logic, and Search fallback
- Cross-run collisions:
  - mitigate with required namespace partitioning at repository level plus tests that prove isolation
- User-entered credential handling:
  - mitigate by making env-based defaults the primary benchmark path and gating synced secret storage behind explicit encryption support
- Overly busy Detail page:
  - mitigate by preserving the narrative hierarchy and clustering high-value actions early

## 13. Definition of Done

The build is done when:

- all major product surfaces are implemented
- saved-show behaviors match the PRD defaults and removal semantics
- AI surfaces match the documented contracts and quality bar closely enough to be testable
- every user-owned record is scoped to `(namespace_id, user_id)`
- destructive reset is namespace-safe
- the app runs from environment configuration without source edits
- tests cover the highest-risk behaviors and the core benchmark journeys
