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
| PRD-001 | Use Next.js latest stable runtime | critical | full | §1 Technology Stack | |
| PRD-002 | Use Supabase official client libraries | critical | full | §1 Technology Stack | |
| PRD-003 | Ship `.env.example` with required variables | critical | full | §3 Environment Variable Interface | |
| PRD-004 | Ignore `.env*` secrets except example | important | full | §2 Repository Layout; §3 Environment Variable Interface | |
| PRD-005 | Configure build through env without code edits | critical | full | §3 Environment Variable Interface | |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | §3 Environment Variable Interface | |
| PRD-007 | Provide app, test, reset command scripts | critical | full | §12 Scripts & One-Command DX | |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | §1 Technology Stack; §11 Migrations & Data Continuity | |
| PRD-009 | Use one stable namespace per build | critical | full | §3 NAMESPACE_ID; §5.1 Namespace resolution | |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | §4 Data Model; §5.4 Destructive testing | |
| PRD-011 | Attach every user record to `user_id` | critical | full | §4 Data Model | |
| PRD-012 | Partition persisted data by namespace and user | critical | full | §4 Data Model; §4.2 RLS Policy Shape | |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | §3 Dev identity injection; §5.2 User resolution | |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | §5.2 User resolution | |
| PRD-015 | Keep backend as persisted source of truth | critical | full | §0 Guiding Principles; §4 Data Model | |
| PRD-016 | Make client cache safe to discard | critical | full | §0 Guiding Principles; §4.3 | |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | §0 Guiding Principles; §18 Compliance Check | |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | §0 Guiding Principles; §9.1 Tile indicator badges | |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | §4.1 `shows.my_status`; §16 Open Questions | |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | §7.2 Auto-save & defaults | |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | §7.4 Tag library | |
| PRD-022 | Define collection membership by assigned status | critical | full | §7.2 Auto-save & defaults | |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | §7.2 Auto-save & defaults | |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | §7.2 Auto-save & defaults | |
| PRD-025 | Removing status deletes show and all My Data | critical | full | §7.2 Auto-save & defaults; §10 DELETE `/api/my-data/[externalId]` | |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | §7.1 Merge rules; §7.2 Clearing status → delete row | Merge logic refreshes existing rows, but the plan never defines a tombstone or restore path that would preserve cleared My Data when the same show is added again. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | §4.1 `shows` columns; §7.2 update-date rules | |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | full | §7.1 Merge rules; §7.2 update-date rules; §8.5 Scoop persistence | |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | §8.5 Scoop persistence | |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | §8.2 surface contracts table; §8.4 Chat context management | |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | §8.3 Show reference resolution | |
| PRD-032 | Show collection and rating tile indicators | important | full | §9.1 Tile indicator badges | |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | full | §4.1 unique constraint; §4.1 settings tables; §7.1 Merge rules | |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | §11 Migrations & Data Continuity | |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | §4.1 `user_settings`; §4.1 `local_ui_state` | |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | §4.1 `provider_data`; §6 External Catalog Layer | |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | §7.1 Merge rules | |
| PRD-038 | Provide filters panel and main screen destinations | important | full | §2 Repository Layout; §9 Feature Implementation Map | |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | §2 Repository Layout; §9 Feature Implementation Map | |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | §2 Repository Layout; §9 Feature Implementation Map | |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | §2 Repository Layout; §9 Feature Implementation Map | |
| PRD-042 | Show only library items matching active filters | important | full | §7.3 Filters & grouping | |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | §7.3 Filters & grouping | |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | §7.3 Filters & grouping | |
| PRD-045 | Render poster, title, and My Data badges | important | full | §2 `components/show`; §9.1 Tile indicator badges | |
| PRD-046 | Provide empty-library and empty-filter states | detail | partial | §17 Phase 7 "Error states, empty states, loading skeletons" | The plan promises generic empty states but does not spell out distinct empty-library and empty-filter behaviors. |
| PRD-047 | Search by title or keywords | important | full | §6 External Catalog Layer; §9 Search | |
| PRD-048 | Use poster grid with collection markers | important | full | §9 Search | |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | §9 Search note; §4.1 `local_ui_state.auto_search` | |
| PRD-050 | Keep Search non-AI in tone | important | full | §8.1 Shared persona: "Search never invokes AI" | |
| PRD-051 | Preserve Show Detail narrative section order | important | full | §17 Phase 3 "Detail page sections in PRD order" | |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | §2 `components/detail` HeaderMedia; §6 External Catalog Layer includes videos | The plan includes header media and video inputs, but it does not explicitly commit to motion-first presentation with graceful non-video fallback behavior. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | §17 Phase 3; §9 Show Detail map | |
| PRD-054 | Place status/interest controls in toolbar | important | full | §9 Show Detail map; §2 `components/show` | |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | §7.2 Auto-save & defaults | |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | §7.2 Auto-save & defaults | |
| PRD-057 | Show overview early for fast scanning | important | full | §17 Phase 3 "Detail page sections in PRD order" | |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | §8.5 Scoop persistence; §8.2 surface contracts | Streaming and TTL are planned, but the detail-page state machine for no-scoop, cached, and open labels plus explicit "Generating…" feedback is not specified. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | §9 Ask about a show; §10 POST `/api/ai/ask` | |
| PRD-060 | Include traditional recommendations strand | important | full | §9 Show Detail map | |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | §9 Explore Similar; §17 Phase 5 | |
| PRD-062 | Include streaming availability and person-linking credits | important | full | §9 Show Detail map; §9 Person Detail | |
| PRD-063 | Gate seasons to TV and financials to movies | important | partial | §9 Show Detail map lists SeasonsList and BudgetRevenue | The plan names both sections but does not explicitly state the media-type gating logic that keeps seasons TV-only and financials movie-only. |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | §1 Technology Stack note; §17 Phase 3 | |
| PRD-065 | Provide conversational Ask chat interface | important | full | §9 Ask | |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | §8.1 Shared persona; §8.2 Ask contract | |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | §2 `components/discover` MentionedShowsStrip; §9 Ask | |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | §8.3 Show reference resolution; §9 Ask | |
| PRD-069 | Show six random starter prompts with refresh | important | full | §9 Ask | |
| PRD-070 | Summarize older turns while preserving voice | important | full | §8.4 Chat context management | |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | §9 Ask about a show | |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | §8.2 "Ask w/ Mentions" structured output; §13.1 AI parsers | The plan commits to `{commentary, showList}` but never specifies the exact required `Title::externalId::mediaType;;...` string contract or parser compatibility guarantees. |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | §8.2 Surface contracts; §13.2 Contract tests | |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | §8.1 Shared persona | |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | §8.2 Concepts surface; §17 Phase 5 | The plan schedules concept generation work, but it does not explicitly lock concepts to taste ingredients rather than genre labels. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | §8.2 Concepts surface; §8.6 concept validator | |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | §8.6 diversity heuristic | The plan guards against generic concepts, but it does not explicitly require ordering concepts by strongest "aha" first across varied axes. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | §9 Explore Similar; §17 Phase 5 | The interaction requires concept selection, but the plan never specifies the user guidance copy that teaches people to pick ingredients they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | §8.2 surface contracts; §9 Explore Similar | |
| PRD-080 | Support full Alchemy loop with chaining | important | full | §9 Alchemy; §17 Phase 5 | |
| PRD-081 | Clear downstream results when inputs change | important | full | §9 Alchemy; §17 Phase 5 | |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | §8.2 Concepts (multi); §9 Alchemy | |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | §8.2 Recs; §9 Explore Similar | |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | §8.6 Quality gate; §13.4 Golden set hook | The plan protects real-show integrity, but it does not define how recommendation quality will be steered toward surprising but still defensible taste-aligned results. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | §0 Guiding Principles; §8.1 Shared persona | |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | §8.1 Shared persona | |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | §8.1 Shared persona | |
| PRD-088 | Structure Scoop as personal taste mini-review | important | partial | §8.2 Scoop surface contract | The plan sizes and parses Scoop output, but it does not explicitly require the full mini-review structure of personal take, stack-up, fit or warnings, and verdict. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | §8.2 Ask contract | |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | §8.2 Surface contracts; §10 AI routes | The plan names the AI surfaces, but it does not enumerate the exact surface-specific context payloads each prompt must receive from library data, My Data, concepts, and recent turns. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | §8.6 Quality gate; §13.4 Golden set hook | The plan hard-fails real-show integrity, but it stops short of planning the rubric-based validation loop for voice, taste alignment, and specificity. |
| PRD-092 | Show person gallery, name, and bio | important | full | §2 `components/person`; §9 Person Detail | |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | partial | §2 `components/person` AnalyticsCharts; §17 Phase 6 | The plan includes generic analytics charts, but it does not explicitly commit to ratings, genres, and projects-by-year as the required chart set. |
| PRD-094 | Group filmography by year | important | full | §2 `components/person` FilmographyByYear; §17 Phase 6 | |
| PRD-095 | Open Show Detail from selected credit | important | full | §9 Person Detail | |
| PRD-096 | Include font size and Search-on-launch settings | important | full | §4.1 `local_ui_state`; §9 Settings | |
| PRD-097 | Support username, model, and API-key settings safely | important | full | §4.1 `user_settings`; §9 Settings | |
| PRD-098 | Export saved shows and My Data as zip | critical | full | §9 Export; §10 GET `/api/export` | |
| PRD-099 | Encode export dates using ISO-8601 | important | full | §2 `lib/export/zip.ts`; §9 Export; §10 GET `/api/export` | |

## 3. Coverage Scores

Critical:  (28 × 1.0 + 2 × 0.5) / 30 × 100 = 96.67%  (29.0 of 30 critical requirements)
Important: (56 × 1.0 + 11 × 0.5) / 67 × 100 = 91.79%  (61.5 of 67 important requirements)
Detail:    (1 × 1.0 + 1 × 0.5) / 2 × 100 = 75.00%  (1.5 of 2 detail requirements)
Overall:   (85 × 1.0 + 14 × 0.5) / 99 × 100 = 92.93%  (99 total requirements)

## 4. Top Gaps

1. PRD-026 | `critical` | Re-add preserves My Data and refreshes public data — The plan defines hard deletion on status clear but no restore or tombstone path, so a user who removes and later re-saves a title could lose their tags, rating, and Scoop history.
2. PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract — Ask can only power the mentioned-shows strip if the parser and AI output share the exact delimiter contract, and that exact format is not planned.
3. PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity — The plan enforces real-show resolution, but without the broader rubric loop it can still regress on voice, taste alignment, and specificity without a clear acceptance gate.
4. PRD-090 | `important` | Feed AI the right surface-specific context inputs — If prompts are not explicitly wired with the right library, My Data, concept, and session inputs per surface, the AI will feel generic even if the infrastructure is correct.
5. PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations — The product’s differentiator is "surprise without betrayal"; without an explicit plan for that behavior, recommendations can become safe, obvious, or weakly justified.

## 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with a high level of structural coverage. It is ready to guide build work on infra, persistence, routing, and the main product surfaces, but it still leaves several behavior-level requirements underspecified, especially around AI contracts and one critical collection lifecycle edge case.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, App Navigation & Discover Shell, and Settings & Export. Those areas get concrete tables, route structure, schema decisions, isolation mechanics, scripts, and migration strategy rather than vague intent, which makes them implementation-ready.

#### Weakness Clusters

The remaining gaps cluster around AI behavior specification rather than page scaffolding. Concepts, Explore Similar & Alchemy plus AI Voice, Persona & Quality are the weakest areas because the plan often creates a prompt module or validator without pinning down the exact output contracts, context payloads, ranking rules, or quality rubric that the PRD treats as product-defining behavior. A smaller but important second cluster sits in Show Detail micro-behavior and the collection re-add lifecycle.

#### Risk Assessment

If this plan were executed as-is, the app would likely ship with solid infrastructure and mostly correct screens, but it would miss parity in the places users notice the product’s "taste brain." QA would first see this in Ask mentions that do not reliably hydrate into the strip, in concept/recommendation output that feels generic, and in a remove-then-re-add flow that does not preserve prior My Data the way the catalog requires.

#### Remediation Guidance

The missing work is mostly not new architecture; it is sharper behavioral planning. The plan needs explicit acceptance criteria for AI schemas, exact mention formatting, surface-specific prompt inputs, concept ranking and guidance copy, and quality validation beyond ID resolution. It also needs one concrete persistence decision for re-add semantics so the delete flow and continuity rules do not conflict at implementation time.
