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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Title and 1. Architecture Overview - Built on Next.js latest stable |  |
| PRD-002 | Use Supabase official client libraries | critical | full | Title and 1. Architecture Overview - Built on Supabase as the persistence layer |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 2. Project Structure and 10.1 Environment Setup |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | 2. Project Structure - .gitignore includes .env* | The plan never says .gitignore should exclude .env* while explicitly keeping .env.example committed. |
| PRD-005 | Configure build through env without code edits | critical | full | 1. Key Architectural Decisions - Environment-driven configuration |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 1. Key Architectural Decisions on server-only AI/catalog keys and 10.1 server-only service role |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 2. Project Structure scripts/ and 10.2 Developer Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 2. Project Structure supabase/migrations/ and 10.3 Migration Workflow |  |
| PRD-009 | Use one stable namespace per build | critical | full | 4. Identity & Isolation Model > Namespace Isolation |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 4. Namespace Isolation and 11.1 Namespace-Scoped Testing |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 3.1 shows table and 3.2 cloud_settings table both include user_id |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 3.4 Row-Level Security and composite uniqueness on namespace/user data |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | 4. User Identity - X-User-Id/default user gated behind NODE_ENV |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 4. OAuth Migration Path |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | 1. Key Architectural Decisions and 9.1 Cross-Device Sync |  |
| PRD-016 | Make client cache safe to discard | critical | full | 9.1 Cross-Device Sync - cache is safe to clear |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | 12. Phase 1 - Set up Supabase project hosted or local | The plan permits hosted Supabase, but it never states Docker must remain optional for the benchmark path. |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | 7.2 Search annotates collection membership and 9.2 Tile Indicators | The plan covers collection and rating badges, but it does not require the user's full saved overlay to win everywhere a show appears. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | 3.1 shows table includes next and 9.5 Filter System lists status next | The schema includes Next, but the plan never defines it as hidden/non-primary and even surfaces it in the filter model. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 5.2 Save Triggers & Defaults and 7.5 Section 3 Status Toolbar |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 6.2 Tag API, 7.5 Section 4 My Tags, and 12. Phase 2 Tag system |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 5.1 Collection Membership |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 5.2 Save Triggers & Defaults |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 5.2 Save Triggers & Defaults |  |
| PRD-025 | Removing status deletes show and all My Data | critical | partial | 5.4 Removal and 6.1 DELETE /api/shows/[id] | The removal flow clears fields but does not commit to deleting the stored show record when status is removed. |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | 5.5 Re-Adding a Previously Removed Show | Re-add logic assumes a record still exists, so it never explains how removed shows retain My Data and refresh catalog fields correctly. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 3.1 shows table timestamps and 5.6 Timestamp Tracking |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | 5.6 Timestamp Tracking and 9.1 Cross-Device Sync | Timestamp usage is defined for sync and freshness, but not for user-facing sorting where the PRD expects it. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | partial | 7.5 Section 5 Overview + Scoop and 8.6 Streaming | The 4-hour cache is covered, but the plan does not limit persisted Scoop storage to shows already in the collection. |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | 7.3 Ask says session context is cleared on page exit and 7.4 Alchemy flow is page-local | Ask is session-scoped, but Alchemy state is never explicitly marked session-only and disposable on exit. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 6.4 AI API recommend endpoint and 8.5 Quality Bar real-show integrity gate |  |
| PRD-032 | Show collection and rating tile indicators | important | full | 9.2 Tile Indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 9.1 Cross-Device Sync and 6.1 POST /api/shows upsert by external_id | The plan covers timestamp conflict resolution, but not duplicate detection and transparent merging across synced libraries/settings. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | 3.3 app_metadata and 10.3 Migration Workflow | Migrations and versioning exist, but the plan does not define a continuity mechanism that guarantees safe carry-forward across model upgrades. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | 3.2 cloud_settings, 10.1 local settings, 5.4 removal UI state, and 9.5 lastSelectedFilter |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | 3.1 provider_data JSONB and 7.5 transient recommendations/cast/crew sections | Provider data is persisted, but the plan does not constrain it to provider IDs only while keeping detail fetch payloads transient. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 5.5 Re-Adding a Previously Removed Show and 5.6 Timestamp Tracking |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 2. Project Structure routes plus 7.1 Collection Home and 9.5 Filter System |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | 2. Project Structure includes /search, /ask, and /alchemy routes | The plan defines discover routes, but not a persistent Find/Discover navigation affordance visible across the app shell. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | 2. Project Structure includes /settings route | The plan defines a settings route, but not a persistent primary-navigation entry that keeps Settings always reachable. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 2. Project Structure and 7.2 to 7.4 Search, Ask, and Alchemy pages |  |
| PRD-042 | Show only library items matching active filters | important | full | 7.1 Collection Home - Filter selections narrow the visible set |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | partial | 7.1 Collection Home - grouped by status Active to Later to Wait to Done to Quit | The grouping model is status-based rather than the required Active, Excited, Interested, and Others buckets. |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 7.1 Collection Home and 9.5 Filter System |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | 12. Phase 2 - Show tile component with poster, title, rating badge, membership indicator |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | partial | 7.1 Collection Home - Empty state prompt to search or discover | The no-library empty state is present, but the distinct empty-filter/no-results state is not. |
| PRD-047 | Search by title or keywords | important | full | 7.2 Search - debounced text query to /api/search |  |
| PRD-048 | Use poster grid with collection markers | important | full | 7.2 Search - poster grid with membership annotations |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | 7.2 Search auto-focus if autoSearch is enabled and 7.7 Settings auto-search toggle | Auto-focus on the Search page is weaker than auto-opening Search on launch when the setting is enabled. |
| PRD-050 | Keep Search non-AI in tone | important | missing | none | The plan never states that Search must remain a plain catalog surface without AI voice or persona styling. |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | 7.5 Show Detail lists 13 sections in order | The plan approximates the detail flow, but it does not preserve the exact narrative ordering from the detail-page spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | 7.5 Section 1 Header Media Carousel with poster fallback | Fallback behavior is present, but the plan does not prioritize trailers or other motion-rich header media when available. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 7.5 Section 2 Core Facts + Community Score |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 7.5 Section 3 Status Toolbar |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 7.5 Section 4 My Tags and 5.2 Save Triggers & Defaults |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 7.5 Section 2 User rating display and 5.2 Save Triggers & Defaults |  |
| PRD-057 | Show overview early for fast scanning | important | full | 7.5 Section 5 Overview + Scoop |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | 7.5 Section 5 streams progressively and uses a 4-hour cache | Streaming and caching are covered, but the required closed/cached/open Scoop states and explicit Generating feedback are missing. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 7.5 Section 6 Ask about this show CTA |  |
| PRD-060 | Include traditional recommendations strand | important | full | 7.5 Section 8 Recommendations Strand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 7.5 Section 9 Explore Similar - button, concepts, then recommendations |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 7.5 Sections 10 to 12 for providers, cast, and crew |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 7.5 Section 13 Season/Financial Info |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 7.5 places status, tags, overview, Scoop, Ask, and Explore Similar near the top | Primary actions are early by implication, but the plan never states the anti-overwhelm strategy for keeping long-tail detail secondary. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 7.3 Ask - chat message list and input bar |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | 8.2 Ask prompt - conversational, warm, opinionated, spoiler-safe | The plan captures tone, but not the direct-answer quality bar that requires fast, confident recommendations at the top of the response. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | partial | 7.3 Ask - render as tappable show cards inline | Inline cards are not the required horizontal mentioned-shows strip from the Ask UX. |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 7.3 Ask - tap mentioned show to Detail with Search fallback |  |
| PRD-069 | Show six random starter prompts with refresh | important | partial | 7.3 Ask - 3-5 random starter prompts from a pool of ~80 | The plan changes the starter-prompt count and omits the refresh affordance required by the PRD. |
| PRD-070 | Summarize older turns while preserving voice | important | partial | 7.3 and 8.4 - older turns summarized to 1-2 sentences | Summarization is present for context control, but not for voice preservation; the summary could easily drift off-persona. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 7.5 Section 6 CTA and 7.3 body includes optional show context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | 7.3 Ask mentions structured showList output and 8.2 Ask prompt calls for structured output | The plan mentions structured output, but not the exact {commentary, showList} object and delimiter contract the UI depends on. |
| PRD-073 | Retry malformed mention output once, then fallback | important | partial | 7.3 Ask - parsing failure falls back to unstructured text plus Search | It falls back on parse failure, but skips the required single retry with stricter formatting before degrading gracefully. |
| PRD-074 | Redirect Ask back into TV/movie domain | important | missing | none | The plan never states that off-domain Ask prompts must be redirected back into TV/movie recommendations. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | 8.2 Concept prompt - evocative, specific, not generic | The prompt asks for specificity, but the plan never frames concepts as taste ingredients rather than genre labels. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 8.2 Concept prompt - return N short concept bullets, 1-3 words, not generic |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | 8.2 Concept prompt - ordered by strength | Strength ordering is covered, but cross-axis diversity across structure, vibe, emotion, and craft is not. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 7.4 Step 3 selects 1-8 concepts and 7.5 Section 9 selects 1+ concepts | Selection is required, but the plan omits the ingredient-picking guidance copy that explains how users should steer results. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 7.5 Section 9 - returns 5 recommendations with reasoning per selected concepts |  |
| PRD-080 | Support full Alchemy loop with chaining | important | partial | 7.4 Step 5 - Iterate or Save | The loop supports reruns, but not explicit chaining that turns prior Alchemy outputs into new inputs. |
| PRD-081 | Clear downstream results when inputs change | important | partial | 7.4 Step 5 lets users go back and rerun | Backtracking exists, but the plan never says upstream changes automatically clear downstream concepts and recommendation results. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | 7.4 Step 2 always returns 8 concept chips | The plan fixes Alchemy to eight concepts instead of explicitly giving multi-show flows a larger option pool than single-show flows. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 7.4 Step 4 and 7.5 Section 9 - reasons name matching concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | full | 8.2 Recommendation prompt - defensible surprises tied to selected concepts |  |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | 8.3 Voice & Personality - all AI surfaces share a consistent personality |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | 8.3 Voice & Personality, 8.5 Quality Bar, and 9.3 Spoiler Safety | The plan covers spoiler safety and quality checks, but not the full shared guardrail set across all AI surfaces. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | 8.3 Voice & Personality pillars |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 7.5 Section 5 and 8.2 Scoop prompt |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | 8.2 Ask prompt and 8.3 Ask mode |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 8.4 Context Building |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | 8.5 Quality Bar |  |
| PRD-092 | Show person gallery, name, and bio | important | partial | 7.6 Person Detail - profile header with photo, name, and bio | The plan provides a single profile photo, not the gallery treatment called for in the PRD. |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | partial | 7.6 analytics - number of shows in collection and average rating | The analytics omit genre breakdowns and projects-by-year views, covering only a smaller subset of the required signals. |
| PRD-094 | Group filmography by year | important | partial | 7.6 filmography grouped by role in a poster grid | The plan addresses filmography, but groups it by role instead of by year. |
| PRD-095 | Open Show Detail from selected credit | important | partial | 12. Phase 3 - Navigation between shows and people | The plan mentions navigation broadly, but never states that selecting a person credit should open Show Detail directly. |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 7.7 Settings local settings and 12. Phase 5 settings page |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 7.7 Cloud settings and 8.1 Provider Abstraction |  |
| PRD-098 | Export saved shows and My Data as zip | critical | partial | 6.6 Export API and 9.4 Data Export - JSON download | The export design produces JSON, not the required zip backup that packages saved shows and My Data together. |
| PRD-099 | Encode export dates using ISO-8601 | important | missing | none | The plan never specifies ISO-8601 encoding for exported dates. |

## 3. Coverage Scores

Critical:  (22 × 1.0 + 8 × 0.5) / 30 × 100 = 86.7%  (26.0 of 30 critical requirements)
Important: (35 × 1.0 + 29 × 0.5) / 67 × 100 = 73.9%  (49.5 of 67 important requirements)
Detail:    (0 × 1.0 + 2 × 0.5) / 2 × 100 = 50.0%  (1.0 of 2 detail requirements)
Overall:   (57 × 1.0 + 39 × 0.5) / 99 × 100 = 77.3%  (76.5 of 99 total requirements)

## 4. Top Gaps

- PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract — Without the exact structured Ask contract, the mentioned-shows row can break or mis-map titles, collapsing one of the main AI-to-detail navigation loops.
- PRD-018 | `critical` | Overlay saved user data on every show appearance — If saved-user overlays do not consistently win everywhere a show appears, the core product promise of your version of the catalog becomes unreliable.
- PRD-025 | `critical` | Removing status deletes show and all My Data — Ambiguous removal semantics create ghost-record risk and make collection membership harder to reason about for users, tests, and sync logic.
- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data — A vague re-add path risks either losing My Data after removal or restoring stale fields, which directly undermines trust in persistence.
- PRD-098 | `critical` | Export saved shows and My Data as zip — Exporting raw JSON instead of a zip backup misses the explicit portability contract stakeholders will expect for data ownership and recovery.

## 5. Coverage Narrative

#### Overall Posture
This is a structurally sound plan with meaningful product-contract gaps. It would likely produce a working Next.js and Supabase application with the right major screens, but several exact behaviors that make the product trustworthy remain under-specified, especially around Ask output structure, collection lifecycle edge cases, and export fidelity.

#### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, Show Detail & Relationship UX, and AI Voice, Persona & Quality. Those areas have concrete architecture, schema, routing, and phased build tasks, so a team could start implementation with relatively little ambiguity about the runtime, persistence model, or the major show-detail and AI voice surfaces.

#### Weakness Clusters
The gaps are not random. They cluster in Ask Chat, Concepts, Explore Similar & Alchemy, and Collection Data & Persistence, where the PRD depends on exact behavioral contracts rather than broad feature existence. Person Detail and Settings & Export are also weak, but in a different way: they are present in the plan as shells, yet still off-spec on important specifics such as gallery depth, analytics shape, backup packaging, and export formatting.

#### Risk Assessment
If this plan were executed as-is, the first thing a stakeholder or QA reviewer would notice is a product that looks broadly complete but breaks key invisible contracts. Ask could render recommendations without a reliable machine-readable mention payload, saved-state overlays could drift across browse surfaces, removed and re-added shows could behave inconsistently, and export would land in the wrong format. Those are not cosmetic misses; they affect trust, recoverability, and the app's taste-aware discovery loop.

#### Remediation Guidance
The plan needs another pass focused on precise acceptance criteria, not more broad architecture. Tighten the data-lifecycle rules for removal, re-add, Scoop persistence, sync, and upgrades; specify the exact Ask and concept-response contracts including retries and domain redirects; define the navigation-shell and Search behavior more explicitly; and expand the Person Detail and export sections so they match the PRD's concrete UX and data-format requirements rather than placeholder feature shells.
