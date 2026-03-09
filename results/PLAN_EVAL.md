## 1. Requirements Extraction

### Pass 1: Identify Functional Areas

1. Benchmark Runtime & Isolation
2. Collection Data & Persistence
3. App Navigation & Discover Shell
4. Collection Home & Search
5. Show Detail & Relationship UX
6. Ask Chat
7. Concepts, Explore Similar & Alchemy
8. AI Voice, Persona & Quality
9. Person Detail
10. Settings & Export

### Pass 2: Extract Requirements Within Each Area

### Benchmark Runtime & Isolation

- PRD-001 | `critical` | Use Next.js latest stable runtime | `infra_rider_prd.md > 2. Benchmark Baseline (Current Round)`
- PRD-002 | `critical` | Use Supabase official client libraries | `infra_rider_prd.md > 2. Benchmark Baseline (Current Round)`
- PRD-003 | `critical` | Ship `.env.example` with required variables | `infra_rider_prd.md > 3.1 Environment variable interface`
- PRD-004 | `important` | Ignore `.env*` secrets except example | `infra_rider_prd.md > 3.1 Environment variable interface`
- PRD-005 | `critical` | Configure build through env without code edits | `infra_rider_prd.md > 3.1 Environment variable interface`
- PRD-006 | `critical` | Keep secrets out of repo and server-only | `infra_rider_prd.md > 3.1 Environment variable interface`
- PRD-007 | `critical` | Provide app, test, reset command scripts | `infra_rider_prd.md > 3.2 One-command developer experience`
- PRD-008 | `critical` | Include repeatable schema evolution artifacts | `infra_rider_prd.md > 3.3 Database evolution artifacts`
- PRD-009 | `critical` | Use one stable namespace per build | `infra_rider_prd.md > 4.1 Build/run namespace (required)`
- PRD-010 | `critical` | Isolate namespaces and scope destructive resets | `infra_rider_prd.md > 4.1 Build/run namespace (required)`
- PRD-011 | `critical` | Attach every user record to `user_id` | `infra_rider_prd.md > 4.2 User identity (required)`
- PRD-012 | `critical` | Partition persisted data by namespace and user | `infra_rider_prd.md > 4.3 Relationship between namespace and user`
- PRD-013 | `important` | Support documented dev auth injection, prod-gated | `infra_rider_prd.md > 5.1 Auth is not required to be "real" in benchmark mode`
- PRD-014 | `important` | Real OAuth later needs no schema redesign | `infra_rider_prd.md > 5.2 Migration to real OAuth must be straightforward`
- PRD-015 | `critical` | Keep backend as persisted source of truth | `infra_rider_prd.md > 6.1 Source of truth`
- PRD-016 | `critical` | Make client cache safe to discard | `infra_rider_prd.md > 6.2 Cache is disposable`
- PRD-017 | `important` | Avoid Docker requirement for cloud-agent compatibility | `infra_rider_prd.md > 2. Benchmark Baseline (Current Round)`

### Collection Data & Persistence

- PRD-018 | `critical` | Overlay saved user data on every show appearance | `product_prd.md > 4.1 Show (Movie or TV)`
- PRD-019 | `important` | Support visible statuses plus hidden `Next` | `product_prd.md > 4.2 Status System ("My Status")`
- PRD-020 | `critical` | Map Interested/Excited chips to Later interest | `product_prd.md > 4.2 Status System ("My Status")`
- PRD-021 | `important` | Support free-form multi-tag personal tag library | `product_prd.md > 4.4 Tags (User Lists)`
- PRD-022 | `critical` | Define collection membership by assigned status | `product_prd.md > 5.1 Collection Membership`
- PRD-023 | `critical` | Save shows from status, interest, rating, tagging | `product_prd.md > 5.2 Saving Triggers`
- PRD-024 | `critical` | Default save to Later/Interested except rating-save Done | `product_prd.md > 5.3 Default Values When Saving`
- PRD-025 | `critical` | Removing status deletes show and all My Data | `product_prd.md > 5.4 Removing from Collection`
- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data | `product_prd.md > 5.5 Re-adding the Same Show`
- PRD-027 | `critical` | Track per-field My Data modification timestamps | `product_prd.md > 5.6 Timestamps`
- PRD-028 | `important` | Use timestamps for sorting, sync, freshness | `product_prd.md > 5.6 Timestamps`
- PRD-029 | `critical` | Persist Scoop only for saved shows, 4h freshness | `product_prd.md > 4.9 AI Scoop ("The Scoop")`
- PRD-030 | `important` | Keep Ask and Alchemy state session-only | `product_prd.md > 5.7 AI Data Persistence`
- PRD-031 | `critical` | Resolve AI recommendations to real selectable shows | `product_prd.md > 5.8 AI Recommendations Map to Real Shows`
- PRD-032 | `important` | Show collection and rating tile indicators | `product_prd.md > 5.9 Tile Indicators`
- PRD-033 | `important` | Sync libraries/settings consistently and merge duplicates | `product_prd.md > 5.10 Data Sync & Integrity`
- PRD-034 | `critical` | Preserve saved libraries across data-model upgrades | `product_prd.md > 5.11 Data Continuity Across Versions`
- PRD-035 | `important` | Persist synced settings, local settings, UI state | `supporting_docs/technical_docs/storage-schema.md > Other persistent storage (key-value settings)`
- PRD-036 | `important` | Keep provider IDs persisted and detail fetches transient | `supporting_docs/technical_docs/storage-schema.md > Show (movie or TV series)`
- PRD-037 | `critical` | Merge catalog fields safely and maintain timestamps | `supporting_docs/technical_docs/storage-schema.md > Merge / overwrite policy (important)`

### App Navigation & Discover Shell

- PRD-038 | `important` | Provide filters panel and main screen destinations | `product_prd.md > 6. App Structure & Navigation`
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-040 | `important` | Keep Settings in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-041 | `important` | Offer Search, Ask, Alchemy discover modes | `product_prd.md > 6. App Structure & Navigation`

### Collection Home & Search

- PRD-042 | `important` | Show only library items matching active filters | `product_prd.md > 7.1 Collection Home`
- PRD-043 | `important` | Group home into Active, Excited, Interested, Others | `product_prd.md > 7.1 Collection Home`
- PRD-044 | `important` | Support All, tag, genre, decade, score, media filters | `product_prd.md > 4.5 Filters (Ways to View the Collection)`
- PRD-045 | `important` | Render poster, title, and My Data badges | `product_prd.md > 7.1 Collection Home`
- PRD-046 | `detail` | Provide empty-library and empty-filter states | `product_prd.md > 7.1 Collection Home`
- PRD-047 | `important` | Search by title or keywords | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-048 | `important` | Use poster grid with collection markers | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-049 | `detail` | Auto-open Search when setting is enabled | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-050 | `important` | Keep Search non-AI in tone | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`

### Show Detail & Relationship UX

- PRD-051 | `important` | Preserve Show Detail narrative section order | `supporting_docs/detail_page_experience.md > 3. Narrative Hierarchy (Section Intent)`
- PRD-052 | `important` | Prioritize motion-rich header with graceful fallback | `supporting_docs/detail_page_experience.md > 3.1 Header Media`
- PRD-053 | `important` | Surface year, runtime/seasons, and community score early | `supporting_docs/detail_page_experience.md > 3.2 Core Facts + Community Score`
- PRD-054 | `important` | Place status/interest controls in toolbar | `supporting_docs/detail_page_experience.md > 3.3 My Relationship Controls`
- PRD-055 | `critical` | Auto-save unsaved tagged show as Later/Interested | `supporting_docs/detail_page_experience.md > 3.3 My Relationship Controls`
- PRD-056 | `critical` | Auto-save unsaved rated show as Done | `supporting_docs/detail_page_experience.md > 3.3 My Relationship Controls`
- PRD-057 | `important` | Show overview early for fast scanning | `supporting_docs/detail_page_experience.md > 2. First-15-Seconds Experience`
- PRD-058 | `important` | Scoop shows correct states and progressive feedback | `supporting_docs/detail_page_experience.md > 3.4 Overview + Scoop`
- PRD-059 | `important` | Ask-about-show deep-link seeds Ask context | `supporting_docs/detail_page_experience.md > 3.5 Ask About This Show`
- PRD-060 | `important` | Include traditional recommendations strand | `supporting_docs/detail_page_experience.md > 3.6 Traditional Recommendations Strand`
- PRD-061 | `important` | Explore Similar uses CTA-first concept flow | `supporting_docs/detail_page_experience.md > 3.7 Explore Similar (Concept Discovery)`
- PRD-062 | `important` | Include streaming availability and person-linking credits | `supporting_docs/detail_page_experience.md > 3.8 Streaming Availability`
- PRD-063 | `important` | Gate seasons to TV and financials to movies | `supporting_docs/detail_page_experience.md > 5. Critical States`
- PRD-064 | `important` | Keep primary actions early and page not overwhelming | `supporting_docs/detail_page_experience.md > 4. Busyness vs Power`

### Ask Chat

- PRD-065 | `important` | Provide conversational Ask chat interface | `product_prd.md > 7.3 Ask (Find → Ask)`
- PRD-066 | `important` | Answer directly with confident, spoiler-safe recommendations | `supporting_docs/discovery_quality_bar.md > 2.2 Ask / Explore Search Chat`
- PRD-067 | `important` | Show horizontal mentioned-shows strip from chat | `product_prd.md > 7.3 Ask (Find → Ask)`
- PRD-068 | `important` | Open Detail from mentions or Search fallback | `product_prd.md > 7.3 Ask (Find → Ask)`
- PRD-069 | `important` | Show six random starter prompts with refresh | `product_prd.md > 7.3 Ask (Find → Ask)`
- PRD-070 | `important` | Summarize older turns while preserving voice | `supporting_docs/ai_prompting_context.md > 4. Conversation Summarization (Chat Surfaces)`
- PRD-071 | `important` | Seed Ask-about-show sessions with show handoff | `product_prd.md > 7.3 Ask (Find → Ask)`
- PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract | `supporting_docs/ai_prompting_context.md > 3.2 Ask with Mentions (Structured "Mentioned Shows")`
- PRD-073 | `important` | Retry malformed mention output once, then fallback | `supporting_docs/ai_prompting_context.md > 5. Guardrails & Fallbacks`
- PRD-074 | `important` | Redirect Ask back into TV/movie domain | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`

### Concepts, Explore Similar & Alchemy

- PRD-075 | `important` | Treat concepts as taste ingredients, not genres | `supporting_docs/concept_system.md > 1. What a Concept Is (User Definition)`
- PRD-076 | `important` | Return bullet-only, 1-3 word, non-generic concepts | `supporting_docs/ai_prompting_context.md > 3.4 Concepts (Single-Show and Multi-Show)`
- PRD-077 | `important` | Order concepts by strongest aha and varied axes | `supporting_docs/concept_system.md > 4. Generation Rules`
- PRD-078 | `important` | Require concept selection and guide ingredient picking | `supporting_docs/concept_system.md > 5. Selection UX Rules`
- PRD-079 | `important` | Return exactly five Explore Similar recommendations | `supporting_docs/concept_system.md > 6. Concepts → Recommendations Contract`
- PRD-080 | `important` | Support full Alchemy loop with chaining | `product_prd.md > 7.4 Alchemy (Find → Alchemy)`
- PRD-081 | `important` | Clear downstream results when inputs change | `product_prd.md > 7.4 Alchemy (Find → Alchemy)`
- PRD-082 | `important` | Generate shared multi-show concepts with larger option pool | `supporting_docs/concept_system.md > 8. Notes`
- PRD-083 | `important` | Cite selected concepts in concise recommendation reasons | `supporting_docs/concept_system.md > 6. Concepts → Recommendations Contract`
- PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations | `supporting_docs/discovery_quality_bar.md > 1.2 Taste Alignment`

### AI Voice, Persona & Quality

- PRD-085 | `important` | Keep one consistent AI persona across surfaces | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`
- PRD-087 | `important` | Make AI warm, joyful, and light in critique | `supporting_docs/ai_voice_personality.md > 2. Non-Negotiable Voice Pillars`
- PRD-088 | `important` | Structure Scoop as personal taste mini-review | `supporting_docs/ai_voice_personality.md > 4.1 Scoop (Show Detail "The Scoop")`
- PRD-089 | `important` | Keep Ask brisk and dialogue-like by default | `supporting_docs/ai_voice_personality.md > 4.2 Ask (Find → Ask)`
- PRD-090 | `important` | Feed AI the right surface-specific context inputs | `supporting_docs/ai_prompting_context.md > 2. Shared Inputs (Typical)`
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity | `supporting_docs/discovery_quality_bar.md > 4. Scoring Rubric (Quick)`

### Person Detail

- PRD-092 | `important` | Show person gallery, name, and bio | `product_prd.md > 7.6 Person Detail Page`
- PRD-093 | `important` | Include ratings, genres, and projects-by-year analytics | `product_prd.md > 7.6 Person Detail Page`
- PRD-094 | `important` | Group filmography by year | `product_prd.md > 7.6 Person Detail Page`
- PRD-095 | `important` | Open Show Detail from selected credit | `product_prd.md > 7.6 Person Detail Page`

### Settings & Export

- PRD-096 | `important` | Include font size and Search-on-launch settings | `product_prd.md > 7.7 Settings & Your Data`
- PRD-097 | `important` | Support username, model, and API-key settings safely | `product_prd.md > 7.7 Settings & Your Data`
- PRD-098 | `critical` | Export saved shows and My Data as zip | `product_prd.md > 7.7 Settings & Your Data`
- PRD-099 | `important` | Encode export dates using ISO-8601 | `product_prd.md > 7.7 Settings & Your Data`

Total: 99 requirements (30 critical, 67 important, 2 detail) across 10 functional areas

## 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | 4.1 Technical Baseline: Next.js latest stable with App Router and TypeScript. |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 4.1 Technical Baseline: Supabase Postgres via official client libraries. |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 6.3 Required Repo Deliverables: .env.example with required variables and comments. |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | 6.3 Required Repo Deliverables: .gitignore excludes real .env* files while retaining .env.example. |  |
| PRD-005 | Configure build through env without code edits | critical | full | 6.3 Required Repo Deliverables plus suggested environment variables: build runs from env configuration. |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 4.3 Runtime Boundaries plus 6.3 Required Repo Deliverables: server-only services and separate public vs service-role keys. |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 6.3 Required Repo Deliverables: one-command scripts for app start, tests, reset, and lint or typecheck. |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 6.3 Required Repo Deliverables plus Phase 2: committed migrations and seed or reset helpers. |  |
| PRD-009 | Use one stable namespace per build | critical | full | 6.1 Identity Resolver: namespace_id comes from env or config for the lifetime of a run. |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 6.2 Isolation Rules: namespace-scoped reset, namespace-bounded test fixtures, and no global teardown. |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 5.2 Supabase Schema plus 6.2 Isolation Rules: user-owned rows are partitioned with user_id. |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 5.2 Supabase Schema plus 6.2 Isolation Rules: all reads and writes include (namespace_id, user_id). |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | 6.1 Identity Resolver: X-User-Id in development or test with DEFAULT_USER_ID fallback. | The plan defines benchmark-friendly identity injection, but it does not explicitly call out documentation requirements or a production gate for the dev path. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 6.1 Identity Resolver: user_id stays opaque so the schema remains OAuth-ready. |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | 2 Product Readout plus 5.5 Local Storage and Disposable Cache: backend persistence is the source of truth. |  |
| PRD-016 | Make client cache safe to discard | critical | full | 5.5 Local Storage and Disposable Cache: clearing local storage resets preferences and cache, not saved data. |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Phase 8 Hardening and benchmark readiness: confirm Docker is optional, not required. |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | 2 Product Readout plus 7.1 Catalog Provider Adapter: saved-show hydration overlays user data everywhere. |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | 5.3 Enums and Domain Values plus 8.3 Show Detail Page: next stays in the data model while visible chips stay separate. |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 5.4 Merge and Save Rules plus 8.3 Show Detail Page: Interested and Excited set my_status=later plus an interest value. |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 8.1 Collection Home: derive the tag library from distinct my_tags values in the saved collection. |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 2 Product Readout: a saved show is defined by myStatus existing. |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 5.4 Merge and Save Rules: status, interest, rating, and tagging all trigger saves. |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 5.4 Merge and Save Rules: Later or Interested defaults, with rating-save forcing Done. |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 5.4 Merge and Save Rules plus 8.3 Show Detail Page: clearing status deletes the row and all My Data. |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | 5.4 Merge and Save Rules: hard deletion on status clear plus duplicate-save upserts are specified. | The plan deletes the saved row on status removal and does not specify any mechanism that restores prior My Data when the show is added again later. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 5.2 Supabase Schema: per-field timestamps are defined for status, interest, tags, score, scoop, and details. |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | full | 5.4 Merge and Save Rules plus 8.1 Collection Home plus 9.3 Scoop: timestamps drive sorting, merge resolution, and freshness. |  |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 5.4 Merge and Save Rules plus 9.3 Scoop: Scoop persists only for saved shows and regenerates after 4 hours. |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | 2 Product Readout plus 8.2 Alchemy mode: Ask and Alchemy are session-scoped, with Alchemy clearing on route exit. |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 7.1 Catalog Provider Adapter plus 9.5 Concept-Based Recommendations: resolve by external ID first, then search fallback. |  |
| PRD-032 | Show collection and rating tile indicators | important | full | 8.1 Collection Home plus 8.2 Search mode: tiles show in-collection and rating indicators. |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | full | 5.2 Supabase Schema plus 5.4 Merge and Save Rules plus Phase 7: versioned settings, upserts, and sync verification are planned. |  |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | 5.2 Supabase Schema: app_metadata tracks data_model_version for future migration awareness. | The plan introduces schema-version metadata, but it does not spell out concrete migration or backfill steps that guarantee seamless upgrades. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | 5.2 Supabase Schema plus 5.5 Local Storage and Disposable Cache: cloud settings, local settings, and UI state are all planned. |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | 7.1 Catalog Provider Adapter: provider IDs are stored in provider_data and detail payloads come through adapter fetches. |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 5.4 Merge and Save Rules: selectFirstNonEmpty, per-field timestamp resolution, and details_update_date refresh. |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 8.1 Collection Home plus 4.2 Proposed Directory Structure: filter rail plus main destinations are planned as first-class surfaces. |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | 8.2 Find and Discover Hub plus Phase 1 project foundation: the route exists inside the shell, but primary-nav persistence is not explicit. | Find/Discover is planned as a route, but the shell requirement that it stay in persistent primary navigation is not stated explicitly. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | 8.5 Settings and Your Data plus Phase 1 project foundation: the route exists inside the shell, but primary-nav persistence is not explicit. | Settings is planned as a route, but the shell requirement that it stay in persistent primary navigation is not stated explicitly. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 2 Product Readout plus 8.2 Find and Discover Hub: Search, Ask, and Alchemy are the three discover modes. |  |
| PRD-042 | Show only library items matching active filters | important | full | 8.1 Collection Home: show only library items matching active filters. |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 8.1 Collection Home: Active, Excited, Interested, and Other status groupings are listed explicitly. |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 8.1 Collection Home: All Shows, tag, No tags, genre, decade, community score, and media-type filters are called out. |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | 8.1 Collection Home plus Phase 3: poster tile, badge primitives, and hydrated cards cover the tile presentation. |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 8.1 Collection Home: explicit empty-library and empty-filter states. |  |
| PRD-047 | Search by title or keywords | important | full | 8.2 Search mode: debounced live text search by title or keywords. |  |
| PRD-048 | Use poster grid with collection markers | important | full | 8.2 Search mode: poster-grid results with in-collection markers. |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | 8.2 Search mode: optional auto-open on launch from the local setting. |  |
| PRD-050 | Keep Search non-AI in tone | important | full | 8.2 Search mode: no AI voice in this mode. |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | 8.3 Show Detail Page: the plan says it will preserve hierarchy but lists toolbar controls ahead of the narrative body. | The plan says it will preserve the detail-page hierarchy, but its listed section order moves relationship controls ahead of the documented narrative sequence. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | 8.3 Show Detail Page: header media carousel with graceful poster, backdrop, and logo fallback plus inline trailer playback. |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 8.3 Show Detail Page: core facts row surfaces year, runtime or seasons, and community score early. |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 8.3 Show Detail Page: sticky or top-toolbar personal controls include status and interest chips. |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 5.4 Merge and Save Rules plus 8.3 Show Detail Page: tagging an unsaved show auto-saves Later plus Interested. |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 5.4 Merge and Save Rules plus 8.3 Show Detail Page: rating an unsaved show auto-saves Done. |  |
| PRD-057 | Show overview early for fast scanning | important | full | 8.3 Show Detail Page: overview text remains in the early section stack for fast scanning. |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | 8.3 Show Detail Page plus 9.3 Scoop: copy states, progressive streaming, cached state, and freshness rules are specified. |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 8.3 Show Detail Page plus 8.2 Ask mode: Ask about this show seeds the Ask session with show context. |  |
| PRD-060 | Include traditional recommendations strand | important | full | 8.3 Show Detail Page: traditional recommendation strand is a dedicated section. |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 8.3 Show Detail Page: Get Concepts to select concepts to Explore Shows is the planned CTA-first flow. |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 8.3 Show Detail Page: streaming availability plus cast and crew strands that deep-link into people. |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 8.3 Show Detail Page: seasons are TV-only and budget or revenue is movie-only. |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | 8.3 Show Detail Page plus Risks and Mitigations: actions stay early and the plan explicitly guards against an overly busy page. |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | 8.2 Ask mode: chat transcript with user and assistant turns. |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | 9.1 Shared AI Rules plus 9.2 Ask: spoiler-safe, opinionated answers with the direct answer in the first 3 to 5 lines. |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 8.2 Ask mode: mentioned shows render in a row from parsed structured output. |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 8.2 Ask mode: mentions open Detail when resolved or hand off to Search when unresolved. |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 8.2 Ask mode: six random starter prompts with a refresh action. |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | 8.2 Ask mode: older turns are summarized into 1 to 2 persona-consistent sentences after roughly 10 messages. |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 8.2 Ask mode: the Ask-about-this-show variant seeds the conversation with show context. |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | 8.2 Ask mode: commentary plus exact showList parser contract are specified explicitly. |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 9.2 Ask: retry once with stricter formatting instructions, then fall back to commentary plus Search handoff. |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | 7.2 AI Provider Adapter plus 9.1 Shared AI Rules: TV or movie-only domain guardrails are part of the adapter contract. |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | 9.1 Shared AI Rules plus 9.4 Concepts: concepts are framed around vibe, structure, emotion, relationship, and craft rather than generic genre blurbs. |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 9.4 Concepts: bullet list only, 1 to 3 words, no explanations, no spoilers. |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | 9.4 Concepts: concept variety across axes is explicit, but strongest-aha ordering is not. | Concept diversity is specified, but the plan does not explicitly say concepts are ordered by the strongest aha first. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 8.3 Explore Similar plus 8.2 Alchemy mode: concept selection is required, but ingredient-picking guidance is not described. | Concept selection is required in both flows, but the UX guidance that teaches users to pick ingredients they want more of is missing. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 8.3 Show Detail Page: Explore Similar shows 5 recommendations per round. |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 8.2 Alchemy mode: select inputs, get concepts, choose up to 8, fetch 6 recommendations, then chain More Alchemy. |  |
| PRD-081 | Clear downstream results when inputs change | important | full | 8.2 Alchemy mode: changing source shows or selected concepts clears downstream results. |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | 8.2 Alchemy mode plus 9.4 Concepts: multi-show generation starts from 2 or more inputs and requests a larger pool. |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 9.5 Concept-Based Recommendations: each reason explicitly ties back to selected concepts and stays concise. |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 2 Product Readout plus 9.5 Concept-Based Recommendations plus 11.4 quality validation: taste-aware quality is planned, but surprise is not explicit. | Taste-aware recommendations are planned, but the plan does not make the surprising-but-defensible quality bar explicit. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | 7.2 AI Provider Adapter plus 9.1 Shared AI Rules: one shared persona spans the AI surfaces. |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | 7.2 AI Provider Adapter plus 9.1 Shared AI Rules: shared guardrails apply across Ask, Scoop, concepts, and recommendation flows. |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | 9.1 Shared AI Rules plus 9.2 Ask plus 9.3 Scoop: the persona is defined, but the warmth and light-critique bar is not made testable. | A shared persona is planned, but the warm, joyful, light-in-critique voice contract is not translated into explicit acceptance criteria. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 9.3 Scoop: personal take, honest stack-up, Scoop centerpiece, fit or warnings, and Worth it are all specified. |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | 9.2 Ask: conversational friend, not essay, with direct answers and scan-friendly lists. |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 8.2 Ask mode plus 8.2 Alchemy mode plus 9.1 Shared AI Rules: library, My Data, selected concepts, show context, and conversation summaries are all fed per surface. |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | 11.4 Visual and UX Validation: use the discovery quality bar with real-show integrity as a non-negotiable pass. |  |
| PRD-092 | Show person gallery, name, and bio | important | full | 8.4 Person Detail Page: hero identity, bio, and image gallery. |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 8.4 Person Detail Page: ratings or community score, top genres, and projects by year analytics. |  |
| PRD-094 | Group filmography by year | important | full | 8.4 Person Detail Page: group credits by year. |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 8.4 Person Detail Page: clicking a credit opens Show Detail. |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 8.5 Settings and Your Data: font size or readability and Search on Launch settings. |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 8.5 Settings and Your Data plus 5.2 Supabase Schema: username, AI model, and optional API-key overrides with gated secure storage. |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 8.5 Export requirements: produce a zip with a JSON backup of saved shows and My Data. |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 8.5 Export requirements plus 11.1 Unit Tests: export dates are encoded in ISO-8601 and explicitly tested. |  |

## 3. Coverage Scores

Critical:  (28 x 1.0 + 2 x 0.5) / 30 x 100 = 96.67%  (29.0 of 30 critical requirements)
Important: (59 x 1.0 + 8 x 0.5) / 67 x 100 = 94.03%  (63.0 of 67 important requirements)
Detail:    (2 x 1.0 + 0 x 0.5) / 2 x 100 = 100.00%  (2.0 of 2 detail requirements)
Overall:   (89 x 1.0 + 10 x 0.5) / 99 x 100 = 94.95%  (94.0 of 99 requirements)

## 4. Top Gaps

- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data: Because the plan deletes the saved row on removal, it does not yet prove that a later re-add can restore the user's prior tags, rating, interest, or Scoop instead of starting from scratch.
- PRD-034 | `critical` | Preserve saved libraries across data-model upgrades: The plan adds version metadata but not the concrete migration or backfill path that proves an upgrade cannot strand existing user libraries.
- PRD-051 | `important` | Preserve Show Detail narrative section order: The plan reorders relationship controls ahead of the documented narrative flow, which risks changing the page from guided story to crowded utility surface.
- PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations: Without an explicit surprise criterion, AI discovery can regress into safe but forgettable picks that technically fit while missing the product's differentiation.
- PRD-087 | `important` | Make AI warm, joyful, and light in critique: The plan protects consistency and guardrails, but it does not yet make the emotional voice contract concrete enough to validate reliably.

## 5. Coverage Narrative

### Overall Posture
The plan is structurally strong and covers nearly every required product surface, data rule, and benchmark isolation constraint. The remaining gaps are not missing whole features; they are planning gaps around long-term data trust, shell-level navigation commitments, and a few experience-defining AI and detail-page contracts that still need sharper specification.

### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, Collection Home & Search, Ask Chat, Person Detail, and Settings & Export. Namespace isolation, save semantics, timestamp rules, AI mention contracts, export behavior, and the major benchmark journeys are all described concretely enough that an implementation team would know what to build and how to test it.

### Weakness Clusters
The partial items cluster in three places. First, long-horizon durability work is lighter than it should be, especially around re-add semantics after removal and future data-model upgrades. Second, shell and experience choreography details are softer than the core feature logic, particularly persistent primary navigation and the exact Show Detail narrative order. Third, AI planning captures contracts and guardrails better than it captures the higher-bar taste, surprise, and voice nuances that differentiate the product.

### Risk Assessment
If this plan were executed as-is, the app would probably function well in the benchmark but still feel flatter and less trustworthy than the PRD intends. A stakeholder or QA reviewer would most likely notice that Detail page sequencing feels slightly off, that shell navigation commitments are underspecified, and that AI recommendations are competent and safe without consistently delivering the distinctive, delightful taste profile the product is aiming for. The highest-trust failure mode is data continuity: removing and later re-adding a show, or upgrading the data model, could behave differently from what the PRD promises because those paths are not fully planned yet.

### Remediation Guidance
The next planning pass should add four kinds of detail. Specify explicit persistence semantics for re-adding previously removed shows and an end-to-end migration strategy for versioned data upgrades. Turn shell expectations into acceptance criteria for persistent Find and Settings navigation. Lock the Detail page order with a testable body-vs-toolbar contract so visibility improvements do not silently change the narrative hierarchy. Finally, expand the AI quality section from basic guardrails into measurable voice, concept-ordering, ingredient-selection, and surprise standards so implementation and QA can protect the product's taste identity, not just its syntax.
