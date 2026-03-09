### 1. Requirements Extraction

#### Pass 1: Identify Functional Areas

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

#### Pass 2: Extract Requirements Within Each Area

##### Benchmark Runtime & Isolation

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

##### Collection Data & Persistence

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

##### App Navigation & Discover Shell

- PRD-038 | `important` | Provide filters panel and main screen destinations | `product_prd.md > 6. App Structure & Navigation`
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-040 | `important` | Keep Settings in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-041 | `important` | Offer Search, Ask, Alchemy discover modes | `product_prd.md > 6. App Structure & Navigation`

##### Collection Home & Search

- PRD-042 | `important` | Show only library items matching active filters | `product_prd.md > 7.1 Collection Home`
- PRD-043 | `important` | Group home into Active, Excited, Interested, Others | `product_prd.md > 7.1 Collection Home`
- PRD-044 | `important` | Support All, tag, genre, decade, score, media filters | `product_prd.md > 4.5 Filters (Ways to View the Collection)`
- PRD-045 | `important` | Render poster, title, and My Data badges | `product_prd.md > 7.1 Collection Home`
- PRD-046 | `detail` | Provide empty-library and empty-filter states | `product_prd.md > 7.1 Collection Home`
- PRD-047 | `important` | Search by title or keywords | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-048 | `important` | Use poster grid with collection markers | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-049 | `detail` | Auto-open Search when setting is enabled | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-050 | `important` | Keep Search non-AI in tone | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`

##### Show Detail & Relationship UX

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

##### Ask Chat

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

##### Concepts, Explore Similar & Alchemy

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

##### AI Voice, Persona & Quality

- PRD-085 | `important` | Keep one consistent AI persona across surfaces | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`
- PRD-087 | `important` | Make AI warm, joyful, and light in critique | `supporting_docs/ai_voice_personality.md > 2. Non-Negotiable Voice Pillars`
- PRD-088 | `important` | Structure Scoop as personal taste mini-review | `supporting_docs/ai_voice_personality.md > 4.1 Scoop (Show Detail "The Scoop")`
- PRD-089 | `important` | Keep Ask brisk and dialogue-like by default | `supporting_docs/ai_voice_personality.md > 4.2 Ask (Find → Ask)`
- PRD-090 | `important` | Feed AI the right surface-specific context inputs | `supporting_docs/ai_prompting_context.md > 2. Shared Inputs (Typical)`
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity | `supporting_docs/discovery_quality_bar.md > 4. Scoring Rubric (Quick)`

##### Person Detail

- PRD-092 | `important` | Show person gallery, name, and bio | `product_prd.md > 7.6 Person Detail Page`
- PRD-093 | `important` | Include ratings, genres, and projects-by-year analytics | `product_prd.md > 7.6 Person Detail Page`
- PRD-094 | `important` | Group filmography by year | `product_prd.md > 7.6 Person Detail Page`
- PRD-095 | `important` | Open Show Detail from selected credit | `product_prd.md > 7.6 Person Detail Page`

##### Settings & Export

- PRD-096 | `important` | Include font size and Search-on-launch settings | `product_prd.md > 7.7 Settings & Your Data`
- PRD-097 | `important` | Support username, model, and API-key settings safely | `product_prd.md > 7.7 Settings & Your Data`
- PRD-098 | `critical` | Export saved shows and My Data as zip | `product_prd.md > 7.7 Settings & Your Data`
- PRD-099 | `important` | Encode export dates using ISO-8601 | `product_prd.md > 7.7 Settings & Your Data`

Total: 99 requirements (30 critical, 67 important, 2 detail) across 10 functional areas

### 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | `Overview`; "built on Next.js (latest stable)" |  |
| PRD-002 | Use Supabase official client libraries | critical | full | `### 1.1 Repository Setup`; "Install and configure Supabase client libraries" |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `### 1.1 Repository Setup` and `### 7.1 .env.example` |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | `### 1.1 Repository Setup`; ".gitignore that excludes all .env* files except .env.example" |  |
| PRD-005 | Configure build through env without code edits | critical | full | `### 1.2 Environment Configuration`; "The build MUST run by filling in .env" |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | `### 1.1 Repository Setup`; "server-only, never sent to client" |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `### 7.2 NPM Scripts` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `Phase 2: Database Schema & Migrations` and `### 7.3 Database Migration Strategy` |  |
| PRD-009 | Use one stable namespace per build | critical | full | `### 1.3 Identity & Isolation Model`; "Set once per build/run. Immutable during runtime." |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | `### 2.4 Namespace Reset Script`; namespace-scoped deletes for `shows` and `cloud_settings` |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `### 1.3 Identity & Isolation Model` and `### 2.1 shows Table`; `user_id` on persisted tables |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `### 1.3 Identity & Isolation Model`; "effective data partition is always `(namespace_id, user_id)`" |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | `### 1.3 Identity & Isolation Model` and `### 4.4 Authentication Middleware` |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | `### 7.5 Migration Path to Real OAuth`; "No schema changes needed" |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | `### 2.5 Local Settings & UI State`; "user data lives in Supabase" |  |
| PRD-016 | Make client cache safe to discard | critical | full | `### 2.5 Local Settings & UI State`; "These are disposable; clearing them is safe" |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | `### 7.4 Cloud Agent Compatibility`; "No Docker required to run" |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | `### Display Rule: User's Version Takes Precedence` |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | `### 2.1 shows Table`; `my_status` includes `"next"` while `### 6.4 Show Detail` only surfaces the visible chips |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | `### 3.2 Collection Business Logic` and `### 6.4 Show Detail` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | partial | `### 2.1 shows Table`; `my_tags text[]` and `### 6.4 Show Detail`; "My Tags display + tag picker" | Multi-tag support is planned, but free-form creation semantics and explicit tag-library behavior are not spelled out. |
| PRD-022 | Define collection membership by assigned status | critical | full | `### 3.2 Collection Business Logic` and `### 5.2 AI Scoop`; collection persistence is keyed off `my_status` being present |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | `### 3.2 Collection Business Logic`; auto-save trigger table |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | `### 3.2 Collection Business Logic`; default values block |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | `### 3.2 Collection Business Logic`; "clear show + all My Data" |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | `### 3.2 Collection Business Logic`; "Merge preserves latest My Data... + refreshes public metadata" |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `### 3.2 Collection Business Logic`; every My Data field updates its `*UpdateDate` |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `### 3.1 External Catalog Integration` and `### 3.2 Collection Business Logic` | The plan uses timestamps for merge conflict resolution and Scoop freshness, but it does not specify any sorting behavior driven by those timestamps. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | `### 5.2 AI Scoop`; freshness and persistence rules |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | `### 5.1 Ask`; "Session context is in-memory only" and `### 6.3 Find/Discover Hub`; local React state for chat | Ask is explicitly session-only, but Alchemy is described as local UI state rather than explicitly non-persisted state cleared on leave/reset. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | `### 5.7 AI Recommendation Resolution (Shared)` |  |
| PRD-032 | Show collection and rating tile indicators | important | full | `### 6.2 Collection Home` and `### Tile Indicators` |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `### 2.2 cloud_settings Table`; `version` for conflict resolution and `### 3.1 External Catalog Integration`; timestamp merge rules | The plan covers conflict resolution primitives, but it does not describe duplicate detection and transparent duplicate merging for synced libraries. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | `### Data Continuity`; migration scripts preserve existing `my*` fields with data migrations when needed |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `### 2.2 cloud_settings Table` and `### 2.5 Local Settings & UI State` |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | `### 2.1 shows Table`; `provider_data jsonb` and `### 3.1 External Catalog Integration`; transient detail fetch list | The plan persists provider data and keeps detail fetches transient, but it never constrains persisted provider storage to provider IDs only. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | `### 3.1 External Catalog Integration`; `selectFirstNonEmpty`, timestamp merge, and `details_update_date = now()` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | `### 6.1 Application Layout`; filter panel plus routed main destinations |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | `### 6.1 Application Layout`; route `/find` is defined | The plan defines the destination, but it does not specify a persistent primary-navigation affordance for Find/Discover. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | `### 6.1 Application Layout`; route `/settings` is defined | The plan defines the destination, but it does not specify a persistent primary-navigation affordance for Settings. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `### 6.3 Find/Discover Hub`; mode switcher with Search, Ask, and Alchemy |  |
| PRD-042 | Show only library items matching active filters | important | full | `### 6.2 Collection Home`; "Shows matching current filter" |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `### 6.2 Collection Home`; grouped status sections in the required order |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `### 3.3 Filter & Grouping Logic` |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | `### 6.2 Collection Home`; poster, title, in-collection badge, rating badge |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | `### 6.2 Collection Home`; empty states for no library and no filter results |  |
| PRD-047 | Search by title or keywords | important | full | `### 4.2 Catalog API` and `### 6.3 Search Mode`; search by `q` and "Text input, live queries" |  |
| PRD-048 | Use poster grid with collection markers | important | full | `### 6.3 Search Mode`; poster grid and in-collection badge |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | `### 6.3 Search Mode`; "Can be auto-opened on launch (`autoSearch` setting)" |  |
| PRD-050 | Keep Search non-AI in tone | important | full | `### AI Voice Consistency`; "Search has no AI voice" |  |
| PRD-051 | Preserve Show Detail narrative section order | important | full | `### 6.4 Show Detail`; "Section order per spec" |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | `### 6.4 Show Detail`; trailer video plus poster/logo fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | `### 6.4 Show Detail`; core facts + community score in section 2 |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | `### 6.4 Show Detail`; "Toolbar controls (sticky/prominent, not in scroll body)" |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `### 3.2 Collection Business Logic` and `### 6.4 Show Detail` |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `### 3.2 Collection Business Logic` and `### 6.4 Show Detail` |  |
| PRD-057 | Show overview early for fast scanning | important | full | `### 6.4 Show Detail`; overview appears immediately after the relationship controls |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | `### 5.2 AI Scoop` and `### 6.4 Show Detail`; copy states plus "Generating..." feedback |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | `### 5.1 Ask`; Ask About This Show variant and `### 6.4 Show Detail` CTA |  |
| PRD-060 | Include traditional recommendations strand | important | full | `### 6.4 Show Detail`; traditional recommendations strand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | `### 6.4 Show Detail`; "Get Concepts" -> select -> "Explore Shows" |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | `### 6.4 Show Detail`; streaming availability and cast/crew links to `/person/[id]` |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | `### 6.4 Show Detail`; "Seasons (TV only)" and "Budget / Revenue (movies)" |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | `### 6.4 Show Detail`; sticky toolbar plus Scoop, Ask, and Explore Similar placed high on the page | The section order front-loads actions, but the plan never explicitly addresses clutter control or the "powerful but not overwhelming" design constraint. |
| PRD-065 | Provide conversational Ask chat interface | important | full | `### 5.1 Ask (Chat)` and `### 6.3 Ask Mode` |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `### 5.1 Ask (Chat)`; "fun, chatty, spoiler-safe, opinionated friend" | The plan covers persona and spoiler safety, but it does not explicitly require direct answers in the first lines or confident recommendation formatting. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | `### 5.1 Ask`; "Mentioned Shows" strip and `### 6.3 Ask Mode` |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | `### 5.1 Ask` and `### 6.3 Ask Mode`; detail open or Search handoff if unresolved |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | `### 5.1 Ask`; six random starter prompts with refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | `### 5.1 Ask`; "summarize oldest N turns into 1-2 sentences using same AI persona/tone" |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | `### 5.1 Ask About This Show variant` |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | `### 5.1 Ask`; output `{ commentary, showList }` and exact `Title::externalId::mediaType` format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | `### 5.1 Ask`; failure handling block |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | `### 5.1 Ask`; "Domain restriction: TV/movies only" |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | `### 5.3 Concepts (Single Show)`; diverse axes like structure, tone, emotion, and craft | The plan covers concept quality, but it never explicitly frames concepts as taste ingredients rather than genre labels. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | `### 5.3 Concepts (Single Show)`; bullet list only, 1-3 words, non-generic |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | `### 5.3 Concepts (Single Show)`; "Ordered by aha strength" and diverse axes |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | `### 5.3 Concepts (Single Show)` and `### 6.4 Show Detail`; concept selection is required before exploring | The interaction flow is covered, but the UI guidance to help users "pick the ingredients you want more of" is missing. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `### 5.5 Explore Similar`; "Output: 5 show recommendations" |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `### 5.6 Alchemy`; chaining via "More Alchemy!" |  |
| PRD-081 | Clear downstream results when inputs change | important | full | `### 5.6 Alchemy`; changing input shows or concepts clears downstream results |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | `### 5.4 Concepts (Multi-Show / Alchemy)`; larger pool representing shared commonality |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `### 5.5 Explore Similar`; reason cites selected concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `### 5.5 Explore Similar`; concept-cited reasons and `### AI Voice Consistency`; taste-aware recommendations | The plan covers alignment and explanation, but it does not set the "surprising but defensible" recommendation bar that differentiates discovery quality. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | `### AI Voice Consistency`; "All AI surfaces use the same base persona" |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | `### Spoiler Safety` and `### 5.1 Ask`; shared spoiler-safety and TV/movie-domain guardrails |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | `### 5.1 Ask`; "fun, chatty" persona and `### AI Voice Consistency`; surface adaptations | The plan names a shared persona, but it does not explicitly lock in the warm, joy-forward, light-critique behavior described in the spec. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | `### 5.2 AI Scoop`; personal take, stack-up, Scoop centerpiece, fit, and verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | `### 5.1 Ask`; conversational chat contract | Conversational behavior is covered, but the plan does not specify the brisk default length or dialogue cadence expected by the voice spec. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | `### 5.1 Ask`, `### 5.2 AI Scoop`, `### 5.3 Concepts`, `### 5.5 Explore Similar`, and `### 5.6 Alchemy` |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | `### 8.1 Unit Tests` and `### 8.2 Integration Tests`; parser, concept quality, and resolution tests | The plan includes some verification, but it does not define the explicit discovery rubric or a hard-fail quality gate for integrity. |
| PRD-092 | Show person gallery, name, and bio | important | full | `### 6.5 Person Detail`; image gallery and bio block |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | `### 6.5 Person Detail`; analytics charts for average rating, top genres, and projects by year |  |
| PRD-094 | Group filmography by year | important | full | `### 6.5 Person Detail`; "Filmography — grouped by year" |  |
| PRD-095 | Open Show Detail from selected credit | important | full | `### 6.5 Person Detail`; each filmography item opens `/show/[id]` |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `### 6.6 Settings`; font size selector and Search-on-launch toggle |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | `### 6.6 Settings` and `### 2.2 cloud_settings Table`; username, model, AI key, and catalog key with "must never be committed" note |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | `### 6.6 Settings`; "Export My Data" plus `GET /api/export` |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | `### 6.6 Settings` and `### Export Flow`; ISO-8601 dates |  |

### 3. Coverage Scores

score = (84 × 1.0 + 15 × 0.5) / 99 × 100 = 92.42%

Critical:  (30 × 1.0 + 0 × 0.5) / 30 × 100 = 100.00%  (30.0 of 30 critical requirements)
Important: (52 × 1.0 + 15 × 0.5) / 67 × 100 = 88.81%  (59.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.00%  (2.0 of 2 detail requirements)
Overall:   92.42% (91.5 of 99 total requirements)

### 4. Top Gaps

- PRD-033 | `important` | Sync libraries/settings consistently and merge duplicates. This matters because multi-device users can end up with duplicate shows or divergent settings, which undermines trust in the collection as a durable source of truth.
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity. This matters because AI outputs can look structurally valid while still drifting off-brand or failing real-show integrity unless the plan defines an explicit quality gate.
- PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations. This matters because without that bar, Explore Similar and Alchemy can regress into safe, obvious recs and lose the product's main differentiator.
- PRD-066 | `important` | Answer directly with confident, spoiler-safe recommendations. This matters because Ask is one of the fastest discovery loops, and a tentative or overly verbose answer weakens perceived AI quality immediately.
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation. This matters because the app's core Search, Ask, and Alchemy surfaces become harder to reach repeatedly if the primary entry point is not explicitly persistent.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with solid architectural coverage and well-specified core user flows. The remaining gaps are not foundational backend misses; they are mostly product-behavior contracts around navigation persistence, discovery quality bars, and a few persistence nuances that would affect polish, consistency, and parity with the PRD's intended feel.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence core mechanics, Show Detail & Relationship UX, Person Detail, and Settings & Export. It is especially concrete on namespace/user isolation, migration artifacts, auto-save rules, Scoop persistence, AI response contracts, detail-page sectioning, and export behavior.

#### Weakness Clusters

The partials cluster in three patterns. First, the Discover shell is structurally present but underspecified at the primary-navigation level. Second, AI quality requirements are planned as features and contracts, but not yet as acceptance bars: Ask directness, concept philosophy, recommendation surprise, and discovery QA are all lighter than the PRD. Third, persistence edge cases around sync duplicates, provider-data storage scope, and session-only Alchemy behavior are less explicit than the rest of the data model.

#### Risk Assessment

If this plan were executed as-is, the app would likely work end to end, but QA and stakeholders would notice that some of the product's differentiating behavior feels flatter or less intentional than the PRD describes. The first noticeable issues would be discovery quality drift, such as Ask answers that are conversational but not punchy enough, concept/recommendation flows that are aligned but not especially surprising, and AI behavior that lacks a clear pass/fail rubric. Secondary issues would show up in shell/navigation consistency and sync edge cases rather than in the main CRUD flows.

#### Remediation Guidance

The remaining work is mostly a specification pass, not an architectural rewrite. Tighten the plan with explicit acceptance criteria for persistent Find/Settings navigation, session lifecycle rules for Alchemy, free-form tag-library behavior, provider IDs-only persistence, sync duplicate handling, and the AI quality bar across Ask, Concepts, and recommendation surfaces. In practice, this means adding a thin layer of UX contract language and QA gates on top of an otherwise strong implementation plan.
