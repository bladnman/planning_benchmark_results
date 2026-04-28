## 1. Requirements Extraction

### Pass 1: Identify Functional Areas

- Benchmark Runtime & Isolation
- Collection Data & Persistence
- App Navigation & Discover Shell
- Collection Home & Search
- Show Detail & Relationship UX
- Ask Chat
- Concepts, Explore Similar & Alchemy
- AI Voice, Persona & Quality
- Person Detail
- Settings & Export

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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Scope And Ground Rules: Next.js latest stable for runtime, UI, and server boundary |  |
| PRD-002 | Use Supabase official client libraries | critical | full | Scope And Ground Rules: Supabase through official client libraries |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Environment And Configuration: `.env.example` with required values |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | Environment And Configuration: `.gitignore`; Key handling says secrets never committed |  |
| PRD-005 | Configure build through env without code edits | critical | full | Scope And Ground Rules: environment-variable configuration only |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | Environment And Configuration: service role server-only; secrets never committed |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | Phase 0 package scripts: `dev`, `build`, `test`, `lint`, `test:reset` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | Environment And Configuration: Supabase migration files; Phase 1 migrations |  |
| PRD-009 | Use one stable namespace per build | critical | full | Scope And Ground Rules: required isolation by `(namespace_id, user_id)` |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | Namespace Reset And Test Data scopes reset to the provided namespace |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | Identity And Request Context resolves `userId` for all user data access |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | Identity And Request Context and repository layer require namespace and user explicitly |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | Identity resolver accepts `X-User-Id` in dev/test and rejects dev injection in production |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | Identity And Request Context: replace the identity resolver, not the database schema |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Scope And Ground Rules: server-side persisted user data as source of truth |  |
| PRD-016 | Make client cache safe to discard | critical | full | Risk mitigation: persist all user-owned data server-side and treat local state as cache only |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Scope And Ground Rules: no reliance on Docker for primary path |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | Display Overlay: every list, search result, recommendation, AI mention, detail, and export uses projection |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | Relationship Controls maps Active, Done, Quit, Wait, Interested, and Excited | The plan covers the visible status behavior but never specifies the hidden `Next` status in the data model or service rules. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | Relationship Controls: Interested and Excited set `myStatus = later` with matching `myInterest` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `user_tags` table and Collection Home filters for Tags and No tags |  |
| PRD-022 | Define collection membership by assigned status | critical | full | Product Model Summary: a saved show is any movie or TV item with `myStatus` |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | Relationship Controls covers status, rating, and tag save behaviors |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | Relationship Controls: rating unsaved saves as Done; tag unsaved saves as Later and Interested |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | Relationship Controls: removal deletes overlay and clears My Data |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | Catalog merge plus display overlay preserve user overlay while public data refreshes |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | User Overlay Merge lists update timestamps for status, interest, tags, score, and scoop |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | User Overlay Merge timestamp rules; `updated_at desc` index; Scoop freshness | The plan uses timestamps for sync conflicts and freshness, but does not state a user-facing sorting rule based on the per-field modification timestamps. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | AI Scoop: 4-hour freshness and persist only if show is saved |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | Transient Data: Ask history and Alchemy concepts/results/reasons are not persisted |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | Catalog Integration: resolve recommendations by external ID, title, and media type |  |
| PRD-032 | Show collection and rating tile indicators | important | full | Collection Home and Search deliver in-collection and rating indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | User Overlay Merge by timestamp; Settings persist server-side | The plan covers conflict resolution but does not specify duplicate item detection and transparent duplicate merging across synced libraries. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | Migrations preserve saved shows, tags, statuses, interest, ratings, and AI Scoop across upgrades |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `user_settings` stores font, auto search, confirmation hiding, removal count, and last selected filter |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | Catalog Merge persists provider IDs by country and keeps detail payloads transient |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | Catalog Merge and User Overlay Merge specify non-empty public merges and timestamp user merges |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | Application Routes plus Collection Home filters cover filters panel and main destinations |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | Application Routes includes `/find?mode=search`, `/find?mode=ask`, and `/find?mode=alchemy` | The Find routes are planned, but a persistent primary navigation entry is not specified as a concrete UI deliverable. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | Application Routes includes `/settings` | The Settings route is planned, but a persistent primary navigation entry is not specified as a concrete UI deliverable. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | Find Hub Plan mode switching includes Search, Ask, and Alchemy |  |
| PRD-042 | Show only library items matching active filters | important | full | Collection Home Data Loading and Filters apply filters over the collection projection |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | Collection Home Grouping defines Active, Excited, Interested, and Other |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | Collection Home Filters include All Shows, tags, no tags, genre, decade, score, and media type |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | Phase 3 delivers Show tiles with collection and rating indicators |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | Collection Home Empty States covers no saved shows and no filter results |  |
| PRD-047 | Search by title or keywords | important | full | Search Plan: text search by title or keyword |  |
| PRD-048 | Use poster grid with collection markers | important | full | Search Plan: results shown as poster grid with in-collection and rating indicators |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | Search Plan: if `autoSearch` is true, open Search on launch or Find entry |  |
| PRD-050 | Keep Search non-AI in tone | important | full | Search Plan: Search lives inside Find hub and has no AI voice |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | Show Detail Plan says to preserve the documented hierarchy, then lists its own order | The listed order moves relationship controls before tags and does not exactly preserve the documented narrative hierarchy from the detail spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | Show Detail server load fetches images and videos; Phase 5 delivers header media with fallbacks | The plan covers header media and fallback states, but does not explicitly prioritize motion or trailers when available. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | Show Detail Section Order places core facts and community score early |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | Phase 5 delivers Relationship toolbar |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | Tag behavior: adding a tag to an unsaved show saves as Later and Interested |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | Rating behavior: rating an unsaved show creates an overlay with `myStatus = done` |  |
| PRD-057 | Show overview early for fast scanning | important | full | Show Detail Section Order places Overview and Scoop in the early page body |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | AI Scoop covers button states, streaming partial output, and avoiding blank waits |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | Ask-about-this-show handoff seeds optional show context when launched from Detail |  |
| PRD-060 | Include traditional recommendations strand | important | full | Show Detail includes Traditional recommendations and provider recommendations |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | Explore Similar flow starts with Get Concepts, then selection, then Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | Show Detail loads providers, cast, crew, and person-linking credits |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | Show Detail loads seasons for TV and budget/revenue for movies |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | Show Detail clusters relationship controls, tags, overview, Ask, and discovery early |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | Ask UI includes chat transcript, composer, reset, loading, and retry states |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | Ask Voice: direct answer in first few lines, opinionated, and spoiler-safe |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | Ask UI includes Mentioned shows horizontal strip |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | Ask fallback opens resolved items or hands unresolved titles to Search |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | Ask UI includes welcome view with 6 random starter prompts and refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | Ask Session Context summarizes older turns and Ask Voice preserves the chatty persona |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | Ask Session Context includes optional handoff show context when launched from Detail |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | AI Output Contract defines `commentary` and exact `showList` string format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | AI Output Contract retries once with stricter formatting, then falls back |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | Ask Voice: no recommendations outside TV/movies |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | Product Model Summary and Explore Similar describe concepts as taste ingredients |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | Concept Generation: bullet list only, 1 to 3 words, evocative and specific |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | Concept Generation: ordered by strength and varied across structure, tone, emotion, dynamics, and craft |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | full | Explore Similar and Alchemy require concept selection and ingredient-picking guidance |  |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | Explore Similar returns 5 resolved recommendations |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | Alchemy Flow supports 2+ starting shows, concepts, 6 recs, and More Alchemy chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | Alchemy and Explore Similar clear downstream results when inputs or concepts change |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | Alchemy Concept Generation requires concepts shared across all input shows | The plan requires shared multi-show concepts but omits the larger option pool expected for multi-show generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | Concept recommendation reasons explicitly connect to selected concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | full | Alchemy results include 1 to 2 defensible surprises and AI quality checks cover taste alignment |  |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | AI Service Layer centralizes AI surfaces and Risk mitigation preserves product voice |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | AI Service Layer guardrails enforce TV/movie domain and spoiler-safe defaults across surfaces |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | Ask Voice describes a warm, chatty TV/movie friend with opinionated but not harsh tone |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | AI Scoop content contract includes personal take, stack-up, Scoop paragraph, warnings, and verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | Ask Voice calls for direct first lines and bulleted lists for multiple recommendations |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | AI Service Layer inputs include show, overlay, library context, turns, concepts, and recommendations |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | AI Quality Checks use the discovery quality bar and require real-show integrity exactly 2 |  |
| PRD-092 | Show person gallery, name, and bio | important | full | Person Detail loads profile, image gallery, and biography |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | Person Detail analytics include average rating, top genres, and projects by year |  |
| PRD-094 | Group filmography by year | important | full | Person Detail loads credits grouped by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | Person Detail interactions open Show Detail from selected credit |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | Settings includes font size and Search on Launch |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | Settings includes username, AI model, and optional API key settings with safe handling |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | Export behavior produces a `.zip` containing saved shows and My Data |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | Export behavior encodes dates as ISO-8601 |  |

## 3. Coverage Scores

score = (91 × 1.0 + 8 × 0.5) / 99 × 100 = 95.96%

Critical:  (30 × 1.0 + 0 × 0.5) / 30 × 100 = 100.00%  (30.0 of 30 critical requirements)
Important: (59 × 1.0 + 8 × 0.5) / 67 × 100 = 94.03%  (63.0 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.00%  (2.0 of 2 detail requirements)
Overall:   95.96% (99 total requirements)

## 4. Top Gaps

- **PRD-033** | `important` | Sync libraries/settings consistently and merge duplicates: The plan covers timestamp conflict handling but not duplicate detection and transparent duplicate merging, which means synced libraries can still drift or show duplicate copies of the same title.
- **PRD-051** | `important` | Preserve Show Detail narrative section order: The plan broadly builds the page but changes the documented hierarchy, which risks losing the fast orientation and emotional flow the detail spec is trying to protect.
- **PRD-039** | `important` | Keep Find/Discover in persistent primary navigation: The route exists, but without a planned persistent nav entry, discovery can become less reachable than the PRD requires.
- **PRD-040** | `important` | Keep Settings in persistent primary navigation: The settings route and page exist, but export, key management, and accessibility settings need a guaranteed primary navigation path.
- **PRD-019** | `important` | Support visible statuses plus hidden `Next`: Visible status behavior is covered, but omitting hidden `Next` leaves a compatibility gap for the data model and future up-next behavior.

## 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with no critical requirement gaps. The architecture, persistence model, identity isolation, collection behavior, AI contracts, export path, and test strategy are all materially covered. The remaining risk is precision: several important product requirements are addressed by adjacent sections or broad intent, but not nailed down as explicit implementation work.

#### Strength Clusters

Coverage is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Ask Chat, AI Voice, Person Detail, and Settings & Export. The plan gives concrete tables, request-context rules, merge utilities, route boundaries, AI service contracts, export behavior, and verification phases. The critical requirements are especially well handled because namespace/user partitioning and server-side persistence are specified at multiple layers.

#### Weakness Clusters

The partial items cluster around user-facing exactness and edge-case continuity rather than core system design. Navigation persistence is implied by routes but not planned as a persistent primary UI. Detail page hierarchy and motion priority are close but not exact. Data continuity covers migrations and timestamp conflicts, but leaves duplicate merging, hidden `Next`, and timestamp-driven sorting under-specified. Concept generation is strong generally, but does not distinguish the larger multi-show option pool.

#### Risk Assessment

If executed as-is, the app would probably function and preserve user data, but QA would notice mismatches at the PRD-polish layer: Find and Settings may not feel persistently reachable, Show Detail may not follow the intended narrative rhythm, Alchemy may feel less exploratory than specified, and sync edge cases may still produce duplicate or oddly sorted library states. These are not foundational blockers, but they are the kind of product-fit issues stakeholders see quickly in review.

#### Remediation Guidance

The remaining planning work should add explicit acceptance criteria rather than broad new architecture. Add a primary navigation deliverable, hidden `Next` status semantics, timestamp-based sort rules, duplicate detection and merge behavior, exact Show Detail section ordering with motion-first header preference, and a multi-show concept generation pool size rule. These refinements would turn most partials into direct implementation tasks.
