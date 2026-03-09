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
| PRD-001 | Use Next.js latest stable runtime | `critical` | full | `Spec Drivers`; `Proposed Architecture` (`Next.js` App Router). |  |
| PRD-002 | Use Supabase official client libraries | `critical` | partial | `Spec Drivers`; `Delivery Phases > 1. Foundation and repo scaffolding` (`Supabase client setup`). | The plan commits to Supabase but does not explicitly require the official Supabase client libraries. |
| PRD-003 | Ship `.env.example` with required variables | `critical` | full | `Target File Layout` (`.env.example`); `Testing Strategy` (`confirm .env.example completeness`). |  |
| PRD-004 | Ignore `.env*` secrets except example | `important` | partial | `Settings And Backup` (`Keep API keys out of version control`); `Testing Strategy` (`no secret leakage`). | It never explicitly calls for `.gitignore` rules that exclude `.env*` while preserving `.env.example`. |
| PRD-005 | Configure build through env without code edits | `critical` | full | `Spec Drivers` (`env-only setup`); `Delivery Phases > 1. Foundation and repo scaffolding` (`environment contract`); `Definition Of Done` (`runs via env configuration only`). |  |
| PRD-006 | Keep secrets out of repo and server-only | `critical` | full | `Settings And Backup`; `Cross-Cutting Decisions` (`server boundaries to protect secrets`); `Testing Strategy` (`browser-only anon key usage`). |  |
| PRD-007 | Provide app, test, reset command scripts | `critical` | partial | `Target File Layout` (`package.json`, `reset-namespace.ts`); `Testing Strategy`. | The reset path is planned, but start-app and test-script deliverables are not explicitly called out as required commands. |
| PRD-008 | Include repeatable schema evolution artifacts | `critical` | full | `Target File Layout` (`supabase/migrations/`); `Delivery Phases > 1. Foundation and repo scaffolding`; `Testing Strategy` (`repeatable migrations`). |  |
| PRD-009 | Use one stable namespace per build | `critical` | partial | `Domain Model And Data Responsibilities` (`(namespace_id, user_id)`); `Definition Of Done` (`namespace-safe persistence`). | The plan enforces namespacing but does not explicitly state that one stable namespace is fixed for the lifetime of each build/run. |
| PRD-010 | Isolate namespaces and scope destructive resets | `critical` | full | `Target File Layout` (`reset-namespace.ts`); `Delivery Phases > 2. Data model, persistence, and merge engine`; `Testing Strategy` (`namespace-scoped reset`). |  |
| PRD-011 | Attach every user record to `user_id` | `critical` | full | `Spec Drivers` (`strict (namespace_id, user_id) isolation`); `Domain Model And Data Responsibilities`. |  |
| PRD-012 | Partition persisted data by namespace and user | `critical` | full | `Domain Model And Data Responsibilities` (`The effective storage partition is (namespace_id, user_id)`). |  |
| PRD-013 | Support documented dev auth injection, prod-gated | `important` | partial | `Delivery Phases > 1. Foundation and repo scaffolding` (`benchmark-friendly identity injection`); `Target File Layout` (`src/server/auth/`). | The plan does not explicitly require documentation of the dev auth path or spell out production gating. |
| PRD-014 | Real OAuth later needs no schema redesign | `important` | partial | `Target File Layout` (`src/server/auth/`); `Domain Model And Data Responsibilities` (`explicit identity context`). | It implies an auth abstraction but does not explicitly say real OAuth can be added later without schema redesign. |
| PRD-015 | Keep backend as persisted source of truth | `critical` | full | `Spec Drivers` (`server-side source of truth`); `Cross-Cutting Decisions` (`writes go through server boundaries`); `Definition Of Done`. |  |
| PRD-016 | Make client cache safe to discard | `critical` | partial | `Proposed Architecture` (`Only durable data should be persisted`); `Cross-Cutting Decisions`. | The plan implies disposable client state, but it never explicitly states that clearing local storage or reinstalling must be safe. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | `important` | missing | none | Docker avoidance and hosted-Supabase cloud-agent compatibility are not addressed anywhere in the plan. |
| PRD-018 | Overlay saved user data on every show appearance | `critical` | full | `Product Breakdown` (`user's version of a show always wins`); `Domain Model And Data Responsibilities` (`Show` overlays public and user-owned fields). |  |
| PRD-019 | Support visible statuses plus hidden `Next` | `important` | partial | `Domain Model And Data Responsibilities` (`myStatus`, `myInterest`); `Feature-By-Feature Plan > Collection Home`. | The status model is covered broadly, but the hidden `Next` state is never called out. |
| PRD-020 | Map Interested/Excited chips to Later interest | `critical` | partial | `Feature-By-Feature Plan > Collection Home` (`Excited`, `Interested` groups); `Delivery Phases > 3. Collection and search backbone` (`interest save`). | The plan does not explicitly state that the Interested/Excited chips map to `Later` plus an interest level. |
| PRD-021 | Support free-form multi-tag personal tag library | `important` | full | `Domain Model And Data Responsibilities` (`myTags`); `Delivery Phases > 3. Collection and search backbone` (`tag-derived navigation`); `Target File Layout` (`TagManager`). |  |
| PRD-022 | Define collection membership by assigned status | `critical` | partial | `Domain Model And Data Responsibilities` (`Show` carries `myStatus`); `Delivery Phases > 3. Collection and search backbone`. | It plans status-driven saves, but it does not explicitly define collection membership as presence of a status. |
| PRD-023 | Save shows from status, interest, rating, tagging | `critical` | full | `Delivery Phases > 3. Collection and search backbone` (`Wire all save triggers now`); `Cross-Cutting Decisions` (`auto-save defaults`). |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | `critical` | full | `Delivery Phases > 3. Collection and search backbone` (`rating-to-save as Done`; `tag-to-save as Later + Interested`). |  |
| PRD-025 | Removing status deletes show and all My Data | `critical` | partial | `Feature-By-Feature Plan > Show Detail` (`destructive removal behavior behind confirmation`); `Cross-Cutting Decisions` (`remove-from-collection semantics`). | Removal confirmation is planned, but the plan never explicitly says clearing status deletes the show and all My Data fields. |
| PRD-026 | Re-add preserves My Data and refreshes public data | `critical` | partial | `Domain Model And Data Responsibilities` (catalog merge rules preserve user-owned fields); `Delivery Phases > 2. Data model, persistence, and merge engine`. | The merge engine is planned, but the specific re-add flow that restores prior My Data while refreshing public metadata is not spelled out. |
| PRD-027 | Track per-field My Data modification timestamps | `critical` | full | `Domain Model And Data Responsibilities` (`user-owned fields resolve by per-field timestamps`); `Cross-Cutting Decisions` (`merge timestamps`). |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | `important` | partial | `Domain Model And Data Responsibilities`; `Feature-By-Feature Plan > Scoop` (`4-hour window`). | Timestamp use is planned for merge behavior and freshness, but sorting and sync use cases are not called out explicitly. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | `critical` | full | `Feature-By-Feature Plan > Scoop` (`Persist only when the show is in the collection`; `4-hour window`). |  |
| PRD-030 | Keep Ask and Alchemy state session-only | `important` | full | `Proposed Architecture` (`chat history, mention strips, Alchemy outputs ... session-scoped`); `Feature-By-Feature Plan > Ask`; `Feature-By-Feature Plan > Alchemy`. |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | `critical` | full | `Delivery Phases > 5. AI discovery surfaces` (`recommendation resolution`); `Feature-By-Feature Plan > Explore Similar`; `Cross-Cutting Decisions`. |  |
| PRD-032 | Show collection and rating tile indicators | `important` | full | `Feature-By-Feature Plan > Collection Home` (`in-collection and rating badges`); `Feature-By-Feature Plan > Search` (`Mark in-collection items`). |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | `important` | partial | `Domain Model And Data Responsibilities` (timestamped merges); `Feature-By-Feature Plan > Settings And Backup` (`optionally synced settings`). | The plan addresses timestamps and synced settings but does not explicitly cover cross-device library sync behavior or duplicate-item merging. |
| PRD-034 | Preserve saved libraries across data-model upgrades | `critical` | partial | `Delivery Phases > 2. Data model, persistence, and merge engine` (`app_metadata`); `Target File Layout` (`supabase/migrations/`). | Schema evolution is planned, but the plan does not explicitly require upgrade-safe migration/backfill that preserves saved libraries across model changes. |
| PRD-035 | Persist synced settings, local settings, UI state | `important` | partial | `Proposed Architecture` (`Show`, `CloudSettings`, and `AppMetadata` persisted); `Feature-By-Feature Plan > Settings And Backup`; `Feature-By-Feature Plan > Search` (`Search on Launch`); `Feature-By-Feature Plan > Show Detail` (`remembered opt-out flag`). | It covers synced settings and some local UI state, but it never explicitly captures the broader UI-state persistence contract. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | `important` | partial | `Domain Model And Data Responsibilities` (transient credits/seasons/images/videos/recommendations stay re-fetchable; provider data resolves from one consistent show shape). | The transient/detail boundary is well covered, but the plan does not explicitly say only provider IDs are persisted. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | `critical` | full | `Domain Model And Data Responsibilities` (`first-non-empty`, per-field timestamps, `detailsUpdateDate`, `creationDate`); `Delivery Phases > 2. Data model, persistence, and merge engine`. |  |
| PRD-038 | Provide filters panel and main screen destinations | `important` | full | `Target File Layout` (route shell and pages); `Delivery Phases > 3. Collection and search backbone` (`filter state`, `tag-derived navigation`). |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | `important` | partial | `Target File Layout` (`/find` route, `Find` page); `Target File Layout` (`SearchMode`, `AskMode`, `AlchemyMode`). | Find/Discover exists as a route and mode hub, but persistent primary-navigation placement is not explicitly planned. |
| PRD-040 | Keep Settings in persistent primary navigation | `important` | partial | `Target File Layout` (`/settings` route, `Settings` page). | Settings is planned as a page, but the plan does not explicitly commit to persistent primary-navigation placement. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | `important` | full | `Target File Layout` (`SearchMode`, `AskMode`, `AlchemyMode`); `Feature-By-Feature Plan > Search`; `Feature-By-Feature Plan > Ask`; `Feature-By-Feature Plan > Alchemy`. |  |
| PRD-042 | Show only library items matching active filters | `important` | full | `Feature-By-Feature Plan > Collection Home` (`Implement filtered collection views`); `Delivery Phases > 3. Collection and search backbone`. |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | `important` | full | `Feature-By-Feature Plan > Collection Home` (`grouped into Active, Excited, Interested, and remaining statuses`). |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | `important` | full | `Feature-By-Feature Plan > Collection Home` (`tag-driven filters`, `No tags`, genre, decade, community score, media-type toggle). |  |
| PRD-045 | Render poster, title, and My Data badges | `important` | full | `Feature-By-Feature Plan > Collection Home` (`Surface in-collection and rating badges consistently on show tiles`). |  |
| PRD-046 | Provide empty-library and empty-filter states | `detail` | full | `Feature-By-Feature Plan > Collection Home` (`Ensure empty states route users back to Search or Ask`). |  |
| PRD-047 | Search by title or keywords | `important` | partial | `Feature-By-Feature Plan > Search` (`straightforward catalog search mode`). | Search is planned, but the plan does not explicitly state title-or-keyword query behavior. |
| PRD-048 | Use poster grid with collection markers | `important` | partial | `Feature-By-Feature Plan > Search` (`Mark in-collection items`). | Collection markers are covered, but the poster-grid presentation is not explicitly called out. |
| PRD-049 | Auto-open Search when setting is enabled | `detail` | full | `Feature-By-Feature Plan > Search` (`Support Search on Launch from settings via local UI state`). |  |
| PRD-050 | Keep Search non-AI in tone | `important` | full | `Feature-By-Feature Plan > Search` (`no AI personality`). |  |
| PRD-051 | Preserve Show Detail narrative section order | `important` | full | `Delivery Phases > 4. Show detail and person detail` (`Implement the detail page in the narrative order required ...`). |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | `important` | partial | `Feature-By-Feature Plan > Show Detail` (`mood-setting media`); `Delivery Phases > 4. Show detail and person detail`. | The plan prioritizes the header media, but it does not explicitly call out trailer-first behavior with graceful fallback when motion assets are absent. |
| PRD-053 | Surface year, runtime/seasons, and community score early | `important` | full | `Feature-By-Feature Plan > Show Detail` (`quick facts` early); `Delivery Phases > 4. Show detail and person detail`. |  |
| PRD-054 | Place status/interest controls in toolbar | `important` | full | `Target File Layout` (`StatusToolbar`); `Feature-By-Feature Plan > Show Detail` (`personal relationship controls`). |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | `critical` | full | `Delivery Phases > 3. Collection and search backbone` (`tag-to-save as Later + Interested`); `Feature-By-Feature Plan > Show Detail`. |  |
| PRD-056 | Auto-save unsaved rated show as Done | `critical` | full | `Delivery Phases > 3. Collection and search backbone` (`rating-to-save as Done`); `Feature-By-Feature Plan > Show Detail`. |  |
| PRD-057 | Show overview early for fast scanning | `important` | full | `Feature-By-Feature Plan > Show Detail` (`overview` in first-15-seconds hierarchy). |  |
| PRD-058 | Scoop shows correct states and progressive feedback | `important` | partial | `Feature-By-Feature Plan > Scoop` (`generate on demand`, `stream progressively`, `Persist only when the show is in the collection`, `4-hour window`). | Streaming and persistence are planned, but the detailed Scoop state model and toggle-copy states are not explicitly covered. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | `important` | full | `Delivery Phases > 4. Show detail and person detail` (`Ask handoff`); `Feature-By-Feature Plan > Ask` (`Ask about this show`). |  |
| PRD-060 | Include traditional recommendations strand | `important` | full | `Delivery Phases > 4. Show detail and person detail` (`recommendations`); `Feature-By-Feature Plan > Show Detail`. |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | `important` | partial | `Feature-By-Feature Plan > Explore Similar` (`Generate a concept set`; `Require 1+ selected concepts before recommendation generation`). | The plan covers the flow but does not explicitly preserve the CTA-first `Get Concepts` / `Explore Shows` interaction language. |
| PRD-062 | Include streaming availability and person-linking credits | `important` | full | `Delivery Phases > 4. Show detail and person detail` (`providers, cast/crew`); `Feature-By-Feature Plan > Person Detail`. |  |
| PRD-063 | Gate seasons to TV and financials to movies | `important` | partial | `Delivery Phases > 4. Show detail and person detail` (`media-specific sections`). | The plan implies conditional media sections but does not explicitly say seasons are TV-only and financials are movie-only. |
| PRD-064 | Keep primary actions early and page not overwhelming | `important` | partial | `Feature-By-Feature Plan > Show Detail` (`first-15-seconds hierarchy`); `Delivery Phases > 4. Show detail and person detail`. | The plan emphasizes early actions, but it does not explicitly address clutter-control or the busyness-vs-power balance. |
| PRD-065 | Provide conversational Ask chat interface | `important` | full | `Feature-By-Feature Plan > Ask` (`Separate general Ask ... route both through one conversation engine`). |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | `important` | partial | `Product Breakdown` (`AI contract layer ... on-brand, spoiler-safe`); `Feature-By-Feature Plan > Ask` (`one voice contract`); `Testing Strategy` (`voice adherence`, `taste alignment`). | The plan treats tone and quality as goals, but it does not explicitly state that Ask answers should lead directly with confident recommendations. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | `important` | full | `Feature-By-Feature Plan > Ask` (`mentioned-shows strip`). |  |
| PRD-068 | Open Detail from mentions or Search fallback | `important` | partial | `Feature-By-Feature Plan > Ask` (`mentioned-shows strip`); `Cross-Cutting Decisions` (`Search/non-interactive fallback when catalog resolution fails`). | Fallback behavior is planned, but the happy-path tap from a mention into Show Detail is only implied, not stated outright. |
| PRD-069 | Show six random starter prompts with refresh | `important` | missing | none | The welcome state's six random starter prompts and refresh behavior are omitted from the plan. |
| PRD-070 | Summarize older turns while preserving voice | `important` | full | `Feature-By-Feature Plan > Ask` (`Summarize older turns after roughly 10 messages while preserving persona tone`). |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | `important` | full | `Feature-By-Feature Plan > Ask` (`Separate general Ask from Ask about this show`); `Delivery Phases > 4. Show detail and person detail` (`Ask handoff`). |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | `critical` | partial | `Feature-By-Feature Plan > Ask` (`exact Title::externalId::mediaType formatting`); `Cross-Cutting Decisions` (`schema validation`). | The plan specifies the list string format but does not explicitly call for the surrounding `commentary` + `showList` response object. |
| PRD-073 | Retry malformed mention output once, then fallback | `important` | full | `Cross-Cutting Decisions` (`one retry path for malformed output, and a Search/non-interactive fallback`). |  |
| PRD-074 | Redirect Ask back into TV/movie domain | `important` | partial | `Product Breakdown` (`AI contract layer`); `Feature-By-Feature Plan > Ask`; `Testing Strategy` (`voice adherence`). | TV/movie domain control is part of the supporting docs, but the plan never explicitly states Ask should redirect back when users leave the domain. |
| PRD-075 | Treat concepts as taste ingredients, not genres | `important` | partial | `Spec Drivers` (`concept rules`); `Feature-By-Feature Plan > Explore Similar`; `Feature-By-Feature Plan > Alchemy`. | The plan treats concepts as a feature surface, but it never explicitly states that concepts are taste ingredients rather than genres. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | `important` | partial | `Delivery Phases > 5. AI discovery surfaces` (`typed request builders`, `strict output parsers`); `Cross-Cutting Decisions` (`schema validation`). | Format validation is planned, but the exact bullet-only, 1-3 word, non-generic concept contract is not specified. |
| PRD-077 | Order concepts by strongest aha and varied axes | `important` | partial | `Cross-Cutting Decisions` (`concept/reason specificity`); `Testing Strategy` (`discovery quality bar`). | The plan does not explicitly require strongest-first ordering or varied concept axes. |
| PRD-078 | Require concept selection and guide ingredient picking | `important` | partial | `Feature-By-Feature Plan > Explore Similar` (`Require 1+ selected concepts`); `Feature-By-Feature Plan > Alchemy` (`capping user selection at 8`). | Selection gating is covered, but the ingredient-picking guidance/copy requirement is not addressed. |
| PRD-079 | Return exactly five Explore Similar recommendations | `important` | full | `Feature-By-Feature Plan > Explore Similar` (`Return exactly 5 recommendations`). |  |
| PRD-080 | Support full Alchemy loop with chaining | `important` | full | `Feature-By-Feature Plan > Alchemy` (`2+ input shows`, `selection at 8`, `Return exactly 6 recommendations per round`, `support chained sessions`). |  |
| PRD-081 | Clear downstream results when inputs change | `important` | full | `Feature-By-Feature Plan > Alchemy` (`Clear downstream concepts/results when seed inputs change`). |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | `important` | full | `Feature-By-Feature Plan > Alchemy` (`Generate a larger shared concept pool than Explore Similar`). |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | `important` | full | `Feature-By-Feature Plan > Explore Similar` (`reasons tied directly to the chosen concepts`); `Cross-Cutting Decisions`. |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | `important` | full | `Cross-Cutting Decisions` (`discovery quality as testable behavior`); `Testing Strategy` (`taste alignment`, `surprise without betrayal`). |  |
| PRD-085 | Keep one consistent AI persona across surfaces | `important` | full | `Feature-By-Feature Plan > Ask` (`one voice contract`); `Delivery Phases > 5. AI discovery surfaces` (`Keep persona consistency in one shared prompt-contract layer`). |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | `critical` | partial | `Product Breakdown` (`on-brand, spoiler-safe`); `Delivery Phases > 5. AI discovery surfaces`; `Cross-Cutting Decisions`. | The plan centralizes AI contracts, but it does not explicitly enumerate the shared guardrails that every surface must enforce. |
| PRD-087 | Make AI warm, joyful, and light in critique | `important` | partial | `Delivery Phases > 5. AI discovery surfaces` (`persona consistency`); `Testing Strategy` (`voice adherence`). | It covers consistency and quality checks, but it does not explicitly preserve the warm, joyful, lightly critical tone pillars. |
| PRD-088 | Structure Scoop as personal taste mini-review | `important` | partial | `Feature-By-Feature Plan > Scoop`; `Product Breakdown` (`AI contract layer`). | Scoop generation and persistence are planned, but the personal-taste mini-review structure is not explicitly specified. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | `important` | partial | `Feature-By-Feature Plan > Ask` (`one conversation engine and one voice contract`). | The plan does not explicitly commit to Ask responses being brisk and dialogue-like by default. |
| PRD-090 | Feed AI the right surface-specific context inputs | `important` | partial | `Product Breakdown` (systems share one consistent show model); `Delivery Phases > 5. AI discovery surfaces` (`stable collection/catalog backbone`). | The plan implies shared context inputs, but it does not explicitly enumerate which surface-specific inputs each AI surface receives. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | `important` | full | `Cross-Cutting Decisions` (`Real-show integrity is non-negotiable`, `golden-set harness`); `Testing Strategy` (`discovery quality bar`). |  |
| PRD-092 | Show person gallery, name, and bio | `important` | full | `Feature-By-Feature Plan > Person Detail` (`Support bio, gallery`); `Delivery Phases > 4. Show detail and person detail`. |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | `important` | partial | `Feature-By-Feature Plan > Person Detail` (`basic analytics`). | Analytics are planned, but ratings, genres, and projects-by-year are not explicitly named. |
| PRD-094 | Group filmography by year | `important` | full | `Feature-By-Feature Plan > Person Detail` (`year-grouped credits`). |  |
| PRD-095 | Open Show Detail from selected credit | `important` | full | `Feature-By-Feature Plan > Person Detail` (`credit selection re-enters the normal Show Detail pipeline`). |  |
| PRD-096 | Include font size and Search-on-launch settings | `important` | full | `Feature-By-Feature Plan > Search` (`Search on Launch`); `Feature-By-Feature Plan > Settings And Backup` (`Split local-only convenience settings`). |  |
| PRD-097 | Support username, model, and API-key settings safely | `important` | partial | `Delivery Phases > 6. Settings, backup, and hardening` (`API key/model configuration`); `Feature-By-Feature Plan > Settings And Backup`. | Model selection and API-key handling are planned, but username settings are not explicitly included. |
| PRD-098 | Export saved shows and My Data as zip | `critical` | full | `Domain Model And Data Responsibilities` (`.zip` containing JSON); `Feature-By-Feature Plan > Settings And Backup` (`Support export of all durable user data as a zipped JSON backup`). |  |
| PRD-099 | Encode export dates using ISO-8601 | `important` | full | `Domain Model And Data Responsibilities` (`ISO-8601 dates`); `Testing Strategy` (`export date encoding`). |  |

## 3. Coverage Scores

Overall formula:

`score = (56 × 1.0 + 41 × 0.5) / 99 × 100 = 77.3%`

Critical:  (19 × 1.0 + 11 × 0.5) / 30 × 100 = 81.7%  (24.5 of 30 critical requirements)
Important: (35 × 1.0 + 30 × 0.5) / 67 × 100 = 74.6%  (50.0 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   (56 × 1.0 + 41 × 0.5) / 99 × 100 = 77.3%  (76.5 of 99 total requirements)

## 4. Top Gaps

- PRD-009 | `critical` | Use one stable namespace per build. The plan enforces `(namespace_id, user_id)` partitioning, but if namespace lifetime is not fixed per build, benchmark runs can bleed into one another and destructive resets stop being trustworthy.
- PRD-025 | `critical` | Removing status deletes show and all My Data. The plan covers confirmation and removal semantics, but without an explicit full-data wipe contract, users can end up with ghost ratings, tags, or Scoop data after removal.
- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data. The merge engine is planned, but the re-add behavior is not named, which risks losing the user's prior relationship history when a show is saved again.
- PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract. The list string format is specified, but the enclosing response object is not, so the Ask UI contract could fail even if recommendation text is otherwise good.
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces. Persona consistency is planned, but without explicit shared guardrails, surfaces can diverge on spoiler safety, domain limits, honesty, and recommendation integrity.

## 5. Coverage Narrative

#### Overall Posture
The plan is structurally sound and covers most of the product's major systems, but it stops short of full benchmark readiness because several critical rules are handled by implication rather than explicit planning language. The weighted coverage score is 77.3%, which is solid for architecture and sequencing but still leaves meaningful execution risk in benchmark-isolation details and AI behavioral contracts.

#### Strength Clusters
The strongest coverage is in Collection Home & Search, Show Detail & Relationship UX, Person Detail, and Settings & Export. Those sections are concrete about feature sequencing, save triggers, detail-page ordering, export packaging, and the main user journeys, and they are reinforced by explicit testing coverage for merge rules, fallback behavior, and namespace scoping.

#### Weakness Clusters
The gaps cluster in two places. First, the benchmark runtime/isolation area is only partially specified on operational details such as official Supabase libraries, dev-auth documentation, stable namespace lifetime, disposable client cache expectations, and Docker-free cloud-agent compatibility. Second, the AI surface contracts are weaker on exactness than on intent: the plan captures shared infrastructure and quality goals, but several Ask, Scoop, concept-format, and voice requirements remain underspecified.

#### Risk Assessment
If this plan were executed as-is, the app would likely land the major pages and data model but still miss benchmark-critical edge behavior. A QA reviewer would probably notice the gaps first in namespace/test isolation rules and in AI interaction polish: chat starter prompts missing, Ask contracts not fully machine-safe, voice guardrails drifting across surfaces, and some removal/re-add data semantics behaving inconsistently under real use.

#### Remediation Guidance
The remaining planning work is mostly specification tightening, not a wholesale rewrite. The next revision should add explicit benchmark-operational acceptance criteria for env/gitignore/scripts/auth/isolation/cache behavior; make the save/remove/re-add lifecycle fully explicit; and turn the AI sections from high-level intent into exact contracts for response schemas, guardrails, voice pillars, concept formatting, and surface-specific context inputs.
