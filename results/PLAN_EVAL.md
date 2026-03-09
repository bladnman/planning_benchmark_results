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

### 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | 2.1 Runtime and platform |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 2.1 Runtime and platform |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 11. Developer Experience and Repo Deliverables |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | 11. Developer Experience and Repo Deliverables |  |
| PRD-005 | Configure build through env without code edits | critical | full | 5.1 Namespace model; 11. Developer Experience and Repo Deliverables |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 5.1 Namespace model; 5.3 Security and migration path; 11. Developer Experience and Repo Deliverables |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 11. Developer Experience and Repo Deliverables |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 11. Developer Experience and Repo Deliverables; Phase 0: Foundation |  |
| PRD-009 | Use one stable namespace per build | critical | full | 5.1 Namespace model |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 5.1 Namespace model; 10.2 Integration tests; 11. Developer Experience and Repo Deliverables |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 4.2 Primary tables; 5.2 User identity model |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 4.2 Primary tables; 5.2 User identity model |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | 5.2 User identity model | The plan defines the mechanism, but it never explicitly commits to documenting the dev-auth path. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 5.3 Security and migration path |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | 2.1 Runtime and platform |  |
| PRD-016 | Make client cache safe to discard | critical | full | 2.1 Runtime and platform |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | 11. Developer Experience and Repo Deliverables | The plan is cloud-agent compatible in spirit, but it never explicitly states that Docker is optional or not required. |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | 6.3 Overlay everywhere |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | 1. Scope and Delivery Goal | The plan defers first-class Next UI, but it never explicitly preserves hidden Next support in the data model. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 4.4 Save and removal rules; 8.3 Show Detail |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 4.2 Primary tables; 8.1 Collection Home; 8.3 Show Detail |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 4.4 Save and removal rules |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 4.4 Save and removal rules |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 4.4 Save and removal rules |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 4.4 Save and removal rules |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | 4.5 Merge policy | The plan explains refresh-and-merge behavior, but it does not explain how re-adding after removal still restores prior My Data. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 4.2 Primary tables |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | 4.3 Why this schema; 4.5 Merge policy; 7.3 Scoop | The plan uses timestamps for merge integrity and freshness, but it does not explicitly plan timestamp-driven sorting behavior. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 7.3 Scoop |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | 4.1 Persistence strategy; 9.2 Client session state |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 7.5 Concept recommendations |  |
| PRD-032 | Show collection and rating tile indicators | important | partial | 8.1 Collection Home; 8.2 Find Hub -> Search mode | The plan calls out in-collection badges, but it does not explicitly commit to a separate user-rating tile indicator everywhere tiles appear. |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 4.3 Why this schema; 4.5 Merge policy; 10.2 Integration tests | Timestamp-based consistency is planned, but duplicate detection and transparent merge behavior are not explicitly specified. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | 4.2 Primary tables; 11. Developer Experience and Repo Deliverables | The plan includes versioning and migrations, but it does not define a concrete continuity/backfill strategy for model upgrades. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | 4.2 Primary tables |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | 4.2 Primary tables; 6.2 Internal show normalization | The plan explicitly persists provider IDs, but it only implies that detail fetches stay transient and re-pullable. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 4.5 Merge policy |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 3.1 Route map; 8.1 Collection Home |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | 3.1 Route map; 8.2 Find Hub | The plan names Find as a destination, but it does not explicitly commit to persistent primary-navigation shell behavior. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | 3.1 Route map; 8.5 Settings and Export | The plan names Settings as a route, but it does not explicitly commit to persistent primary-navigation shell behavior. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 3.1 Route map; 8.2 Find Hub |  |
| PRD-042 | Show only library items matching active filters | important | full | 8.1 Collection Home; 10.3 End-to-end tests |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 8.1 Collection Home |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | partial | 8.1 Collection Home | The plan covers tag, genre, decade, score, and media filters, but it never explicitly calls out the default All view. |
| PRD-045 | Render poster, title, and My Data badges | important | partial | 8.1 Collection Home | The plan names a ShowTile component, but it does not explicitly specify poster, title, and My Data badges together as tile anatomy. |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 8.1 Collection Home |  |
| PRD-047 | Search by title or keywords | important | partial | 8.2 Find Hub -> Search mode | The plan commits to live text search, but it does not explicitly specify title-or-keyword matching semantics. |
| PRD-048 | Use poster grid with collection markers | important | full | 8.2 Find Hub -> Search mode |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | Phase 3: Detail depth and person navigation; 8.5 Settings and Export |  |
| PRD-050 | Keep Search non-AI in tone | important | partial | 8.2 Find Hub -> Search mode | Search is separated from Ask, but the plan never explicitly protects Search from AI voice/persona drift. |
| PRD-051 | Preserve Show Detail narrative section order | important | full | 8.3 Show Detail |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | 8.3 Show Detail | The plan includes a header media carousel, but it does not explicitly prioritize motion or define graceful fallback behavior. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 8.3 Show Detail |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 8.3 Show Detail |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 4.4 Save and removal rules; 8.3 Show Detail |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 4.4 Save and removal rules; 8.3 Show Detail |  |
| PRD-057 | Show overview early for fast scanning | important | full | 8.3 Show Detail |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | 7.3 Scoop |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 7.2 Ask; 8.3 Show Detail |  |
| PRD-060 | Include traditional recommendations strand | important | full | 8.3 Show Detail |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | partial | 7.4 Concepts; 8.3 Show Detail | The plan includes Explore Similar, but it does not explicitly lay out the CTA-first Get Concepts -> select -> Explore Shows flow. |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 8.3 Show Detail; 8.4 Person Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 8.3 Show Detail |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 8.3 Show Detail | The plan prioritizes early actions, but it does not translate the busyness-vs-power rule into concrete page-level constraints. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 7.2 Ask; 8.2 Find Hub -> Ask mode |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | 7.1 Shared AI rules | The plan covers spoiler safety and opinionated tone, but it does not explicitly require direct answers in the first lines or confident Ask-style formatting. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 8.2 Find Hub -> Ask mode |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 7.5 Concept recommendations; 10.3 End-to-end tests |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 7.2 Ask |  |
| PRD-070 | Summarize older turns while preserving voice | important | partial | 7.2 Ask | The plan includes summarization, but it does not require summaries to preserve the same persona and voice. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 7.2 Ask; 8.3 Show Detail |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | 7.2 Ask |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 7.2 Ask |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | 7.1 Shared AI rules |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | 7.4 Concepts | The plan treats concepts as specific and diverse, but it never explicitly frames them as taste ingredients rather than genres. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 7.4 Concepts; 10.5 AI quality validation |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | 7.4 Concepts | The plan asks for concept diversity, but it does not explicitly require ordering by strongest aha first. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 8.2 Find Hub -> Alchemy mode; 10.1 Unit tests | The plan covers selection mechanics, but it does not include the UX guidance that tells users to pick the ingredients they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 7.5 Concept recommendations |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 8.2 Find Hub -> Alchemy mode |  |
| PRD-081 | Clear downstream results when inputs change | important | partial | 10.1 Unit tests; Phase 5: Explore Similar and Alchemy | The plan mentions reset behavior, but it does not clearly state that changing upstream Alchemy inputs clears downstream concepts and results. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | 7.4 Concepts | The plan requires shared multi-show concepts, but it does not specify a larger option pool for multi-show generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | partial | 8.2 Find Hub -> Alchemy mode; 7.5 Concept recommendations | The plan includes reasons, but it does not explicitly require those reasons to name the selected concepts. |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 13.1 AI output variability | The plan references the quality bar, but it does not concretely require surprising-but-defensible, taste-aligned recommendation behavior. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | partial | 7.1 Shared AI rules | The plan uses shared AI infrastructure, but it does not explicitly state one consistent persona across Scoop, Ask, Explore Similar, and Alchemy. |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | 7.1 Shared AI rules |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | 7.1 Shared AI rules | The plan asks for a warm, opinionated voice, but it does not explicitly require joy-forward tone or staying light when critical. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | partial | 7.3 Scoop; 10.5 AI quality validation | The plan covers freshness and streaming, but it does not specify the required mini-review structure of personal take, stack-up, fit, and verdict. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | 7.2 Ask | The plan defines Ask as chat, but it does not explicitly require brisk, dialogue-like default responses. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | 7.1 Shared AI rules; 7.2 Ask | The plan introduces shared prompt/context infrastructure, but it does not spell out the full surface-specific context inputs. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | 10.5 AI quality validation; 13.1 AI output variability | The plan references the rubric, but it does not adopt it as an explicit scoring gate or make real-show integrity a hard-fail condition. |
| PRD-092 | Show person gallery, name, and bio | important | partial | 8.4 Person Detail | The plan covers hero image, name, and bio, but it does not explicitly plan the required person image gallery. |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 8.4 Person Detail |  |
| PRD-094 | Group filmography by year | important | full | 8.4 Person Detail |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 8.4 Person Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 8.5 Settings and Export |  |
| PRD-097 | Support username, model, and API-key settings safely | important | partial | 8.5 Settings and Export | The plan covers username and model selection, but it stops short of a concrete safe API-key settings flow and leans on environment guidance instead. |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 8.5 Settings and Export |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 8.5 Settings and Export |  |

### 3. Coverage Scores

Overall score = (64 × 1.0 + 35 × 0.5) / 99 × 100 = 82.3%

Critical:  (28 × 1.0 + 2 × 0.5) / 30 × 100 = 96.7%  (29.0 of 30 critical requirements)
Important: (34 × 1.0 + 33 × 0.5) / 67 × 100 = 75.4%  (50.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   (64 × 1.0 + 35 × 0.5) / 99 × 100 = 82.3%  (99 total requirements)

### 4. Top Gaps

- PRD-034 | critical | Preserve saved libraries across data-model upgrades. The plan includes versioning and migrations, but without an explicit continuity/backfill path a schema change could strand or drop user libraries.
- PRD-026 | critical | Re-add preserves My Data and refreshes public data. The plan covers merge behavior for saved shows, but it never explains how a removed show can be re-encountered without losing prior user overlay data.
- PRD-091 | important | Validate discovery with rubric and hard-fail integrity. The plan references the quality bar, but without explicit gates the team could ship recommendations that feel okay in demos yet fail taste alignment or real-show integrity.
- PRD-033 | important | Sync libraries/settings consistently and merge duplicates. The plan covers timestamps and persistence, but not explicit duplicate detection/merge flows, which risks silent data divergence across devices or sync events.
- PRD-085 | important | Keep one consistent AI persona across surfaces. The plan uses shared AI infrastructure, but it does not turn cross-surface persona consistency into a concrete acceptance criterion, so the product voice could drift surface by surface.

### 5. Coverage Narrative

#### Overall Posture

This is a structurally strong plan with high critical-path coverage and no completely unaddressed requirements, but it is not fully execution-ready yet. The biggest issue is not missing screens or architecture; it is that several product-defining behaviors are still specified at the principle level instead of the acceptance-criteria level.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Ask Chat, Show Detail & Relationship UX, and the core Collection Data & Persistence model. It gives concrete architecture, persistence boundaries, exact auto-save/removal rules, the Ask structured-output contract, and a near-complete Show Detail section order with the major flows called out explicitly.

#### Weakness Clusters

Most partials cluster in AI Voice, Persona & Quality and in Concepts, Explore Similar & Alchemy, where the plan names the features and contracts but often stops short of the exact behavioral bar from the supporting docs. A second cluster sits in persistence edge cases such as hidden Next support, re-add semantics, duplicate merge handling, and continuity across data-model upgrades.

#### Risk Assessment

If this plan were executed as-is, the team would likely deliver a product that works end-to-end on the happy path but feels softer than the PRD in the places stakeholders will notice first. QA and product review would most likely flag generic or inconsistent AI behavior, underspecified concept-recommendation quality, and ambiguity around what happens to saved data during re-add, sync, or schema migration edge cases.

#### Remediation Guidance

The remaining work is mostly specification hardening rather than architectural redesign. Tighten the plan by adding explicit acceptance criteria for AI tone, surface-specific context inputs, recommendation-quality gates, and CTA flows, then add concrete data-lifecycle rules for hidden statuses, re-add behavior, duplicate merges, and model-upgrade continuity so those edge cases are implemented intentionally instead of inferred during build-out.
