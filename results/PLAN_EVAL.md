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
| PRD-001 | Use Next.js latest stable runtime | critical | full | 2.1 Technology Stack |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 2.1 Technology Stack |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Phase 1: Infrastructure Setup |  |
| PRD-004 | Ignore `.env*` secrets except example | important | missing | none | The plan never mentions a .gitignore rule to exclude .env secrets while keeping .env.example. |
| PRD-005 | Configure build through env without code edits | critical | full | 2.1 Technology Stack plus Phase 1: Infrastructure Setup |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | partial | 2.1 Technology Stack plus Phase 5: Settings Page | It uses environment variables, but it does not define the server-only boundary for elevated keys or safe client exposure rules. |
| PRD-007 | Provide app, test, reset command scripts | critical | partial | 2.3 Destructive Testing plus Phase 5: Review & Refine | Reset is planned, but the start-app and run-tests scripts are not explicitly part of the deliverable set. |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | Phase 1: Infrastructure Setup |  |
| PRD-009 | Use one stable namespace per build | critical | full | 2.3 Identity & Run Isolation |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 2.3 Identity & Run Isolation |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 2.3 Identity & Run Isolation |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 2.3 Identity & Run Isolation plus 2.4 Data Model & Persistence |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | 2.3 Auth Strategy | The dev identity mechanism is named, but documentation and explicit production gating are not part of the plan. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 2.3 Auth Strategy |  |
| PRD-015 | Keep backend as persisted source of truth | critical | partial | 2.4 Data Model & Persistence plus Phase 1: Data Layer | Supabase persistence is central, but the plan never states that client-local state is disposable and subordinate to the backend. |
| PRD-016 | Make client cache safe to discard | critical | missing | none | No cache or local-storage strategy is described to prove users can clear client state without losing persisted data. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | 2.1 Technology Stack | The stack mentions hosted or local Supabase, but it never explicitly rules out Docker or treats it as optional. |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | 2.4 Merge Strategy | The merge strategy favors user data, but the plan does not say every appearance of a show must render the overlaid saved version. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | 3.1 Status & Auto-Save Rules | Visible statuses are planned, but the hidden data-model support for Next is omitted. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | partial | 2.4 Data Model & Persistence plus Phase 2: Home Collection | Tags exist, but the plan does not define the free-form tag-library behavior beyond storing arrays and filtering by tags. |
| PRD-022 | Define collection membership by assigned status | critical | partial | 3.1 Status & Auto-Save Rules | The plan uses status-driven save rules, but it does not explicitly define collection membership as assigned status. |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | 2.4 Merge Strategy | Merge behavior is described broadly, but the re-add flow is not called out as preserving prior My Data while refreshing public metadata. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 2.4 Data Model & Persistence |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | 2.4 Data Model & Persistence plus 3.2 AI Data Persistence | Timestamps are used for conflicts and Scoop freshness, but sorting and broader sync/freshness behavior are not specified. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 3.2 AI Data Persistence & Freshness |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | 3.2 AI Data Persistence & Freshness |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | Phase 3: RecommendationService plus AI Hallucinations edge case |  |
| PRD-032 | Show collection and rating tile indicators | important | partial | Phase 4: Search identifies in-collection items | Search marks collection status, but rating badges and consistent tile indicators across surfaces are not planned. |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 2.4 Data Model & Persistence plus Merge Strategy | Timestamped merges are covered, but cross-device sync behavior and duplicate detection/merging are not. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | Data Migrations edge case |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | partial | 2.4 Settings Table plus Phase 5: Settings Page | Some settings fields are planned, but persisted local settings and UI state like filter memory and removal-confirmation flags are missing. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | missing | none | The plan does not specify that provider IDs are persisted while detail-heavy fields stay transient and re-fetchable. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 2.4 Merge Strategy |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 2.2 Project Structure plus Phase 2 and Phase 5 page plans |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | 2.2 Project Structure plus Phase 4: Discovery Surfaces | The plan defines Find routes and modes, but it never says Find remains in persistent primary navigation. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | Phase 5: Settings Page | A Settings page exists, but the plan does not place it in persistent primary navigation. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | Phase 4: Discovery Surfaces |  |
| PRD-042 | Show only library items matching active filters | important | partial | Phase 2: Home Collection | Filtering is implied, but the plan does not specify that Home shows only items matching the active filter set. |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | Phase 2: Home Collection |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | partial | Phase 2: Home Collection | Only All and tag-based views are explicit; genre, decade, community-score, and media-type controls are not planned. |
| PRD-045 | Render poster, title, and My Data badges | important | partial | Phase 2: Home Collection | The grid is planned, but poster/title rendering and My Data badges are not specified as tile requirements. |
| PRD-046 | Provide empty-library and empty-filter states | detail | missing | none | No empty-library or empty-filter state behavior is planned. |
| PRD-047 | Search by title or keywords | important | full | Phase 4: Find Hub - Search |  |
| PRD-048 | Use poster grid with collection markers | important | partial | Phase 4: Find Hub - Search | Collection markers are covered, but poster-grid presentation is not explicitly planned. |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | 2.4 Settings Table | The plan stores autoSearch, but it does not define the launch behavior that opens Search when enabled. |
| PRD-050 | Keep Search non-AI in tone | important | partial | Phase 4: Find Hub - Search | Search is modeled as catalog search, but the plan does not explicitly protect it from AI voice or tone bleed. |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | Phase 2: Show Detail Page | The detail page is named at a high level, but the required section-by-section narrative order is not preserved in the plan. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | Phase 2: Show Detail Page | Header media is planned, but motion-first behavior and graceful poster/backdrop fallback are not described. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | partial | Phase 2: Show Detail Page | Core facts are mentioned, but the plan does not explicitly surface year, runtime/seasons, and community score early together. |
| PRD-054 | Place status/interest controls in toolbar | important | partial | Phase 2: Show Detail Page | Status chips exist, but the plan does not place status and interest controls in the toolbar. |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 3.1 Status & Auto-Save Rules |  |
| PRD-057 | Show overview early for fast scanning | important | missing | none | The plan omits overview placement and the fast-scan behavior expected near the top of the page. |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | Phase 3: ScoopService plus 3.2 AI Data Persistence | It supports streaming Scoop output and freshness, but not the detailed cached/open/generating states from the spec. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | missing | none | No Ask-about-this-show entry point or seeded handoff from detail is planned. |
| PRD-060 | Include traditional recommendations strand | important | missing | none | The detail-page recommendations strand is missing from the implementation breakdown. |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | Phase 4: Explore Similar |  |
| PRD-062 | Include streaming availability and person-linking credits | important | missing | none | Streaming availability and cast/crew links from detail are not planned. |
| PRD-063 | Gate seasons to TV and financials to movies | important | missing | none | The plan never gates seasons to TV and financials to movies. |
| PRD-064 | Keep primary actions early and page not overwhelming | important | missing | none | The plan does not address action clustering or the anti-overwhelm layout guidance. |
| PRD-065 | Provide conversational Ask chat interface | important | full | Phase 4: Find Hub - Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | Phase 3: Prompt Engineering plus ChatService | The persona is spoiler-safe, but the plan does not require direct answers and confident recommendation delivery in Ask. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | Phase 4: Find Hub - Ask |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | partial | Phase 4: Find Hub - Ask plus AI Hallucinations edge case | Mention resolution and search fallback are covered, but opening detail directly from the strip is not stated. |
| PRD-069 | Show six random starter prompts with refresh | important | missing | none | Starter prompts and refresh behavior are not planned. |
| PRD-070 | Summarize older turns while preserving voice | important | partial | Phase 3: ChatService | Older-turn summarization is planned, but persona-preserving summary behavior is not. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | missing | none | No show-context handoff is planned for Ask-about-show sessions. |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | Phase 3: ChatService | The plan mentions a structured showList payload, but it omits the exact commentary plus serialized showList contract the UI/parser depends on. |
| PRD-073 | Retry malformed mention output once, then fallback | important | partial | AI Hallucinations edge case | Fallback handling exists, but the exact retry-once contract for malformed structured mention output is missing. |
| PRD-074 | Redirect Ask back into TV/movie domain | important | partial | Phase 3: Prompt Engineering | The plan stays in a TV/movie app context, but it does not specify redirect behavior for out-of-domain prompts. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | Phase 3: ConceptService plus Alchemy | Concepts are described as evocative ingredients, but the plan never explicitly distinguishes them from genres or plot categories. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | partial | Phase 3: ConceptService | The 1-3 word bullet format is planned, but the non-generic quality bar is not. |
| PRD-077 | Order concepts by strongest aha and varied axes | important | missing | none | No ordering or cross-axis diversity rule is planned for concept output. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | Phase 4: Explore Similar plus Alchemy | Concept selection exists, but the plan does not guide ingredient picking or define the required selection gate. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | missing | none | The Explore Similar flow omits the exact five-recommendation output contract. |
| PRD-080 | Support full Alchemy loop with chaining | important | partial | Phase 4: Alchemy | The core Alchemy steps are present, but the chaining loop for another round is not. |
| PRD-081 | Clear downstream results when inputs change | important | missing | none | The plan does not clear downstream concepts/results when inputs change. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | Phase 4: Alchemy | Shared multi-show concepts are implied, but the larger option pool for multi-show generation is not planned. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | partial | Phase 3: RecommendationService | Recommendation reasons exist, but the plan does not require them to cite selected concepts explicitly. |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | Phase 3: RecommendationService | Library/context-aware recs are planned, but the surprise-without-betrayal quality bar is not. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | Phase 3: Prompt Engineering |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | Phase 3: Prompt Engineering | Spoiler-safe persona work is planned, but the shared cross-surface guardrail contract is not fully defined. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | Phase 3: Prompt Engineering | Warmth is present, but the joy-forward and light-in-critique voice requirements are not fully specified. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | partial | Phase 3: ScoopService | Scoop is a mini-blog review, but the required personal-take, stack-up, fit, and verdict structure is missing. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | Phase 4: Find Hub - Ask plus ChatService | Ask exists, but the brisk dialogue-first default response behavior is not specified. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | Phase 3 AI services | The plan names some inputs per service, but it does not comprehensively define the surface-specific context bundle each AI feature needs. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | missing | none | No discovery quality rubric or hard-fail integrity validation plan is included. |
| PRD-092 | Show person gallery, name, and bio | important | partial | Phase 2: Person Detail Page | The person page covers stats and filmography, but gallery, name, and bio are not specified. |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | partial | Phase 2: Person Detail Page | Analytics are implied by "stats," but ratings, genres, and projects-by-year analytics are not concretely planned. |
| PRD-094 | Group filmography by year | important | full | Phase 2: Person Detail Page |  |
| PRD-095 | Open Show Detail from selected credit | important | missing | none | The plan does not state that selecting a credit opens Show Detail. |
| PRD-096 | Include font size and Search-on-launch settings | important | partial | Phase 5: Settings Page plus 2.4 Settings Table | Font size is explicit, but Search-on-launch only appears as storage and not as a defined user setting flow. |
| PRD-097 | Support username, model, and API-key settings safely | important | partial | 2.4 Settings Table plus Phase 5: Settings Page | Several settings fields exist in storage, but the plan omits username and model selection in the Settings UI and does not spell out safe handling. |
| PRD-098 | Export saved shows and My Data as zip | critical | full | Phase 5: Export |  |
| PRD-099 | Encode export dates using ISO-8601 | important | missing | none | The export plan does not specify ISO-8601 date encoding. |

## 3. Coverage Scores

Critical:  (21 x 1.0 + 8 x 0.5) / 30 x 100 = 83.3%  (25.0 of 30 critical requirements)
Important: (11 x 1.0 + 40 x 0.5) / 67 x 100 = 46.3%  (31.0 of 67 important requirements)
Detail:    (0 x 1.0 + 1 x 0.5) / 2 x 100 = 25.0%  (0.5 of 2 detail requirements)
Overall:   (32 x 1.0 + 49 x 0.5) / 99 x 100 = 57.1%  (56.5 of 99 total requirements)

## 4. Top Gaps

1. PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract - Without the exact commentary/showList wire contract, the Ask UI cannot reliably render selectable mentioned shows or recover deterministically when parsing fails.
2. PRD-016 | `critical` | Make client cache safe to discard - If cache/local state disposal is undefined, users can lose trust in persistence after a reinstall, storage clear, or browser reset.
3. PRD-006 | `critical` | Keep secrets out of repo and server-only - Missing secret-boundary planning creates a path to leaking elevated credentials or baking unsafe key usage into the client architecture.
4. PRD-015 | `critical` | Keep backend as persisted source of truth - If backend authority is not explicit, optimistic UI and local state can drift into correctness bugs that resets and sync flows will expose quickly.
5. PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces - Shared AI guardrails are the only thing keeping Scoop, Ask, Explore Similar, and Alchemy aligned on spoilers, scope, honesty, and real-show actionability.

## 5. Coverage Narrative

#### Overall Posture
This is a structurally sound plan with concerning holes. It covers the benchmark baseline, the core persistence model, and the main discovery surfaces at a credible high level, but too many experience-level and AI-contract details remain partial or missing for this to be a faithful rebuild plan yet.

#### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, and App Navigation & Discover Shell. It gives concrete direction on Next.js plus Supabase, namespace and user partitioning, migrations, save/remove rules, timestamped merge behavior, and the existence of Search, Ask, and Alchemy as distinct surfaces.

#### Weakness Clusters
The gaps concentrate in important-tier product behavior rather than core platform setup. Show Detail & Relationship UX and Concepts, Explore Similar & Alchemy carry the highest density of missing items, while Ask and broader AI quality requirements are often present only as service names rather than exact interaction contracts, output formats, or acceptance bars.

#### Risk Assessment
If this plan were executed as-is, the app would likely ship as a competent technical scaffold that misses parity on the product's feel and deterministic behavior. A stakeholder or QA reviewer would notice first that Show Detail lacks the required narrative hierarchy and deep-link flows, and that Ask or concept-driven discovery lacks the exact parser contracts, starter states, and quality rules that make the current product actionable and trustworthy.

#### Remediation Guidance
The next planning pass should add view-level acceptance criteria and exact AI behavior contracts, not replace the architecture. Tighten the plan around detail-page section ordering, empty and filter states, Ask starter and handoff flows, exact showList formatting and fallback rules, concept counts and reset behavior, and the persistence matrix for cache disposal, settings, and transient versus stored data.
