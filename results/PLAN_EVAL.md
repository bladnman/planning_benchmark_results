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
| PRD-001 | Use Next.js latest stable runtime | critical | full | `1.1 Tech Stack` says "Next.js (latest stable, App Router)" |  |
| PRD-002 | Use Supabase official client libraries | critical | full | `1.1 Tech Stack`; `1.2 Fractal Directory Structure` includes `lib/supabase/client.ts` and `server.ts` |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `4.1 Project Setup`; `5.1 .env.example` |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | `4.1 Project Setup` `.gitignore`; `5.2 Security Rules` |  |
| PRD-005 | Configure build through env without code edits | critical | full | `4.1 Project Setup` typed env wrapper plus complete env list in `5.1` |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | `5.2 Security Rules`; `1.2` separates browser and server Supabase clients |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `1.6 Scripts` lists `npm run dev`, `npm test`, and `npm run test:reset` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `2.1 Supabase Schema (Migrations)`; `1.2 Supabase Schema`; `7.6 Data Continuity` |  |
| PRD-009 | Use one stable namespace per build | critical | full | `2.2 Identity & Isolation` says every query is scoped by env `NAMESPACE_ID` |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | `2.1` PK/RLS isolate namespace data; `1.6 Scripts` scopes `npm run test:reset` to the namespace |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `2.1` `shows` and `cloud_settings` tables both require `user_id` |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `2.1` primary keys and RLS policies use `(namespace_id, user_id)` |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | `2.2 Identity & Isolation`; `5.2 Security Rules` gate `X-User-Id` to development |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | `2.2 Identity & Isolation` says real OAuth swaps in with "no schema change needed" |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | `1.3 Key Architectural Principles` says backend is source of truth; `9 Key Business Rules` repeats it |  |
| PRD-016 | Make client cache safe to discard | critical | full | `1.3 Key Architectural Principles`; `9 Key Business Rules` says clearing client storage must not lose user data |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | `1.1 Tech Stack` says Supabase can be hosted or local | The plan implies a hosted path but never explicitly documents Docker as optional or unnecessary for benchmark execution. |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | `1.5 useShow` merges Supabase and TMDB data; `7.1 Show Tile Indicators`; `9 Key Business Rules` says the user's version takes precedence everywhere | The plan states the rule, but it only concretely specifies badges and merged detail fetches rather than a shared overlay mechanism for every show surface. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | `2.1` `my_status` enum includes `next`; `4.4 My Relationship` omits it from visible chips | The plan preserves `Next` in the schema but never defines its hidden, non-primary-UI treatment. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | `4.4 My Relationship`; `9 Key Business Rules Checklist` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `1.2` includes `TagPicker`; `2.2 Filter Sidebar` derives dynamic tag filters from user tags |  |
| PRD-022 | Define collection membership by assigned status | critical | full | `9 Key Business Rules Checklist` says a show is in collection when `myStatus` is non-null |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | `4.4 My Relationship`; `7.1 Save Show Flow` |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | `4.4 My Relationship`; `7.1 Save Show Flow` |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | `4.4 My Relationship`; `7.2 Remove Show Flow` |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | `2.3 Merge Rules`; `7.2 Remove Show Flow`; `9 Key Business Rules Checklist` | The checklist promises re-add preservation, but the planned remove flow hard-deletes the row and never explains how prior My Data survives for a later re-add. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `2.1` `shows` table includes per-field `*_update_date` columns |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `2.3 Merge Rules` use timestamps for conflict resolution; `4.5 Overview + Scoop` uses 4-hour freshness | The plan covers sync conflict resolution and Scoop freshness but does not specify timestamp-driven sorting behavior. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | `4.5 Overview + Scoop`; `9 Key Business Rules Checklist` |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | `5.2 Ask Mode`; `5.3 Alchemy Mode`; `9 Key Business Rules Checklist` |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | partial | `3.3 AI Response Parsing`; `5.2 Ask Mode`; `5.3 Alchemy Mode` | Ask has an explicit resolver and fallback path, but Explore Similar and Alchemy do not spell out the same validate-and-fallback handling. |
| PRD-032 | Show collection and rating tile indicators | important | full | `7.1 Show Tile Indicators` |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `2.1 cloud_settings`; `2.3 Merge Rules` | The plan establishes shared persistence and merge rules but does not define duplicate detection or multi-device library reconciliation behavior. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | `7.6 Data Continuity`; `2.1 app_metadata` |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `1.5 useSettings`; `2.2 Filter Sidebar` persists `lastSelectedFilter`; `4.4` tracks removal-confirmation UI state; `6.2 Settings Page` |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | `2.1` stores `provider_data` JSONB; `3.1 TMDB Integration` says mapping follows the storage schema rules | The plan does not explicitly lock in "provider IDs only" persistence or enumerate transient-only detail payloads. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | `2.3 Merge Rules` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | `2.1 App Shell` defines a left filter sidebar and main content area |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | `2.1 App Shell` top bar includes a persistent Find/Discover button |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | `2.1 App Shell` top bar includes a persistent Settings button |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `3.3 Find/Discover Hub` mode switcher tabs: Search, Ask, Alchemy |  |
| PRD-042 | Show only library items matching active filters | important | full | `2.3 CollectionHome` applies selected filter and media toggle |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `2.3 CollectionHome` enumerates the four status groups |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `2.2 Filter Sidebar` lists All Shows, dynamic tags, genre, decade, community score, and media toggle |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | `2.3 CollectionHome` says tiles show poster, title, and My Data badges |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | `2.3 CollectionHome` defines both empty states |  |
| PRD-047 | Search by title or keywords | important | full | `3.2 Search Mode` uses text input with debounced search |  |
| PRD-048 | Use poster grid with collection markers | important | full | `3.2 Search Mode` renders results in a poster grid and marks in-collection items |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | `3.2 Search Mode` supports Search-on-Launch and auto-focuses search; `6.2 Settings Page` includes the toggle | The plan mentions focus behavior but does not clearly commit to routing the user directly into Search on launch. |
| PRD-050 | Keep Search non-AI in tone | important | partial | `3.2 Search Mode` is a direct TMDB query with poster-grid results | The plan keeps Search structurally non-AI but never states the UX/copy rule that Search should remain neutral and non-personified. |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | `4.1 Show Detail` says sections render in narrative hierarchy order; `4.2-4.12` break down the page | The plan does not preserve the exact documented order, especially around tag placement and where genres/languages surface. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | `4.2 Header Media` includes inline trailer playback and poster-only fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | `4.3 Core Facts`; `4.1 Show Detail` keeps these early in the page |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | `4.4 My Relationship` puts status chips in the toolbar |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `4.4 My Relationship` tags behavior |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `4.4 My Relationship` rating bar behavior |  |
| PRD-057 | Show overview early for fast scanning | important | full | `4.5 Overview + Scoop` appears near the top of the page plan |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | `4.5 Overview + Scoop` defines toggle states, streaming, and "Generating..." feedback |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | `4.6 Ask About This Show`; `5.2 Ask Mode` |  |
| PRD-060 | Include traditional recommendations strand | important | full | `4.7 Traditional Recommendations` |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | `4.8 Explore Similar` flow: Get Concepts, select concepts, Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | `4.9 Streaming Providers`; `4.10 Cast & Crew` |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | `4.11 Seasons`; `4.12 BudgetRevenue` |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | `4.4-4.8` cluster saving and discovery actions early in the detail flow | The plan places key actions early but does not spell out a concrete clutter-management or information-density strategy. |
| PRD-065 | Provide conversational Ask chat interface | important | full | `5.2 Ask Mode` chat UI with user and assistant turns |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `3.2 Ask Prompt`; `5.2 Ask Mode` | The plan defines friendly, taste-aware chat but does not explicitly require direct-answer-first responses or a concrete confidence bar. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | `5.2 Ask Mode`; `MentionedShowsStrip` |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | `5.2 Ask Mode`; `3.3 AI Response Parsing` fallback behavior |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | `5.2 Ask Mode` |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | `3.2 Ask Prompt` summarization; `5.2 Ask Mode` |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | `4.6 Ask About This Show`; `5.2 Ask Mode` |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | `3.2 Ask Prompt` exact `{ commentary, showList }`; `3.3 AI Response Parsing` exact parser |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | `3.3 AI Response Parsing` |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | `3.2 Ask Prompt` is TV/movies-only; `9 Key Business Rules Checklist` repeats the guardrail |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | `3.2 Concept Prompt` explicitly frames concepts as ingredients; `4.8` and `5.3` use concept selection flows |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | `3.2 Concept Prompt` output contract |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | `3.2 Concept Prompt` says concepts are ordered by strength and varied across axes |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | `4.8 Explore Similar` and `5.3 Alchemy Mode` require concept selection before recommendations | The plan requires selection but does not include the helper copy or UX guidance that teaches users to pick ingredients they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `4.8 Explore Similar` says "5 recommendations per round" |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `5.3 Alchemy Mode` includes the full flow and "More Alchemy!" chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | `5.3 Alchemy Mode` says changing shows or concepts clears downstream state |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | `7.4 Alchemy Flow` says multi-show concept generation returns `8+` shared concepts | The plan hints at a larger multi-show pool but never commits to a concrete larger-than-single-show output contract. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `3.2 Alchemy/Explore Similar Recs Prompt`; `4.8 Explore Similar` reason text |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `3.2 Alchemy/Explore Similar Recs Prompt` uses concept-grounded reasons and allows classics alongside recent shows | The plan describes concept matching but does not add a validation loop or quality bar for surprise-without-betrayal. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | partial | `3.2 AI Integration` defines per-surface prompts with compatible voice directions | The plan does not establish a single shared persona contract or reusable base prompt to keep all surfaces aligned. |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | `3.2` prompt descriptions and `9 Key Business Rules Checklist` mention domain and spoiler guardrails | The guardrails appear as scattered prompt notes rather than a shared cross-surface enforcement contract. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | `3.2 Scoop Prompt` is warm and opinionated; `3.2 Ask Prompt` is conversational | The plan captures warmth and opinionation but does not explicitly preserve the joy-forward, gentle-critique requirement across surfaces. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | `3.2 Scoop Prompt` defines a structured mini blog post of taste |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | `3.2 Ask Prompt` uses a conversational-friend persona rather than an essay format |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | `3.2` lists surface-specific inputs for Scoop, Ask, Concepts, Alchemy, and summarization |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | missing | none | The plan never introduces a discovery-quality rubric or a hard-fail validation gate for real-show integrity and recommendation quality. |
| PRD-092 | Show person gallery, name, and bio | important | full | `6.1 Person Detail` and `PersonHeader` |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | `6.1 PersonAnalytics` includes average ratings, top genres, and projects by year |  |
| PRD-094 | Group filmography by year | important | full | `6.1 Filmography` groups credits by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | `6.1 Filmography` taps navigate to `/show/[id]` |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `6.2 Settings Page` `AppSettings` |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | `6.2 Settings Page`; `5.2 Security Rules` on API-key safety |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | `6.2 Data Management`; `7.2 Data Export` |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | `6.2 Data Management`; `7.2 Data Export` |  |

### 3. Coverage Scores

Overall score:

`score = (79 × 1.0 + 19 × 0.5) / 99 × 100 = 89.39%`

Critical:  (26 × 1.0 + 4 × 0.5) / 30 × 100 = 93.33%  (28.0 of 30 critical requirements)
Important: (52 × 1.0 + 14 × 0.5) / 67 × 100 = 88.06%  (59.0 of 67 important requirements)
Detail:    (1 × 1.0 + 1 × 0.5) / 2 × 100 = 75.00%  (1.5 of 2 detail requirements)
Overall:   89.39% (99 total requirements)

### 4. Top Gaps

1. PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces
Without a shared guardrail contract, AI behavior can drift by surface on spoiler safety, domain limits, and honesty, creating inconsistent product behavior even if each prompt looks reasonable in isolation.

2. PRD-018 | `critical` | Overlay saved user data on every show appearance
If saved overlays do not follow a show everywhere it appears, the product breaks its central promise that the library is the user's personalized lens on the catalog.

3. PRD-031 | `critical` | Resolve AI recommendations to real selectable shows
If AI recommendations are not validated and resolved consistently across Ask, Explore Similar, and Alchemy, users will hit dead-end or untrustworthy suggestions on the app's core discovery paths.

4. PRD-026 | `critical` | Re-add preserves My Data and refreshes public data
If removed shows lose their prior relationship data permanently, users cannot trust that the app remembers their history when they revisit a title later.

5. PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity
Without a discovery-quality rubric and integrity gate, the app can ship hallucinated, off-brand, or weakly grounded recommendations without any explicit plan to catch them.

### 5. Coverage Narrative

#### Overall Posture

This is a strong plan with concentrated but meaningful gaps rather than a structurally weak one. The architecture, persistence model, primary navigation, core collection flows, and major screen breakdowns are all concrete, but several critical semantics remain underspecified around AI behavior contracts, actionability of AI outputs, and preservation of user-specific data.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, App Navigation & Discover Shell, Show Detail & Relationship UX mechanics, Person Detail, and Settings & Export. It also covers most of Collection Data & Persistence well at the default/save/remove rule level, especially timestamps in the schema, export, and namespace-plus-user scoping.

#### Weakness Clusters

The gaps cluster around AI Voice, Persona & Quality, then secondarily around Collection Data & Persistence and Concepts, Explore Similar & Alchemy. The pattern is not random: the plan is weaker wherever the product depends on strict cross-surface behavioral contracts, especially for AI guardrails, recommendation resolution, overlay fidelity, and re-add semantics.

#### Risk Assessment

If this plan were executed as-is, the app would likely function and cover the major journeys, but QA and stakeholders would first notice inconsistency in AI behavior and actionability. The most visible failure modes are AI suggestions that are not consistently validated into real shows, overlay behavior that is thinner than promised outside detail views, and uncertainty about whether user history survives a remove-and-revisit cycle.

#### Remediation Guidance

The remaining planning work is mostly about tightening contracts, not redesigning the app. Add a shared AI behavior spec that all surfaces inherit, define a discovery-quality validation pass with integrity hard-fails, make recommendation-resolution behavior explicit for every AI surface, and specify how removed shows preserve recoverable user history alongside the persistence boundaries for sync and transient TMDB detail data.
