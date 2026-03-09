# PLAN EVALUATION

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

#### Benchmark Runtime & Isolation

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

#### Collection Data & Persistence

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

#### App Navigation & Discover Shell

- PRD-038 | `important` | Provide filters panel and main screen destinations | `product_prd.md > 6. App Structure & Navigation`
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-040 | `important` | Keep Settings in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-041 | `important` | Offer Search, Ask, Alchemy discover modes | `product_prd.md > 6. App Structure & Navigation`

#### Collection Home & Search

- PRD-042 | `important` | Show only library items matching active filters | `product_prd.md > 7.1 Collection Home`
- PRD-043 | `important` | Group home into Active, Excited, Interested, Others | `product_prd.md > 7.1 Collection Home`
- PRD-044 | `important` | Support All, tag, genre, decade, score, media filters | `product_prd.md > 4.5 Filters (Ways to View the Collection)`
- PRD-045 | `important` | Render poster, title, and My Data badges | `product_prd.md > 7.1 Collection Home`
- PRD-046 | `detail` | Provide empty-library and empty-filter states | `product_prd.md > 7.1 Collection Home`
- PRD-047 | `important` | Search by title or keywords | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-048 | `important` | Use poster grid with collection markers | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-049 | `detail` | Auto-open Search when setting is enabled | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-050 | `important` | Keep Search non-AI in tone | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`

#### Show Detail & Relationship UX

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

#### Ask Chat

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

#### Concepts, Explore Similar & Alchemy

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

#### AI Voice, Persona & Quality

- PRD-085 | `important` | Keep one consistent AI persona across surfaces | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`
- PRD-087 | `important` | Make AI warm, joyful, and light in critique | `supporting_docs/ai_voice_personality.md > 2. Non-Negotiable Voice Pillars`
- PRD-088 | `important` | Structure Scoop as personal taste mini-review | `supporting_docs/ai_voice_personality.md > 4.1 Scoop (Show Detail "The Scoop")`
- PRD-089 | `important` | Keep Ask brisk and dialogue-like by default | `supporting_docs/ai_voice_personality.md > 4.2 Ask (Find → Ask)`
- PRD-090 | `important` | Feed AI the right surface-specific context inputs | `supporting_docs/ai_prompting_context.md > 2. Shared Inputs (Typical)`
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity | `supporting_docs/discovery_quality_bar.md > 4. Scoring Rubric (Quick)`

#### Person Detail

- PRD-092 | `important` | Show person gallery, name, and bio | `product_prd.md > 7.6 Person Detail Page`
- PRD-093 | `important` | Include ratings, genres, and projects-by-year analytics | `product_prd.md > 7.6 Person Detail Page`
- PRD-094 | `important` | Group filmography by year | `product_prd.md > 7.6 Person Detail Page`
- PRD-095 | `important` | Open Show Detail from selected credit | `product_prd.md > 7.6 Person Detail Page`

#### Settings & Export

- PRD-096 | `important` | Include font size and Search-on-launch settings | `product_prd.md > 7.7 Settings & Your Data`
- PRD-097 | `important` | Support username, model, and API-key settings safely | `product_prd.md > 7.7 Settings & Your Data`
- PRD-098 | `critical` | Export saved shows and My Data as zip | `product_prd.md > 7.7 Settings & Your Data`
- PRD-099 | `important` | Encode export dates using ISO-8601 | `product_prd.md > 7.7 Settings & Your Data`

Total: 99 requirements (30 critical, 67 important, 2 detail) across 10 functional areas

## 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | `Phase 1.1 Project Scaffolding` |  |
| PRD-002 | Use Supabase official client libraries | critical | full | `Phase 1.2 Supabase Schema & Migrations`, `Key Technical Decisions` |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `Phase 1.1 Project Scaffolding` |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | `Phase 1.1 Project Scaffolding` |  |
| PRD-005 | Configure build through env without code edits | critical | partial | `Phase 1.1 Project Scaffolding` | The env interface is defined, but the plan never states that env values alone make the build runnable without source edits. |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | `Phase 1.1 Project Scaffolding`, `Phase 1.2 Supabase Schema & Migrations`, `Phase 2.2 TMDB Catalog Integration` |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `Phase 1.1 Project Scaffolding`, `Phase 1.4 Test Infrastructure` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `Phase 1.2 Supabase Schema & Migrations` |  |
| PRD-009 | Use one stable namespace per build | critical | full | `Phase 1.1 Project Scaffolding`, `Phase 1.3 Identity & Namespace Isolation` |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | `Phase 1.3 Identity & Namespace Isolation`, `Phase 1.4 Test Infrastructure` |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `Phase 1.2 Supabase Schema & Migrations` |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `Phase 1.2 Supabase Schema & Migrations`, `Phase 1.3 Identity & Namespace Isolation` |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | `Phase 1.3 Identity & Namespace Isolation` | Dev identity injection is planned, but production gating and concrete benchmark-mode documentation are not. |
| PRD-014 | Real OAuth later needs no schema redesign | important | partial | `Phase 1.3 Identity & Namespace Isolation` | Future OAuth migration is noted, but the plan does not explicitly preserve that path without schema redesign. |
| PRD-015 | Keep backend as persisted source of truth | critical | partial | `Phase 1.2 Supabase Schema & Migrations`, `Phase 2.4 API Routes for Collection` | The backend persistence layer is present, but the plan does not state that it is the authoritative source of truth over client state. |
| PRD-016 | Make client cache safe to discard | critical | partial | `Key Technical Decisions`, `Phase 10.3 Local Settings Persistence` | Client caching and local settings are planned, but disposable-cache and reinstall-safety guarantees are not explicit. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | missing | none | No section states that Docker must remain optional or that hosted cloud-agent runs are a supported path. |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | `Phase 5.2 Search API`, `Phase 6.5 Data Fetching`, `Phase 9.5 AI Recommendation → Real Show Resolution` | Overlay behavior is planned on several surfaces, but not as an explicit everywhere-a-show-appears rule. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | `Phase 1.2 Supabase Schema & Migrations`, `Phase 2.1 Show Data Types & Validation`, `Phase 3.3 Shared Components` |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | `Phase 6.2 My Relationship Controls (Toolbar)`, `Phase 11.4 Interest ↔ Status Coupling` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `Phase 3.3 Shared Components`, `Phase 2.4 API Routes for Collection` |  |
| PRD-022 | Define collection membership by assigned status | critical | full | `Phase 3.3 Shared Components`, `Phase 11.2 Remove from Collection` |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | `Phase 11.1 Auto-Save Triggers` |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | `Phase 6.2 My Relationship Controls (Toolbar)`, `Phase 11.1 Auto-Save Triggers` |  |
| PRD-025 | Removing status deletes show and all My Data | critical | partial | `Phase 11.2 Remove from Collection` | The plan clears My Data, but it leaves row deletion versus empty-row retention as an implementation choice. |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | `Phase 2.3 Show Repository (CRUD)`, `Phase 11.3 Re-Add After Removal` | Re-add behavior is touched, but preserving prior My Data after a delete path is not guaranteed. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `Phase 1.2 Supabase Schema & Migrations`, `Phase 11.1 Auto-Save Triggers` |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `Phase 2.3 Show Repository (CRUD)`, `Phase 11.1 Auto-Save Triggers` | Timestamps are used for merges and updates, but explicit sorting and sync usage is not planned. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | `Phase 6.3 AI Scoop Section` |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | `Phase 9.1 Ask (AI Chat)`, `Phase 9.2 Alchemy` | Alchemy is marked session-only, but Ask reset/leave semantics and mentioned-strip ephemerality are not explicit. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | `Phase 9.1 Ask (AI Chat)`, `Phase 9.5 AI Recommendation → Real Show Resolution` |  |
| PRD-032 | Show collection and rating tile indicators | important | full | `Phase 3.3 Shared Components` |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `Phase 2.3 Show Repository (CRUD)`, `Phase 10.1 Settings Page` | Timestamp merge logic exists, but duplicate detection and transparent cross-device sync behavior are not explicit. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | `Phase 1.2 Supabase Schema & Migrations` | `app_metadata` is planned, but no upgrade or migration strategy explains how saved libraries survive model changes. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `Phase 1.2 Supabase Schema & Migrations`, `Phase 10.3 Local Settings Persistence` |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | `Phase 1.2 Supabase Schema & Migrations`, `Phase 6.5 Data Fetching` | Provider data is persisted and detail fetches are planned, but IDs-only storage and transient-only detail fields are not spelled out. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | `Phase 2.3 Show Repository (CRUD)` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | `Phase 3.1 App Layout`, `Phase 3.2 Navigation Structure` |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | `Phase 3.2 Navigation Structure` | The route exists, but the plan does not explicitly place Find/Discover in persistent primary navigation. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | `Phase 3.2 Navigation Structure` | The route exists, but the plan does not explicitly place Settings in persistent primary navigation. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `Phase 12.1 Find Page` |  |
| PRD-042 | Show only library items matching active filters | important | full | `Phase 4.1 Home Page`, `Phase 4.2 Filter Logic` |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `Phase 4.1 Home Page` |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `Phase 3.1 App Layout`, `Phase 4.2 Filter Logic` |  |
| PRD-045 | Render poster, title, and My Data badges | important | partial | `Phase 3.3 Shared Components`, `Phase 5.1 Search Page` | Poster and My Data indicators are explicit, but title rendering is not called out in the planned tile component. |
| PRD-046 | Provide empty-library and empty-filter states | detail | partial | `Phase 4.1 Home Page`, `Phase 3.3 Shared Components` | The empty-library state is explicit, but a dedicated empty-filter state is only implied. |
| PRD-047 | Search by title or keywords | important | full | `Phase 5.1 Search Page` |  |
| PRD-048 | Use poster grid with collection markers | important | full | `Phase 5.1 Search Page`, `Phase 5.2 Search API` |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | `Phase 5.1 Search Page`, `Phase 10.1 Settings Page` | Search-on-launch is mentioned as optional rather than a committed behavior. |
| PRD-050 | Keep Search non-AI in tone | important | partial | `Phase 5.1 Search Page` | The Search surface is non-AI by implication, but the plan never states that its tone must remain strictly non-AI. |
| PRD-051 | Preserve Show Detail narrative section order | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | `Phase 6.2 My Relationship Controls (Toolbar)` |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `Phase 6.2 My Relationship Controls (Toolbar)`, `Phase 11.1 Auto-Save Triggers` |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `Phase 6.2 My Relationship Controls (Toolbar)`, `Phase 11.1 Auto-Save Triggers` |  |
| PRD-057 | Show overview early for fast scanning | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | `Phase 6.3 AI Scoop Section` |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | `Phase 6.1 Detail Page Layout`, `Phase 9.1 Ask (AI Chat)` |  |
| PRD-060 | Include traditional recommendations strand | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | `Phase 6.4 Explore Similar Section` |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | `Phase 6.1 Detail Page Layout` |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | `Phase 6.1 Detail Page Layout`, `Phase 6.2 My Relationship Controls (Toolbar)` | Primary actions are placed early, but the plan does not add any explicit anti-clutter or pacing guidance for keeping the page from feeling overwhelming. |
| PRD-065 | Provide conversational Ask chat interface | important | full | `Phase 9.1 Ask (AI Chat)` |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `Phase 8.2 AI Persona Prompt`, `Phase 9.1 Ask (AI Chat)` | Spoiler-safe and conversational behavior are explicit, but direct-answer-first and confident recommendation criteria are not. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | `Phase 9.1 Ask (AI Chat)` |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | `Phase 9.1 Ask (AI Chat)` |  |
| PRD-069 | Show six random starter prompts with refresh | important | partial | `Phase 9.1 Ask (AI Chat)` | Six random starter prompts are planned, but refresh behavior is not. |
| PRD-070 | Summarize older turns while preserving voice | important | partial | `Phase 9.1 Ask (AI Chat)` | Summarization exists, but preserving the same voice in summaries is not explicit. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | `Phase 9.1 Ask (AI Chat)` |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | `Phase 8.3 Structured Output Parsing`, `Phase 9.1 Ask (AI Chat)` | The mention string format is planned, but the exact `commentary` plus `showList` object contract is not. |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | `Phase 8.3 Structured Output Parsing` |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | `Phase 8.2 AI Persona Prompt` |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` | Concept flows exist, but the plan never explicitly frames concepts as taste ingredients rather than genres. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | partial | `Phase 8.2 AI Persona Prompt`, `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` | Concept generation is planned, but bullet-only, 1-3 word, and non-generic constraints are not fully specified. |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` | The plan covers concept generation, but not strongest-first ordering or cross-axis variety. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` | Concept selection is required, but ingredient-picking guidance copy is missing. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `Phase 6.4 Explore Similar Section`, `Phase 9.3 Explore Similar (Detail Page)` |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `Phase 9.2 Alchemy` |  |
| PRD-081 | Clear downstream results when inputs change | important | partial | `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` | The plan clears results when concepts change, but it does not fully specify clearing downstream state whenever upstream show inputs change. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | `Phase 9.2 Alchemy` | Multi-show concept generation exists, but shared-across-inputs behavior and a larger option pool are not explicit. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `Phase 6.4 Explore Similar Section`, `Phase 9.2 Alchemy` |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `Phase 8.1 AI Client & Prompt Engine`, `Phase 8.2 AI Persona Prompt`, `Phase 9.2 Alchemy` | Taste-aware recommendation flows are planned, but the surprise-without-betrayal quality bar is not. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | `Phase 8.2 AI Persona Prompt` |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | `Phase 8.2 AI Persona Prompt` |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | `Phase 8.2 AI Persona Prompt`, `Phase 9.1 Ask (AI Chat)` | The persona is fun and chatty, but the plan does not explicitly preserve the warm, joyful, light-in-critique balance. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | `Phase 9.4 AI Scoop Generation` |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | `Phase 8.2 AI Persona Prompt`, `Phase 9.1 Ask (AI Chat)` | Ask is conversational, but brisk default pacing and dialogue-first brevity are not explicit. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | `Phase 8.1 AI Client & Prompt Engine`, `Phase 9.1 Ask (AI Chat)`, `Phase 9.4 AI Scoop Generation` |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | `Phase 8.3 Structured Output Parsing`, `Phase 9.5 AI Recommendation → Real Show Resolution`, `Phase 14.2 Integration Tests` | Integrity checks exist in pieces, but the plan never adds the discovery rubric or a hard-fail validation gate. |
| PRD-092 | Show person gallery, name, and bio | important | full | `Phase 7.1 Person Page Layout` |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | `Phase 7.1 Person Page Layout` |  |
| PRD-094 | Group filmography by year | important | full | `Phase 7.1 Person Page Layout` |  |
| PRD-095 | Open Show Detail from selected credit | important | full | `Phase 7.1 Person Page Layout` |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `Phase 10.1 Settings Page` |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | `Phase 10.1 Settings Page`, `Phase 1.1 Project Scaffolding` |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | `Phase 10.2 Export/Backup` |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | `Phase 10.2 Export/Backup` |  |

## 3. Coverage Scores

Overall score:

`(64 × 1.0 + 34 × 0.5) / 99 × 100 = 81.8%`

Critical:  `(22 × 1.0 + 8 × 0.5) / 30 × 100 = 86.7%`  (26.0 of 30 critical requirements)
Important: `(42 × 1.0 + 24 × 0.5) / 67 × 100 = 80.6%`  (54.0 of 67 important requirements)
Detail:    `(0 × 1.0 + 2 × 0.5) / 2 × 100 = 50.0%`  (1.0 of 2 detail requirements)
Overall:   `81.8% (99 total requirements)`

## 4. Top Gaps

- PRD-034 | `critical` | Preserve saved libraries across data-model upgrades. This matters because the plan introduces version metadata but never defines the migration behavior that prevents libraries and My Data from being lost during schema evolution.
- PRD-025 | `critical` | Removing status deletes show and all My Data. This matters because the plan leaves deletion versus empty-row retention open, which can make removal, re-add, and sync behavior diverge from the product’s core lifecycle contract.
- PRD-018 | `critical` | Overlay saved user data on every show appearance. This matters because Search, Detail, and AI surfaces can show conflicting versions of the same title if overlay precedence is not universal.
- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data. This matters because a user who re-encounters a previously saved show could lose personal history or get only a partial refresh of public fields.
- PRD-016 | `critical` | Make client cache safe to discard. This matters because reinstall or local-storage-clear scenarios can expose hidden data-loss assumptions even when backend persistence exists.

## 5. Coverage Narrative

#### Overall Posture
This is a structurally strong implementation plan with meaningful specification gaps. The major product surfaces, persistence model, and AI feature architecture are present, but several cross-cutting contracts remain underspecified enough to create QA-visible drift from the PRD.

#### Strength Clusters
The plan is strongest in Show Detail & Relationship UX, Person Detail, and Settings & Export, where the user-facing surfaces are broken down into concrete sections, flows, and files. It is also solid on the benchmark foundation: Next.js, Supabase, schema artifacts, namespace isolation, and test-reset mechanics are all planned in actionable terms.

#### Weakness Clusters
The gaps cluster around cross-cutting data lifecycle rules and AI behavioral contracts rather than missing screens. Collection Data & Persistence is the biggest concern area: overlay precedence, delete/re-add semantics, upgrade continuity, sync duplication handling, and cache-disposability remain softer than the PRD requires. A second cluster appears in AI planning, where Ask, concept generation, and discovery quality are built as features but not fully specified as strict output contracts.

#### Risk Assessment
If this plan were executed as-is, the app would likely look complete but fail on consistency and edge semantics. The first issues a stakeholder or QA reviewer would notice are stale or conflicting My Data across surfaces, removal/re-add behavior that does not quite match the spec, migration risk around saved libraries, and AI outputs that feel plausible but do not reliably satisfy the exact Ask and concept-quality contracts.

#### Remediation Guidance
The next planning pass should add a short cross-cutting contract section for data lifecycle and continuity: overlay precedence, authoritative storage, disposable caches, delete/re-add behavior, sync duplication, and upgrade migrations. The navigation shell needs a clearer statement about persistent primary-nav entry points. The AI sections also need acceptance-criteria-level detail, especially the exact Ask payload shape, starter-prompt refresh, voice-preserving summarization, concept quality rules, and the discovery validation rubric.
