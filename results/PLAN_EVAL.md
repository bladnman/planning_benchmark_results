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
| PRD-001 | Use Next.js latest stable runtime | critical | full | `Overview` stack: `Next.js 15 (App Router, TypeScript)` |  |
| PRD-002 | Use Supabase official client libraries | critical | full | `1.2 Supabase Client Setup` |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `1.1 Repository Setup` |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | `1.1 Repository Setup` |  |
| PRD-005 | Configure build through env without code edits | critical | full | `1.1 Repository Setup` |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | `1.1 Repository Setup`; `1.2 Supabase Client Setup` |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `1.4 Dev Scripts` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `2.1 Migration Files` |  |
| PRD-009 | Use one stable namespace per build | critical | full | `1.3 Identity & Namespace Injection (Benchmark-Friendly)` |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | `4.3 Test Reset`; `16.3 Test Isolation` |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `2.1 Migration Files` (`user_id` column) |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `2.1 Migration Files`; `4.1 Show Repository` |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | `1.3 Identity & Namespace Injection (Benchmark-Friendly)` |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | `1.3 Identity & Namespace Injection (Benchmark-Friendly)` |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | `Overview`; `4.1 Show Repository` |  |
| PRD-016 | Make client cache safe to discard | critical | partial | `15.1 Display Rule: User Data Always Wins`; `5.2 Rendering Strategy` | It makes the server authoritative, but it never explicitly states that clearing local storage or reinstalling the client is safe. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | `Overview` stack: `Supabase (hosted)` | Hosted Supabase avoids Docker by implication, but the plan never states that Docker is optional or unsupported for benchmark execution. |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | `15.1 Display Rule: User Data Always Wins` |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | `2.1 Migration Files` (`my_status` includes `next`) |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | `8.3 Auto-Save Logic` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `8.2 MyTags.tsx`; `6.1 Filter Sidebar` |  |
| PRD-022 | Define collection membership by assigned status | critical | full | `4.1 Show Repository` |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | `8.3 Auto-Save Logic` |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | `8.3 Auto-Save Logic` |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | `4.1 removeShow(showId)`; `8.4 Removal Confirmation` |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | `15.4 Re-adding the Same Show` | The plan promises re-add preservation, but `removeShow(showId)` deletes the row, so the retention path is internally inconsistent. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `15.3 All Timestamps` |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `15.3 All Timestamps`; `3.3 Merge Policy`; `14.1 API Route` | Timestamps are written and used for merge/freshness, but the plan does not explicitly cover sorting or duplicate-sync semantics. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | `14.1 API Route` |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | `9.3 API Route`; `10.2 API Routes` |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | `15.5 AI Recommendations -> Real Shows` |  |
| PRD-032 | Show collection and rating tile indicators | important | full | `15.2 Show Tile Indicators` |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `4.2 Settings Repository`; `3.3 Merge Policy` | It covers persisted settings and timestamped updates, but not duplicate detection or merge behavior for synced libraries across devices. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | `2.3 Data Continuity` |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `13.1 Settings Page`; `6.1 Filter Sidebar`; `8.4 Removal Confirmation` |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | `2.1 Migration Files` (`provider_data`); `3.2 Show Mapper Rules` |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | `3.3 Merge Policy`; `15.1 Display Rule: User Data Always Wins` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | `5.1 Route Layout`; `6.1 Filter Sidebar` |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | `5.1 Route Layout` | The route exists, but the plan never specifies a persistent primary-navigation affordance that keeps Find/Discover available across the shell. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | `5.1 Route Layout` | The route exists, but the plan never specifies a persistent primary-navigation affordance that keeps Settings available across the shell. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `9.1 Chat UI (Search / Ask / Alchemy mode switcher)` |  |
| PRD-042 | Show only library items matching active filters | important | full | `6.2 Collection Display` |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `6.2 Collection Display` |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `6.1 Filter Sidebar` |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | `6.2 Collection Display`; `ShowTile.tsx` |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | `6.2 Collection Display` empty states |  |
| PRD-047 | Search by title or keywords | important | full | `7.1 Search UI` |  |
| PRD-048 | Use poster grid with collection markers | important | full | `7.1 Search UI`; `7.2 API` |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | missing | none | The setting exists, but the plan only says Search auto-focuses if enabled; it does not define the launch behavior that auto-opens Search. |
| PRD-050 | Keep Search non-AI in tone | important | full | `7.1 Search UI`; `9.1 Chat UI` keeps Search separate from AI modes |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | `8.2 Section Components (in spec-defined narrative order)` | The components are ordered concretely, but the sequence does not preserve the exact narrative hierarchy from the detail-page spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | `8.2 DetailHeader.tsx` |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | `8.2 CoreFacts.tsx` |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | `8.2 StatusToolbar.tsx` |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `8.3 Auto-Save Logic` |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `8.2 MyRating.tsx`; `8.3 Auto-Save Logic` |  |
| PRD-057 | Show overview early for fast scanning | important | partial | `8.2 Overview.tsx` | Overview exists near the top, but it is still pushed behind multiple relationship controls instead of being explicitly prioritized for first-scan reading. |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | `14.2 Scoop Client State Machine` |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | `8.2 AskAboutShow.tsx`; `9.2 Ask Variants` |  |
| PRD-060 | Include traditional recommendations strand | important | full | `8.2 RecommendationsStrand.tsx` |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | `11.1 Integration` |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | `8.2 StreamingProviders.tsx`; `8.2 CastCrew.tsx` |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | `8.2 SeasonsSection.tsx`; `8.2 BudgetRevenue.tsx` |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | `8.2 Section Components`; `4. Busyness vs Power` is not otherwise expanded | Primary actions are early, but the plan does not spell out the decluttering strategy that keeps the page powerful without feeling busy. |
| PRD-065 | Provide conversational Ask chat interface | important | full | `9.1 Chat UI` |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `9.4 AI System Prompt Requirements` | The prompt covers spoiler safety and tone, but not the direct-answer-first behavior and confident recommendation posture. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | `9.1 MentionedShowsStrip.tsx` |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | `9.1 MentionedShowsStrip.tsx`; `9.3 API Route` |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | `9.1 StarterPrompts.tsx` |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | `9.3 API Route` |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | `9.2 Ask Variants` |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | `9.3 API Route` structured output block |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | `9.3 API Route` fallback behavior |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | `9.4 AI System Prompt Requirements` |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | `10.2 API Routes`; `11.1 Integration` | The concepts flow is present, but the plan never explicitly frames concepts as taste ingredients rather than genre labels. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | `10.2 API Routes` |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | `10.2 API Routes` | The plan specifies shared, evocative concepts, but not strongest-first ordering or cross-axis variety. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | `10.1 Alchemy Flow UI`; `11.1 Integration` | Concept selection is required, but the ingredient-picking guidance and nudges are not specified. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `11.1 Integration` |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `10.1 Alchemy Flow UI` |  |
| PRD-081 | Clear downstream results when inputs change | important | partial | `10.1 Alchemy Flow UI` | It clears downstream results when shows change, but not when concept selections themselves change. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | `10.2 API Routes` | Multi-show concepts are shared, but the plan does not explicitly provide a larger option pool than single-show generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `10.2 API Routes` |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `10.2 API Routes` | Reasons cite concepts, but the surprise-without-betrayal quality bar is not captured as an acceptance criterion. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | partial | `9.4 AI System Prompt Requirements`; `14.1 API Route` | Ask and Scoop get compatible tones, but the plan does not explicitly define one shared persona across every AI surface. |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | `9.4 AI System Prompt Requirements`; `14.1 API Route` | AI constraints appear in Ask and Scoop sections, but the plan does not define one shared guardrail contract enforced across all AI routes. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | `9.4 AI System Prompt Requirements`; `14.1 API Route` | Warmth and honesty are implied, but the joy-forward, light-in-critique voice pillars are not explicitly carried across surfaces. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | `14.1 API Route` |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | `9.1 Chat UI`; `9.4 AI System Prompt Requirements` | Ask is conversational, but the brisk dialogue-first default response style is not specified. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | `9.3 API Route`; `14.1 API Route` | Ask and Scoop inputs are spelled out, but the plan does not fully define surface-specific context inputs for every AI surface. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | `15.5 AI Recommendations -> Real Shows` | Real-show resolution is covered, but the broader quality rubric and hard-fail validation loop are not planned. |
| PRD-092 | Show person gallery, name, and bio | important | full | `12.1 Person Page` |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | `12.1 Person Page` |  |
| PRD-094 | Group filmography by year | important | full | `12.1 Person Page` |  |
| PRD-095 | Open Show Detail from selected credit | important | full | `12.1 Person Page` |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `13.1 Settings Page` |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | `13.1 Settings Page` |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | `13.2 Data Export` |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | `13.2 Data Export` |  |

### 3. Coverage Scores

Overall score:

(75 x 1.0 + 23 x 0.5) / 99 x 100 = 87.4%

Critical:  (27 x 1.0 + 3 x 0.5) / 30 x 100 = 95.0%  (28.5 of 30 critical requirements)
Important:  (47 x 1.0 + 20 x 0.5) / 67 x 100 = 85.1%  (57.0 of 67 important requirements)
Detail:  (1 x 1.0 + 0 x 0.5) / 2 x 100 = 50.0%  (1.0 of 2 detail requirements)
Overall:   87.4% (99 total requirements)

### 4. Top Gaps

- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data: If removal truly deletes the record, users can lose prior tags, ratings, status, and Scoop history when they later save the same show again, which breaks a core durability promise.
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces: Without one shared guardrail contract, Ask, Scoop, Alchemy, and Explore Similar can drift on spoiler safety, domain bounds, honesty, and actionable-output behavior.
- PRD-016 | `critical` | Make client cache safe to discard: If cache disposal safety is not planned explicitly, clearing local client state may appear to corrupt user data or make benchmark resets unreliable.
- PRD-090 | `important` | Feed AI the right surface-specific context inputs: If each AI surface is not fed the right context, discovery quality will feel inconsistent and taste alignment will vary unpredictably across Ask, Scoop, Explore Similar, and Alchemy.
- PRD-077 | `important` | Order concepts by strongest aha and varied axes: Without ordering and variety rules, concept chips can become repetitive or low-signal, weakening both Explore Similar and Alchemy recommendation quality.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with notable semantic gaps rather than a weak or skeletal one. It is ready enough to guide substantial engineering work, but not yet precise enough to guarantee a benchmark-faithful rebuild without additional planning around durability and AI behavior contracts.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Collection Home & Search, and the main scaffolding for Show Detail and Ask Chat. Those areas are broken into concrete phases, routes, repository functions, and component-level tasks rather than being left as broad feature headings.

#### Weakness Clusters

The gaps cluster around two patterns. First, AI-specific behavioral contracts remain looser than the product spec in AI Voice, Persona & Quality and Concepts, Explore Similar & Alchemy. Second, a smaller number of lifecycle semantics are underspecified or internally inconsistent, especially around cache disposability, re-adding removed shows, and persistent shell/navigation expectations.

#### Risk Assessment

If this plan were executed as-is, the app would probably demo well at first pass: the routes, schema, collection flows, and major surfaces are all there. The first serious QA failures would appear in edge-behavior trust loops: a remove-then-re-add flow that cannot clearly preserve prior My Data, and AI surfaces that feel inconsistent because their shared guardrails and context contracts are not fully pinned down.

#### Remediation Guidance

The remaining work is mostly specification-tightening, not architectural rethinking. The plan needs a short follow-up pass that locks the exact data lifecycle for deletion, re-add, and disposable client state; then a shared AI contract layer that defines persona, guardrails, context inputs, and quality validation across all AI routes; then a final acceptance-criteria pass for navigation persistence, launch behavior, detail-page hierarchy, and concept ranking rules.

