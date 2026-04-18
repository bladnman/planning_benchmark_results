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
| PRD-001 | Use Next.js latest stable runtime | critical | full | §1 Tech Stack: "Runtime: Next.js latest stable" |  |
| PRD-002 | Use Supabase official client libraries | critical | full | §1 Tech Stack: "Persistence: Supabase" and official client split under `lib/supabase/` |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | §11 Scripts & Env: explicit `.env.example` variable block |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | §11 Scripts & Env: `.env.example` is specified | The plan names `.env.example` but never explicitly requires the `.gitignore` rule that excludes `.env*` secrets while preserving `.env.example`. |
| PRD-005 | Configure build through env without code edits | critical | full | §11 Scripts & Env: "connection is pure env configuration" |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | §1 Tech Stack: "anon key" in browser, "service-role key stays server-only"; §9 says keys never committed |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | §11 Scripts & Env: `dev`, `test`, `test:reset`, `db:migrate`, `db:seed` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | §10 Schema Migrations & Data Continuity: `supabase/migrations/` and `seed.sql` |  |
| PRD-009 | Use one stable namespace per build | critical | full | §4 Identity & Isolation: "single `BUILD_NAMESPACE_ID` per process" |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | §4 Identity & Isolation plus §14 Verification: disjoint namespaces and namespace-scoped reset |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | §3 Data Model: `shows` row includes `user_id`; settings tables are keyed by `(namespace_id, user_id)` |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | §3 Data Model: PK `(namespace_id, user_id, id)` and settings rows keyed by namespace plus user |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | §1 Tech Stack and §4 Identity: `X-User-Id` dev injection "gated to non-prod" |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | §4 Identity: replacing middleware "must NOT require schema changes" |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | §3 Data Model: writes go through the server client; §13/§14 make cache clearing non-destructive |  |
| PRD-016 | Make client cache safe to discard | critical | full | §13 Build Order 11 and §14 Verification: "clearing local cache never loses data" |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | §1 Tech Stack: "hosted preferred, local optional, Docker optional"; §11 says "No Docker requirement" |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | §5 Display rule: "Anywhere a show surfaces ... the UI renders the overlaid version" |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | §16 Assumptions: hidden `Next` stays in the data model while visible statuses remain primary UI chips |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | §5 Save triggers and `MyStatusControl`: Interested/Excited save as `Later + interest` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | §3 Data Model stores `my_tags[]`; §5 Filters adds one filter per tag plus `No tags` |  |
| PRD-022 | Define collection membership by assigned status | critical | full | §5 Save triggers and removal semantics make status the collection-membership boundary |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | §5 Implicit save triggers enumerates status, interest, rating, and tagging saves |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | §5 Save triggers and defaults: Later/Interested by default, rating-save => Done |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | §5 Removal: deleting status removes the row and clears all My Data plus AI Scoop |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | §3 Merge policy plus §5 Display rule keep My Data authoritative while refreshing catalog fields |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | §3 Data Model: each `my_*` field is paired with its own `_update_date` |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | §3 Data Model: "merge timestamps drive sync conflict resolution"; §6 uses scoop freshness | The plan uses timestamps for sync conflicts and Scoop freshness, but it never assigns them an explicit sorting role. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | §3 Merge policy and §6 Scoop: persisted only when saved and regenerated after 4h |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | §6 Persistence table marks Ask, Alchemy, and mentioned shows as session-only |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | §6 AI → real show resolution resolves by external ID/title and falls back gracefully |  |
| PRD-032 | Show collection and rating tile indicators | important | full | §5 Tile badges: in-collection and rating indicators are called out explicitly |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | full | §13 Build Order 11 and §14 Sync/merge verification cover newest-wins and duplicate merging |  |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | §10 Schema Migrations & Data Continuity: upgrades preserve all `my_*` data and AI Scoop |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | §3 Data Model defines `cloud_settings`, `local_settings`, and `ui_state` persistence |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | §3 Data Model stores `provider_data jsonb` and references the storage schema | The plan stores `provider_data`, but it does not state the IDs-only persistence rule or explicitly fence detailed catalog payloads off as transient. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | §3 Merge policy spells out `selectFirstNonEmpty`, timestamp resolution, and `detailsUpdateDate` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | §2 Directory Layout: `FilterSidebar` plus Home, Detail, Find, Person, and Settings routes |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | §2 Directory Layout includes `layout.tsx` and `find/page.tsx` | The plan defines the Find route, but it never explicitly commits to a persistent primary-nav entry for Find/Discover in the shared shell. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | §2 Directory Layout includes `layout.tsx` and `settings/page.tsx` | The plan defines the Settings route, but it never explicitly commits to a persistent primary-nav entry for Settings in the shared shell. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | §2 Directory Layout: `ModeSwitcher`, `Search`, `Ask`, and `Alchemy` features under Find |  |
| PRD-042 | Show only library items matching active filters | important | full | §5 Filters/sidebar plus §13 Build Order 3 for filtered Home rendering |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | §5 Home grouping order enumerates Active, Excited, Interested, Other |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | §5 Filters: All, tag, `No tags`, genre, decade, community score, and media toggle |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | §2 Components includes `PosterTile`; §5 Tile badges defines My Data indicators |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | partial | §13 Build Order 12: "Empty states, error states" | The hardening step mentions empty states generically, but it does not explicitly cover both the empty-library prompt and the empty-filter no-results state. |
| PRD-047 | Search by title or keywords | important | full | §2 Directory Layout: `catalog/search/route.ts` and `Search/` feature |  |
| PRD-048 | Use poster grid with collection markers | important | partial | §2 Directory Layout: `PosterTile` primitive and `Search/` feature | The plan implies poster-based search results, but it never explicitly states that Search is a poster grid with collection markers. |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | §9 Settings: `autoSearch` is "open Search on launch" |  |
| PRD-050 | Keep Search non-AI in tone | important | full | §6 AI Surfaces: "Search must have no AI voice" |  |
| PRD-051 | Preserve Show Detail narrative section order | important | full | §7 Show Detail Narrative Order preserves the 12-step section sequence |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | §7 item 1: header media carousel with trailers and graceful poster/backdrop fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | §7 item 2: core facts row with year, runtime/seasons, and community score |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | §7 toolbar sentence and `MyStatusControl` keep relationship controls above the fold |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | §5 Save triggers: tagging an unsaved show saves as Later plus Interested |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | §5 Save triggers: rating an unsaved show saves as Done |  |
| PRD-057 | Show overview early for fast scanning | important | full | §7 item 4 places Overview before deeper content |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | §6 Scoop toggle copy states and streaming behavior are defined explicitly |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | §6 Ask About This Show: Detail entry seeds Ask with current-show context |  |
| PRD-060 | Include traditional recommendations strand | important | full | §7 item 7 and `TraditionalRecsStrand/` |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | §7 item 8 and `ExploreSimilar/` preserve CTA-first concepts flow |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | §7 items 9-10 and `ProvidersStrand/`, `CastStrand/`, `CrewStrand/` |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | §7 items 11-12 and `SeasonsStrand/`, `BudgetRevenue/` |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | §7 closing sentence: primary controls stay above the fold so the page stays powerful, not overwhelming |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | §2 Directory Layout: `Ask/` with `Welcome`, `ChatTranscript`, `MentionedShowsStrip`, `Composer` |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | §6 Ask mechanics plus shared guardrails cover spoiler-safety and confidence broadly | The plan covers Ask mechanics and safety, but it does not explicitly lock in the quality-bar response shape for direct first-line answers and terse recommendation formatting. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | §2 Directory Layout: `MentionedShowsStrip/` in the Ask feature tree |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | §6 Ask: resolved mentions open Detail; unresolved titles hand off to Search |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | §6 Ask: six random starter prompts from a larger pool, refreshable |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | §6 Ask: summarize older turns after ~10 messages while preserving persona voice |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | §6 Ask About This Show seeds the chat with current-show context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | §6 Ask: exact `commentary` plus `showList` contract is written verbatim |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | §6 Ask: one stricter retry, then unstructured fallback with Search handoff |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | §6 Shared guardrails: "stay in TV/movies" |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | §6 Concepts quality rules center specificity and shared taste DNA over genre labels |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | §6 Concepts: bullet list only, 1-3 words, no explanation, no spoilers |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | §6 Concepts: strongest first with diversity across structure, vibe, emotion, and craft |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | §6 Explore Similar and Alchemy flows include chip selection and capped concepts | The plan includes concept selection, but it omits the UX guidance that tells users to pick the ingredients they want more of and the empty-state nudge toward selecting a concept. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | §6 Explore Similar recommendations: exactly 5 shows per round |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | §6 Alchemy recommendations: full flow plus optional "More Alchemy!" chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | §6 Alchemy: "Changing inputs clears concepts/results" |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | §6 Concepts: multi-show uses a larger pool shared across all inputs |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | §6 Explore Similar: each reason explicitly names which selected concepts align |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | full | §6 Alchemy allows classics/hidden gems; §12 enforces the discovery quality bar |  |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | §6 AI Surfaces uses shared guardrails across Scoop, Ask, Concepts, Explore Similar, and Alchemy |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | §6 Shared guardrails explicitly cover domain, spoilers, honesty, specificity, and actionability |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | §6 Shared guardrails: "joy-forward, warm" | The plan captures warmth and joy, but it never explicitly carries the spec's expectation that critique stays light rather than harsh or snobby. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | §6 Scoop: structured mini-blog with personal take, stack-up, centerpiece, fit, warnings, verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | §6 Ask covers transcript/composer mechanics and starter prompts | The plan covers Ask mechanics, but it does not explicitly preserve the voice spec's brisk, dialogue-first default response shape. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | §6 references the supporting AI specs and seeds show context for Ask-about-show | The plan references the AI specs, but it never concretely spells out the per-surface context assembly that makes outputs taste-aware rather than merely on-brand. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | §12 Testing Strategy and §14 Verification both enforce the quality rubric and real-show integrity |  |
| PRD-092 | Show person gallery, name, and bio | important | full | §8 Person Detail: gallery, bio, analytics charts, grouped filmography |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | §8 Person Detail: avg project rating, top genres, and projects-by-year analytics |  |
| PRD-094 | Group filmography by year | important | full | §8 Person Detail: filmography grouped by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | §8 Person Detail: tapping a credit opens that show's Detail page |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | §9 Settings: font size plus `autoSearch` |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | §9 Settings: username, AI model/key, and catalog API key with env-safe handling |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | §9 Settings: `/api/export` returns a `.zip` with all shows plus My Data |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | §9 Settings: export uses ISO-8601 dates |  |

### 3. Coverage Scores

Critical:  (30 × 1.0 + 0 × 0.5) / 30 × 100 = 100.0%  (30 of 30 critical requirements)
Important: (56 × 1.0 + 11 × 0.5) / 67 × 100 = 91.8%  (61.5 of 67 important requirements)
Detail:    (1 × 1.0 + 1 × 0.5) / 2 × 100 = 75.0%  (1.5 of 2 detail requirements)
Overall:   (87 × 1.0 + 12 × 0.5) / 99 × 100 = 93.9%  (99 total requirements)

### 4. Top Gaps

- PRD-090 | `important` | Feed AI the right surface-specific context inputs — Without an explicit per-surface context contract, the AI surfaces can sound on-brand while still feeling untethered to saved taste data, selected concepts, or the current show context.
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation — If Find is only present as a route and not a guaranteed shell affordance, one of the product's core repeat-entry loops becomes easier to bury or regress.
- PRD-040 | `important` | Keep Settings in persistent primary navigation — Burying Settings makes export, model/key configuration, and user-control flows harder to reach, which weakens data ownership and benchmark operability.
- PRD-066 | `important` | Answer directly with confident, spoiler-safe recommendations — Ask can technically function but still fail the product bar if it responds in a rambling or overly indirect way that hides the recommendation.
- PRD-036 | `important` | Keep provider IDs persisted and detail fetches transient — If persistence boundaries are vague, the schema can bloat with stale provider/detail payloads and become harder to migrate safely.

### 5. Coverage Narrative

#### Overall Posture
This is a strong, execution-ready plan with a small set of meaningful specification gaps. It covers the benchmark runtime, storage model, detail experience, AI flows, and delivery order concretely, but it is looser where the PRD expects shell-level UX guarantees and tighter AI behavior contracts.

#### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Show Detail & Relationship UX, Person Detail, and Settings & Export. Those areas have concrete sections, explicit feature decomposition, clear data contracts, and verification steps that make the intended implementation path easy to follow.

#### Weakness Clusters
The partial coverage clusters in two places. First, App Navigation & Discover Shell and a slice of Collection Home & Search are under-specified at the shell/presentation level: persistent nav affordances, explicit poster-grid wording, and named empty states are implied more than committed. Second, AI Voice, Persona & Quality and one Ask requirement are weaker at the behavioral-contract layer: the plan references the supporting specs, but it does not always restate the surface-specific context inputs and response-shape requirements that determine whether the AI feels truly product-correct.

#### Risk Assessment
If this plan were executed as-is, the first QA or stakeholder concern would likely not be a missing major feature. It would be a build that mostly works but feels slightly off-spec: Find or Settings could be less persistent than intended, Search could lose some of its exact presentation polish, and the AI could behave competently without consistently sounding brisk, grounded, and taste-aware in the specific ways the PRD requires.

#### Remediation Guidance
The remaining work is mostly precision planning, not wholesale replanning. Add one explicit shell/navigation section that locks in persistent primary-nav affordances; tighten the Search section with exact presentation and empty-state commitments; and promote the AI behavioral contracts from referenced docs into explicit endpoint-level requirements for context assembly, default response shape, and critique tone. Those additions would close most of the residual risk without changing the overall structure of the plan.
