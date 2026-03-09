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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Overview tech stack: Framework = Next.js (latest stable, App Router) |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 0.1 Repo Initialization installs @supabase/supabase-js and @supabase/ssr |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 0.2 Environment Variable Interface |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | 0.2 .gitignore entries: .env, .env.local, .env.*.local | The ignore pattern does not clearly cover all .env* secret variants except .env.example. |
| PRD-005 | Configure build through env without code edits | critical | full | 0.2 Environment Variable Interface; Phase 10 Cloud Agent Compatibility |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 0.2 server-only service role key; 2.4 Supabase Clients |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 0.4 Package Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 1.1 Supabase Migration Files in supabase/migrations/ |  |
| PRD-009 | Use one stable namespace per build | critical | full | 0.3 Identity & Isolation Model: NAMESPACE_ID stable per build |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 1.3 Reset Script scoped to NAMESPACE_ID; Phase 10 no global teardown |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 0.3 Identity & Isolation Model; schema includes user_id on user-owned rows |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 0.3 Effective partition key = (namespace_id, user_id) |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | 0.3 Dev/benchmark auth injection |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 0.3 Auth isolated to lib/auth.ts |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Key Constraints & Cross-Cutting Concerns: Backend is source of truth |  |
| PRD-016 | Make client cache safe to discard | critical | full | Key Constraints & Cross-Cutting Concerns: localStorage only for UI state |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Phase 10: Docker is NOT required |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | 6.1 Display rule overlays the user's My Data wherever a show appears |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | 1.1 my_status includes next; 4.4 toolbar omits Next from visible chips |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 4.4 Toolbar Interested/Excited mapping |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 2.5 getTagLibrary; 7.2 Tag Library |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 6.1 Collection Membership |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 4.4 Auto-save rules; 6.2 Status/Interest State Machine |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 4.4 Auto-save rules |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 2.5 deleteShow removes from collection and clears all My Data |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | 6.4 Re-adding the Same Show |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 1.1 per-field *_update_date columns |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | full | 6.3 Timestamps |  |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 3.3 AI API scoop; 5.3 AI Scoop Freshness |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | 6.5 AI Data Persistence table |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 5.2 AI Response -> Catalog Resolution |  |
| PRD-032 | Show collection and rating tile indicators | important | full | 4.2 ShowTile in-collection and rating indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 3.4 Settings API version conflict resolution; 6.3 timestamp merge | The plan covers timestamp-based sync but does not specify duplicate detection and transparent merge of duplicate items. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | 8.2 Data Continuity / Migrations |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | partial | 3.4 Settings API; 4.6 Settings; 7.1 lastSelectedFilter localStorage | The persistence model for all local settings and UI state is incomplete, especially hideStatusRemovalConfirmation and related counters. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | 1.1 provider_data JSONB; 3.2 transient fields not persisted |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 2.6 Merge Logic |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 4.1 Global Layout & Navigation |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | 4.1 Persistent top nav with Find/Discover link |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | 4.1 Persistent top nav with Settings link |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 4.3 Find / Discover Hub mode switcher |  |
| PRD-042 | Show only library items matching active filters | important | full | 4.2 Collection Home fetches shows filtered by active filter |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 4.2 Grouped by Active, Excited, Interested, collapsed Others |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 4.1 Filter Panel; 7.1 Filter Configuration |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | 4.2 ShowTile poster, title, badges |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 4.2 Empty states |  |
| PRD-047 | Search by title or keywords | important | full | 4.3 Search text input with live catalog queries |  |
| PRD-048 | Use poster grid with collection markers | important | full | 4.3 Search poster grid; in-collection badge |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | 4.3 Search: autoSearch setting auto-focuses search on Find open | The plan auto-focuses Search inside Find, but it does not specify launching the app directly into Search when the setting is enabled. |
| PRD-050 | Keep Search non-AI in tone | important | partial | 4.3 Search is defined as catalog query UI, not chat | The plan treats Search as plain catalog UI, but it never explicitly protects Search from AI-style tone or assistant-like copy. |
| PRD-051 | Preserve Show Detail narrative section order | important | full | 4.4 Sections in narrative order per detail_page_experience.md |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | 4.4 Header media carousel with trailer and graceful fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 4.4 Core facts row |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 4.4 Toolbar sticky/floating status/interest chips |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 4.4 My Tags plus auto-save rule for first tag |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 4.4 My Rating plus auto-save rule for unsaved show |  |
| PRD-057 | Show overview early for fast scanning | important | full | 4.4 Overview + Scoop appears fourth in section order |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | 4.4 Scoop toggle states; streaming fetch; Generating spinner |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 4.4 Ask about this show CTA seeds context |  |
| PRD-060 | Include traditional recommendations strand | important | full | 4.4 Recommendations strand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 4.4 Explore Similar: Get Concepts -> select -> Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 4.4 Streaming availability and Cast & Crew -> Person Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 4.4 Seasons (TV only); Budget vs Revenue (movies only) |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 4.4 section order clusters toolbar, overview, scoop, ask, concepts early | The plan places primary actions early, but it does not specify concrete anti-overwhelm layout treatment for long-tail detail content. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 4.3 Ask chat UI with user/assistant message bubbles |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | 5.1 Ask prompt plus global spoiler-safe constraint | The plan covers Ask mechanics, but it never makes the direct-answer, confident-picks behavior explicit. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 4.3 Mentioned shows horizontal strip |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 4.3 mentioned show click opens Detail or Search fallback |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 4.3 six random starter prompts + refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | 4.3 Ask summarized history; 5.1 summary preserves persona voice |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 4.4 Ask about this show CTA; 5.1 Ask prompt current show context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | 5.1 Ask output format { commentary, showList } with exact Title::externalId::mediaType format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 5.2 structured output parse failure retry once then fallback |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | partial | 5.1 shared prompt context and persona instructions | The plan never explicitly says Ask redirects out-of-domain prompts back into TV/movies. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | 5.1 Concepts prompt defines evocative concepts | The plan does not clearly state that concepts are taste ingredients rather than genres. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 5.1 Concepts prompt: bullet list, 1-3 words, specific over generic |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | 5.1 Concepts prompt: diverse axes, ordered by strength |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 4.3 Alchemy concept selection max 8 | The plan requires concept selection, but it omits the guidance copy about picking the ingredients the user wants more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 4.4 Explore Similar returns 5 results; 5.1 recommendations count 5 |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 4.3 Alchemy flow includes chaining via More Alchemy |  |
| PRD-081 | Clear downstream results when inputs change | important | full | 4.3 Alchemy: changing shows clears downstream |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | 5.1 multi-show concepts shared across all inputs | The plan covers shared multi-show concepts, but it never specifies returning a larger option pool for multi-show generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 5.1 Recommendation reasons must cite selected concepts explicitly |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 5.1 recommendations use selected concepts + user library summary | The plan grounds recommendations in taste inputs, but it does not specify the surprising-but-defensible quality bar. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | 5.1 persona instructions injected into all prompts |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | 5.1 shared system context; Key Constraints spoiler-safe by default | The plan lacks one explicit shared guardrail contract covering domain limits, honesty, spoiler safety, and actionability across every AI surface. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | 5.1 references ai_voice_personality.md for all prompts |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 5.1 Scoop prompt: personal take, honest stack-up, Scoop paragraph, fit/warnings, Worth it verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | 4.3 Ask UI; 5.1 Ask prompt | The plan does not explicitly state that Ask responses stay brisk and dialogue-like by default. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 5.1 shared context plus surface-specific inputs for all AI surfaces |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | 5.2 resolver validates title/externalId match and fallback | The plan covers integrity checks for resolution, but it does not include the broader discovery rubric or hard-fail quality validation. |
| PRD-092 | Show person gallery, name, and bio | important | full | 4.5 Person Detail image gallery, name, bio |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 4.5 analytics charts: average project ratings, top genres, projects-by-year |  |
| PRD-094 | Group filmography by year | important | full | 4.5 filmography grouped by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 4.5 clicking a credit -> Show Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 4.6 Display: font size and search-on-launch toggle |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 4.6 User, AI, Integrations settings; dev mode prefill from env, never committed |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 3.5 Export API; 8.1 zip with shows.json and settings.json |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 3.5 Export API and 8.1 dates as ISO-8601 |  |

## 3. Coverage Scores

Critical:  (29 × 1.0 + 1 × 0.5) / 30 × 100 = 98.3%  (29.5 of 30 critical requirements)
Important: (54 × 1.0 + 13 × 0.5) / 67 × 100 = 90.3%  (60.5 of 67 important requirements)
Detail:    (1 × 1.0 + 1 × 0.5) / 2 × 100 = 75.0%  (1.5 of 2 detail requirements)
Overall:   (84 × 1.0 + 15 × 0.5) / 99 × 100 = 92.4%  (91.5 of 99 total requirements)

## 4. Top Gaps

- PRD-086 | critical | Enforce shared AI guardrails across all surfaces: Without one explicit cross-surface contract, Ask, Scoop, Explore Similar, and Alchemy can drift on domain limits, spoiler handling, honesty, and actionability even if the plumbing is correct.
- PRD-033 | important | Sync libraries/settings consistently and merge duplicates: Cross-device use can accumulate duplicate or conflicting records if duplicate detection and transparent merges are not planned up front.
- PRD-091 | important | Validate discovery with rubric and hard-fail integrity: The app can ship recommendations that technically resolve but still miss the taste bar because no explicit validation loop guards quality.
- PRD-084 | important | Deliver surprising but defensible taste-aligned recommendations: Discovery can become safe-but-generic if the plan does not force the recommendation layer to balance novelty with fit.
- PRD-066 | important | Answer directly with confident, spoiler-safe recommendations: Ask can feel verbose or hesitant in practice if the response contract does not explicitly prioritize fast, opinionated answers.

## 5. Coverage Narrative

#### Overall Posture
This is a structurally strong plan with broad end-to-end coverage and only one critical gap, but it is not fully execution-ready yet because several AI behavior contracts are still underspecified. The runtime, data model, navigation, detail views, and export path are planned concretely; the remaining risk is mostly in how the AI is expected to behave, not whether the product skeleton exists.

#### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Show Detail & Relationship UX, Person Detail, and Settings & Export. Those areas are mapped to concrete phases, modules, routes, schemas, and business rules rather than vague aspirations.

#### Weakness Clusters
The partial items cluster around AI Voice, Persona & Quality plus Concepts, Explore Similar & Alchemy, with a smaller secondary cluster in sync/local-state semantics and Search launch behavior. The pattern is consistent: the plan usually defines UI flow and API shape, but it is less precise about qualitative AI contracts, guardrails, and a few persistence edge behaviors.

#### Risk Assessment
If this plan were executed as-is, the first thing a stakeholder or QA reviewer would notice is AI inconsistency rather than broken navigation or missing CRUD. Ask could become too essay-like or wander off-domain, concept generation could slide toward generic genre tags, and recommendation quality could vary because the plan does not yet lock in one shared quality-and-guardrails standard.

#### Remediation Guidance
The remaining work is mostly specification tightening, not feature invention. Add one explicit cross-surface AI contract that centralizes shared guardrails, define the discovery quality rubric and failure gates, spell out the missing sync/UI-state persistence semantics, and tighten the few UX behaviors that are currently implied rather than explicitly planned.
