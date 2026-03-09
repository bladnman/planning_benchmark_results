# Plan Evaluation

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
| PRD-001 | Use Next.js latest stable runtime | critical | full | `1. Architecture & Tech Stack` framework bullet |  |
| PRD-002 | Use Supabase official client libraries | critical | partial | `1. Architecture & Tech Stack` database/backend bullet names Supabase | The plan commits to Supabase but never specifies official client libraries. |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `2. Infrastructure & Isolation Strategy` environment variables bullet |  |
| PRD-004 | Ignore `.env*` secrets except example | important | missing | none | The plan never mentions `.gitignore` or excluding `.env*` secrets except `.env.example`. |
| PRD-005 | Configure build through env without code edits | critical | partial | `2. Infrastructure & Isolation Strategy` environment variables bullet | Environment variables are listed, but the plan does not explicitly state builds run from configuration alone without source edits. |
| PRD-006 | Keep secrets out of repo and server-only | critical | partial | `2. Infrastructure & Isolation Strategy` environment variables bullet names public and service-role keys | It does not explicitly prohibit committing secrets or restrict elevated keys to server-only use. |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `2. Infrastructure & Isolation Strategy` scripts bullet and `Phase 1` package scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `2. Infrastructure & Isolation Strategy` database setup bullet and `Phase 1` migrations |  |
| PRD-009 | Use one stable namespace per build | critical | partial | `2. Infrastructure & Isolation Strategy` namespace isolation bullet | Namespaces are scoped into tables and queries, but the plan never states one stable namespace is chosen for the whole build lifetime. |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | Namespace-scoped queries plus `test:reset` for a specific namespace |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `2. Infrastructure & Isolation Strategy` user identity bullet |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `3. Database Schema` includes both `namespace_id` and `user_id` plus a composite key |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | `2. Infrastructure & Isolation Strategy` lists `X-User-Id`, local selector, or fixed default user | Development identity injection approaches are named, but documentation and production gating are not planned. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | `2. Infrastructure & Isolation Strategy` says the schema will support future OAuth |  |
| PRD-015 | Keep backend as persisted source of truth | critical | partial | Supabase-backed schema and auto-save behaviors throughout `3. Database Schema` and `4.1` | The plan assumes backend persistence but never explicitly states server persistence is the source of truth over any client cache. |
| PRD-016 | Make client cache safe to discard | critical | missing | none | No cache or local-storage strategy is specified, so it is unclear whether clearing client state is safe. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | missing | none | The plan never says Docker is optional or that hosted Supabase is the supported cloud-agent path. |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | `3. Database Schema` merge strategy overlays user data on fetched catalog data |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | `4.2 Collection Home` grouping mentions Active, Wait, Quit, Done, and Later+interest | Visible statuses are only implied, and the hidden `Next` status is never modeled. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | partial | `4.2 Collection Home` grouping uses `Later + Excited` and `Later + Interested` | The plan implies the mapping in grouping but never states the UI chips set `Later` plus `my_interest`. |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `3. Database Schema` stores `my_tags`; `4.2 Collection Home` filters by tags |  |
| PRD-022 | Define collection membership by assigned status | critical | partial | `4.1 Show Representation & Detail Page` save/remove behavior centers on status | The plan never explicitly defines collection membership as the presence of `My Status`. |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | partial | `4.1 Show Representation & Detail Page` says setting status, rating, or adding a tag auto-saves | Choosing an interest chip as a save trigger is missing. |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | partial | `4.1 Show Representation & Detail Page` says first rating becomes `Done` and first tag becomes `Later` + `Interested` | The default-save rule is only specified for tagging and rating, not all save paths without an explicit status. |
| PRD-025 | Removing status deletes show and all My Data | critical | full | `4.1 Show Representation & Detail Page` clearing status removes the show and clears all user data |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | `3. Database Schema` merge strategy uses most recent field timestamps | Re-add behavior is not called out as preserving prior My Data while refreshing public fields. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `3. Database Schema` includes update timestamps for all `my_` fields plus `ai_scoop_update_date` |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `3. Database Schema` uses timestamps for merge conflicts; `4.4 AI Integrations` caches Scoop for four hours | Sorting, sync, and freshness uses are not broadly planned. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | partial | `4.4 AI Integrations` says Scoop is cached for four hours | The plan never states Scoop only persists for saved shows. |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | `4.3.3 Alchemy` uses ephemeral session state cleared on exit | Ask history and leave/reset semantics are not specified as session-only. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | partial | `4.3.2 Ask` parses structured mentions; `4.3.4 Explore Similar` returns 5 recommendations | The plan never defines deterministic real-show resolution for all AI recommendations. |
| PRD-032 | Show collection and rating tile indicators | important | full | `4.2 Collection Home` indicators bullet |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `3. Database Schema` settings table plus recent-field merge strategy | Cross-device sync behavior and duplicate merge handling are not specified. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | missing | none | There is no migration or data continuity strategy for preserving libraries across data-model upgrades. |
| PRD-035 | Persist synced settings, local settings, UI state | important | partial | `3. Database Schema` settings table includes `auto_search`, `font_size`, `theme`, etc. | The plan does not separate synced settings, local settings, and persisted UI state. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | missing | none | Provider ID persistence and transient detail-fetch boundaries are absent from the schema plan. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | partial | `3. Database Schema` merge strategy gives user data precedence and resolves conflicts by recency | The plan omits non-empty catalog merge rules and lifecycle fields like `detailsUpdateDate` and `creationDate`. |
| PRD-038 | Provide filters panel and main screen destinations | important | partial | `Phase 3` basic layout `(Sidebar + Main Content)` and `4.2 Collection Home` filtering | The full destination shell for Home, Detail, Find, Person, and Settings is not laid out. |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | `4.3 Discovery Hub (Find)` is treated as a top-level feature area | The plan never says Find/Discover is persistent primary navigation. |
| PRD-040 | Keep Settings in persistent primary navigation | important | missing | none | Settings work appears late, but persistent Settings navigation is not planned. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `4.3 Discovery Hub` includes Search, Ask, and Alchemy modes |  |
| PRD-042 | Show only library items matching active filters | important | full | `4.2 Collection Home` filtering plus `Phase 3` home view applying grouping and filters |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `4.2 Collection Home` grouping bullet |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `4.2 Collection Home` filtering covers tags, media type, genre, decade, and score |  |
| PRD-045 | Render poster, title, and My Data badges | important | partial | `4.2 Collection Home` indicators bullet and `Phase 3` Show Tile component | The plan names badges but does not explicitly preserve poster-and-title tile content. |
| PRD-046 | Provide empty-library and empty-filter states | detail | missing | none | Empty-library and empty-filter states are not mentioned. |
| PRD-047 | Search by title or keywords | important | partial | `4.3.1 Search` says live query against the external catalog API | Search behavior is not specified as title-or-keyword matching. |
| PRD-048 | Use poster grid with collection markers | important | partial | `4.3.1 Search` marks results already in the user collection | The poster-grid layout is not called out. |
| PRD-049 | Auto-open Search when setting is enabled | detail | missing | none | The Search-on-launch setting is not connected back to Search behavior. |
| PRD-050 | Keep Search non-AI in tone | important | missing | none | The plan never preserves Search as a non-AI catalog experience. |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | `4.1 Show Representation & Detail Page` layout hierarchy | The ordered narrative misses required sections like tags-before-overview, genres/languages, streaming, and conditional lower-page content. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | `4.1 Show Representation & Detail Page` starts with Header Media | Motion or trailer priority and graceful fallback behavior are not specified. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | partial | `4.1 Show Representation & Detail Page` lists Core Facts `(Year, Score)` | Runtime or seasons and their early surfacing are incomplete. |
| PRD-054 | Place status/interest controls in toolbar | important | partial | `4.1 Show Representation & Detail Page` includes status chips in user relationship controls | The requirement that status and interest controls live in the toolbar is not stated. |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `4.1 Show Representation & Detail Page` first tag sets `Later` + `Interested` |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `4.1 Show Representation & Detail Page` first rating sets `Done` |  |
| PRD-057 | Show overview early for fast scanning | important | full | `4.1 Show Representation & Detail Page` places Overview immediately after relationship controls |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | `4.4 AI Integrations` says Scoop is cached for four hours with progressive streaming UI | State copy, feedback behavior, and saved-vs-unsaved persistence nuances are missing. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | partial | `4.1 Show Representation & Detail Page` includes an AI Ask CTA | The plan never says Ask-about-show seeds the chat with current show context. |
| PRD-060 | Include traditional recommendations strand | important | full | `4.1 Show Representation & Detail Page` includes Recommendations in the layout hierarchy |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | partial | `4.3.4 Explore Similar` fetches concepts, lets the user select them, then returns recommendations | The CTA-first `Get Concepts` and `Explore Shows` interaction is not specified. |
| PRD-062 | Include streaming availability and person-linking credits | important | partial | `4.1 Show Representation & Detail Page` includes Cast/Crew | Streaming availability and explicit person-linking behavior are not both covered. |
| PRD-063 | Gate seasons to TV and financials to movies | important | missing | none | TV-only seasons and movie-only financial sections are never planned. |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | `4.1 Show Representation & Detail Page` keeps primary actions high in the hierarchy | The plan never addresses the “powerful but not overwhelming” page balance. |
| PRD-065 | Provide conversational Ask chat interface | important | full | `4.3.2 Ask (AI Chat)` |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `4.3.2 Ask` voice is “fun, chatty, spoiler-safe, opinionated TV nerd” | The plan does not commit to direct-first answer structure or confident recommendation behavior. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | partial | `4.3.2 Ask` renders clickable show mentions inline | The required horizontal mentioned-shows strip is not specified. |
| PRD-068 | Open Detail from mentions or Search fallback | important | partial | `4.3.2 Ask` clickable show mentions inline | The plan omits explicit Detail-opening behavior and Search fallback on mapping failure. |
| PRD-069 | Show six random starter prompts with refresh | important | missing | none | Starter prompts and refresh behavior are absent. |
| PRD-070 | Summarize older turns while preserving voice | important | missing | none | Conversation summarization for older turns is not planned. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | missing | none | Ask-about-show handoff sessions are not specified for chat. |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | `4.3.2 Ask` parses `Title::externalId::mediaType;;...` | The exact `commentary` + `showList` contract is only partially captured. |
| PRD-073 | Retry malformed mention output once, then fallback | important | missing | none | Malformed structured-output retry and fallback behavior are not planned. |
| PRD-074 | Redirect Ask back into TV/movie domain | important | partial | `4.4 AI Integrations` says the build will adhere strictly to the AI Prompting Context guide | Domain redirection is only implied, not broken out as a concrete Ask behavior. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | `4.4 AI Integrations` says AI should focus on vibe, structure, and emotional palette rather than generic plot summaries | The plan does not explicitly define concepts as taste ingredients rather than genres. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | partial | `4.4 AI Integrations` says the build will adhere strictly to the AI Prompting Context guide | The bullet-only, 1–3 word, non-generic concept contract is not made concrete in the plan. |
| PRD-077 | Order concepts by strongest aha and varied axes | important | missing | none | Concept ordering by strongest aha and varied axes is not addressed. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | `4.3.3 Alchemy` and `4.3.4 Explore Similar` both require users to select concepts | Ingredient-picking guidance and explicit selection UX requirements are not specified. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `4.3.4 Explore Similar` returns 5 recommendations |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `4.3.3 Alchemy` covers 2+ shows, shared concepts, up to 8 concepts, recommendations, and chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | missing | none | The plan never says changing inputs clears downstream concepts and results. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | `4.3.3 Alchemy` fetches shared concepts via AI | It does not call for a larger option pool for multi-show concept generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | partial | `4.3.3 Alchemy` fetches recommendations based on selected concepts | Concise reasons that cite the selected concepts are not specified. |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `Phase 6` will refine AI prompts to match the Quality Bar guidelines | Surprising-but-defensible taste alignment is delegated to future prompt tuning, not concrete plan work. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | partial | `4.4 AI Integrations` says the build will adhere strictly to the Voice Personality guides | One cross-surface persona is implied but not explicitly enforced across Ask, Scoop, Alchemy, and Explore Similar. |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | `4.4 AI Integrations` commits to the AI Prompting Context guide | Shared AI guardrails are referenced only via guide adherence, not as concrete implementation requirements across surfaces. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | `4.3.2 Ask` voice is “fun, chatty, spoiler-safe, opinionated TV nerd” | Warmth, joy-forward tone, and light-touch criticism are not fully specified across surfaces. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | partial | `4.4 AI Integrations` says Scoop evaluates fit and provides a personal take | The required mini-review structure and sections are not planned. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | `4.3.2 Ask` is framed as AI chat with a conversational voice | Brisk default pacing and dialogue-first behavior are not specified. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | `4.3.2 Ask` sends library context and conversation history to the AI | Surface-specific input contracts for Scoop, Ask-about-show, Explore Similar, and Alchemy are incomplete. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | `Phase 6` will refine AI prompts to match the Quality Bar guidelines | The plan does not include explicit rubric-based validation or hard-fail integrity checks. |
| PRD-092 | Show person gallery, name, and bio | important | missing | none | Person detail content is not planned beyond linking cast and crew on Show Detail. |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | missing | none | Analytics for ratings, genres, and projects-by-year are absent. |
| PRD-094 | Group filmography by year | important | missing | none | Filmography grouping by year is not mentioned. |
| PRD-095 | Open Show Detail from selected credit | important | missing | none | Opening Show Detail from a person credit is not specified. |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `3. Database Schema` settings table includes `auto_search` and `font_size` |  |
| PRD-097 | Support username, model, and API-key settings safely | important | partial | `2. Infrastructure & Isolation Strategy` lists API keys; `3. Database Schema` has a generic settings table | Username, model selection, and safe user-entered key handling are not defined as settings work. |
| PRD-098 | Export saved shows and My Data as zip | critical | partial | `Phase 6` implement data export functionality | Zip export of saved shows plus My Data is not specified. |
| PRD-099 | Encode export dates using ISO-8601 | important | missing | none | The export date encoding format is not covered. |

## 3. Coverage Scores

Critical:  (12 × 1.0 + 16 × 0.5) / 30 × 100 = 66.7%  (20.0 of 30 critical requirements)
Important: (13 × 1.0 + 37 × 0.5) / 67 × 100 = 47.0%  (31.5 of 67 important requirements)
Detail:    (0 × 1.0 + 0 × 0.5) / 2 × 100 = 0.0%  (0.0 of 2 detail requirements)
Overall:   (25 × 1.0 + 53 × 0.5) / 99 × 100 = 52.0%  (51.5 of 99 total requirements)

## 4. Top Gaps

- **PRD-034** | `critical` | Preserve saved libraries across data-model upgrades: The plan never addresses upgrade continuity, so a schema or storage revision could wipe the user’s core asset and break the benchmark’s continuity promise.
- **PRD-016** | `critical` | Make client cache safe to discard: Without an explicit disposable-cache strategy, clearing local state or reinstalling the app can appear to lose user data and undermine backend durability.
- **PRD-037** | `critical` | Merge catalog fields safely and maintain timestamps: The plan lacks the concrete merge rules that protect user edits during catalog refreshes, sync, and re-add flows, so data correctness is still under-specified.
- **PRD-072** | `critical` | Emit `commentary` plus exact `showList` contract: Ask depends on a strict model-output contract to render actionable mentions, and the plan only covers part of that contract.
- **PRD-031** | `critical` | Resolve AI recommendations to real selectable shows: If recommendations are not mapped deterministically into real show objects, discovery becomes non-actionable exactly where the product is supposed to convert curiosity into saves.

## 5. Coverage Narrative

#### Overall Posture
This is a structurally sound high-level plan with concerning holes. It covers the stack choice, isolation model, core library flows, and the existence of the major discovery surfaces, but it leaves too many benchmark-scored behaviors at the level of implication rather than explicit implementation intent.

#### Strength Clusters
The plan is strongest in **Benchmark Runtime & Isolation**, where it names Next.js, Supabase, namespace scoping, user identity, migrations, and developer scripts. It is also reasonably strong in **Show Detail & Relationship UX** and the core collection mechanics inside **Collection Data & Persistence**, especially around auto-save triggers, removal semantics, grouping, and the high-level Detail-page layout.

#### Weakness Clusters
The biggest weakness cluster is AI behavioral specificity. **Ask Chat**, **AI Voice, Persona & Quality**, and parts of **Concepts, Explore Similar & Alchemy** are mostly partial because the plan references the supporting guides without translating them into concrete contracts, fallback behavior, validation rules, or surface-specific tasking. A second cluster is long-tail product completeness: **Person Detail** is entirely absent, and **Settings & Export** stays too vague for a benchmark that scores exact output behavior.

#### Risk Assessment
If this plan were executed as-is, the first visible failure would be a product that looks directionally correct but fails acceptance on exact behavior. QA would likely catch AI mention-contract drift, missing chat starter/summarization flows, absent person-detail functionality, and weak persistence guarantees around cache clearing, upgrades, and merge safety before users ever see a polished “complete” build.

#### Remediation Guidance
The missing work is less about adding entirely new architecture and more about converting vague intent into explicit contracts. The plan needs a tighter persistence-spec section for cache disposal, version continuity, and merge rules; explicit tasking for Ask and concept-output contracts, retries, and validation; and new sections for Person Detail plus exact export behavior. In other words, the next revision should deepen specification and acceptance criteria, not just add more implementation phases.
