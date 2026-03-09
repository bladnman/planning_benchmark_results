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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Overview; §1 Initialize Project |  |
| PRD-002 | Use Supabase official client libraries | critical | full | §1 Dependencies |  |
| PRD-003 | Ship `.env.example` with required variables | critical | partial | §1 Environment Variables | The plan includes `.env.example` variables but does not mention the required short comments for each variable. |
| PRD-004 | Ignore `.env*` secrets except example | important | missing | none | The plan never commits to a `.gitignore` rule that excludes `.env*` secrets while preserving `.env.example`. |
| PRD-005 | Configure build through env without code edits | critical | full | §1 Environment Variables; §6 Auth & Identity Strategy |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | §1 Environment Variables; §6 Service Role Key |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | §1 npm Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | §2 Migration; §2 Test Reset Script |  |
| PRD-009 | Use one stable namespace per build | critical | full | §6 Development |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | §2 Test Reset Script; §6 Development |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | §2 Database Schema |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | §2 Database Schema |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | §6 Development | The plan documents header/default-user injection but does not state how that mechanism is disabled or gated outside dev/test. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | §6 OAuth Migration Path |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | §5 React Query Configuration |  |
| PRD-016 | Make client cache safe to discard | critical | full | §5 React Query Configuration |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | §2 Database Schema; §1 npm Scripts | The plan assumes Supabase tooling but never says Docker is optional or unnecessary for cloud-agent execution. |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | §5 Merge Logic; §9 Home Page; §9 Find Page | The plan shows badges and merged detail views in several places but does not guarantee full My Data overlay on every show appearance, especially AI/search recommendation surfaces. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | partial | §4 Core Types; §9 Show Detail Page | The plan has visible status chips but it collapses interest into status values and never models the hidden `Next` state. |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | partial | §4 Core Types; §9 Show Detail Page | `Excited` and `Interested` are treated as primary statuses instead of chips that map to `Later` plus an interest level. |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | §9 Home Page; §9 Show Detail Page |  |
| PRD-022 | Define collection membership by assigned status | critical | partial | §5 Implicit Save Defaults; §9 Home Page | Home excludes items without status, but the plan never makes status the single definition of collection membership and even allows note-only saves without status. |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | partial | §5 Implicit Save Defaults; §9 Show Detail Page | Status, rating, and tagging save paths are covered, but interest-triggered saves are folded into the wrong status model. |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | partial | §5 Implicit Save Defaults | The plan gets rating-to-Done correct but turns tag saves into an `interested` status instead of the required `Later` plus `Interested` defaults. |
| PRD-025 | Removing status deletes show and all My Data | critical | full | §7 DELETE /api/shows/[id]; §9 StatusChipGroup |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | missing | none | Deleting the row on removal means the plan never preserves prior My Data for a later re-add or catalog refresh. |
| PRD-027 | Track per-field My Data modification timestamps | critical | partial | §2 Database Schema; §5 Merge Logic | Several per-field timestamps exist, but the canonical interest timestamp and the full My Data timestamp set are not modeled. |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | §5 Merge Logic; §5 React Query Configuration | Timestamps are used for merge and Scoop freshness, but not for the broader sorting, sync, and freshness behaviors the PRD calls for. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | §7 POST /api/ai/scoop |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | §7 AI Routes; §9 Find Page |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | §7 POST /api/ai/recs; §9 Ask; §10 Recommendations |  |
| PRD-032 | Show collection and rating tile indicators | important | full | §9 Home Page; §9 Find Page |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | §5 Merge Logic; §7 Settings & Utility | Conflict-safe merges are mentioned, but duplicate detection and cross-device consistency behavior are not planned explicitly. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | §2 app_metadata table; §13 Phase 1 | Schema versioning is implied, but the plan never describes upgrade behavior that guarantees existing libraries survive model changes intact. |
| PRD-035 | Persist synced settings, local settings, UI state | important | partial | §2 user_settings and ui_state tables; §7 Settings & Utility | Some settings and UI state are persisted, but the exact local settings called out in the PRD such as `autoSearch` and `fontSize` are not covered. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | §8 TMDB Mapper | The plan keeps provider data transient but does not persist provider IDs as opaque stored data for saved shows. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | §5 Merge Logic |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | §9 Home Page; §9 Pages & Features |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | partial | §9 Find Page | The plan defines a Find page but never specifies persistent primary navigation that keeps Find/Discover visible across the shell. |
| PRD-040 | Keep Settings in persistent primary navigation | important | partial | §9 Settings Page | The plan defines a Settings page but never specifies persistent primary navigation that keeps Settings visible across the shell. |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | §9 Find Page |  |
| PRD-042 | Show only library items matching active filters | important | full | §9 Home Page; §9 FilterSidebar |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | §9 Home Page |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | partial | §9 Home Page; §9 FilterSidebar | Tag, genre, decade, and media filters are planned, but “All Shows” and community-score filtering are missing. |
| PRD-045 | Render poster, title, and My Data badges | important | full | §9 Home Page; ShowCard |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | §13 Phase 7 |  |
| PRD-047 | Search by title or keywords | important | full | §9 Find Page Search |  |
| PRD-048 | Use poster grid with collection markers | important | full | §9 Find Page Search |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | missing | none | The plan never stores or honors an auto-open Search-on-launch setting. |
| PRD-050 | Keep Search non-AI in tone | important | full | §9 Find Page Search; §8 AI Prompts |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | §9 Show Detail Page | The planned page is close, but it does not explicitly preserve the canonical narrative hierarchy from the detail experience spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | §9 Show Detail Page; HeaderMedia | Backdrop/poster rendering is planned, but motion-prioritized trailer behavior with graceful fallback is not committed. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | partial | §9 Show Detail Page; HeaderMedia | Year and runtime/seasons are early, but the plan never explicitly brings community score forward with them. |
| PRD-054 | Place status/interest controls in toolbar | important | partial | §9 DetailToolbar | The toolbar placement is correct, but the control model is still status-based rather than a status-plus-interest relationship. |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | partial | §5 Implicit Save Defaults; §9 TagsSection | Tagging triggers an implicit save, but it saves as `interested` status instead of `Later` plus `Interested`. |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | §5 Implicit Save Defaults; §9 RatingControl |  |
| PRD-057 | Show overview early for fast scanning | important | full | §9 OverviewSection |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | §9 ScoopSection; §10 Scoop | Streaming and TTL are covered, but the required UI copy/state progression for no-scoop, cached-scoop, and open states is not. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | §9 AskCTA; §10 Ask |  |
| PRD-060 | Include traditional recommendations strand | important | full | §9 RecommendationsStrand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | partial | §9 ExploreSimilar | Concept results are planned, but the CTA-first flow of “Get Concepts” then “Explore Shows” is not spelled out. |
| PRD-062 | Include streaming availability and person-linking credits | important | full | §9 ProvidersSection; §9 CastCrewSection |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | §9 SeasonsSection; §9 BudgetRevenueSection |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | §9 Show Detail Page; §14 Key Design Decisions | Primary actions are early, but the plan does not explicitly address the page-level busyness balance from the detail experience spec. |
| PRD-065 | Provide conversational Ask chat interface | important | full | §9 Find Page Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | §10 Ask | The plan says Ask is conversational, but it does not lock in direct-first, confident, spoiler-safe recommendation behavior. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | missing | none | The plan renders inline show pills in chat instead of the required horizontal mentioned-shows strip. |
| PRD-068 | Open Detail from mentions or Search fallback | important | partial | §9 Find Page Ask | Mentioned shows open Detail, but the required Search fallback for unresolved mentions is not planned. |
| PRD-069 | Show six random starter prompts with refresh | important | missing | none | No starter-prompt welcome state or refresh behavior is specified for Ask. |
| PRD-070 | Summarize older turns while preserving voice | important | missing | none | The plan stores chat history in component state but never summarizes older turns to preserve context economically. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | §9 AskCTA; §10 Ask |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | missing | none | The plan uses custom `[[SHOW:id:title]]` tokens instead of the required structured `commentary` plus exact `showList` contract. |
| PRD-073 | Retry malformed mention output once, then fallback | important | missing | none | No retry-once parser recovery path or fallback behavior is defined for malformed Ask mention output. |
| PRD-074 | Redirect Ask back into TV/movie domain | important | partial | §8 AI Prompts; §10 Ask | The plan references the AI prompting guide, but it never explicitly enforces redirecting off-domain queries back into TV/movie territory. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | §10 Concepts |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | partial | §10 Concepts | The plan returns labeled concept objects with descriptions, which breaks the bullet-only, 1–3 word, no-explanation output contract. |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | §10 Concepts | The concept rules cite the source doc, but the plan never commits to strongest-first ordering or axis diversity. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | §9 Alchemy; §9 ExploreSimilar | Concept selection is present, but the user-guidance copy about choosing ingredients is missing. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | missing | none | The plan never fixes Explore Similar to the required five recommendations per round. |
| PRD-080 | Support full Alchemy loop with chaining | important | partial | §9 Find Page Alchemy | The main loop exists, but the chaining step for continuing into another round is omitted. |
| PRD-081 | Clear downstream results when inputs change | important | missing | none | The plan does not state that changing Alchemy inputs or concept selections clears downstream concepts/results. |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | §10 Concepts | Multi-show generation is covered generally, but the larger option pool requirement is not planned. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | partial | §10 Recommendations | Recommendations include rationales, but those rationales are not required to cite the selected concepts explicitly. |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | §10 Recommendations; §10 Scoop | The plan mentions specificity, but it does not define the surprise-without-betrayal or taste-alignment quality bar for recs. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | §8 AI Prompts |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | §8 AI Prompts; §10 AI Surface Details | The plan references the specs, but it does not establish one shared contract for spoiler safety, domain bounds, honesty, and actionability across all AI surfaces. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | §8 AI Prompts | Warmth is present, but the full joy-forward and light-in-critique voice behavior is not explicitly preserved. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | partial | §10 Scoop | Scoop is personality-driven, but the plan does not require the full personal-taste mini-review structure and sections. |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | partial | §10 Ask | Ask is conversational, but the plan does not specify the brisk default length and dialogue-like pacing from the voice spec. |
| PRD-090 | Feed AI the right surface-specific context inputs | important | partial | §7 AI Routes; §10 AI Surface Details | Show context and concepts are passed through, but the plan never makes library/My Data and other surface-specific context inputs universal where required. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | missing | none | There is no planned rubric-based validation or hard-fail integrity check for discovery outputs. |
| PRD-092 | Show person gallery, name, and bio | important | partial | §9 Person Detail Page | Photo, name, and bio are covered, but the image gallery requirement is not. |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | partial | §9 PersonAnalytics | Ratings and genres are planned, but projects-by-year analytics are missing. |
| PRD-094 | Group filmography by year | important | missing | none | Filmography is grouped by role instead of by year. |
| PRD-095 | Open Show Detail from selected credit | important | full | §9 Filmography |  |
| PRD-096 | Include font size and Search-on-launch settings | important | missing | none | The Settings plan omits both font-size and Search-on-launch settings. |
| PRD-097 | Support username, model, and API-key settings safely | important | partial | §9 Settings Page | Model and API-key settings are present, but username sync and the full safe-handling expectations are not fully specified. |
| PRD-098 | Export saved shows and My Data as zip | critical | full | §7 GET /api/export; §9 Settings Page |  |
| PRD-099 | Encode export dates using ISO-8601 | important | missing | none | The export route is planned, but the ISO-8601 date-encoding requirement is not mentioned. |

### 3. Coverage Scores

Critical:  (18 × 1.0 + 10 × 0.5) / 30 × 100 = 76.7%  (23.0 of 30 critical requirements)
Important: (22 × 1.0 + 34 × 0.5) / 67 × 100 = 58.2%  (39.0 of 67 important requirements)
Detail:    (1 × 1.0 + 0 × 0.5) / 2 × 100 = 50.0%  (1.0 of 2 detail requirements)
Overall:   (41 × 1.0 + 44 × 0.5) / 99 × 100 = 63.6%  (63.0 of 99 total requirements)

### 4. Top Gaps

- PRD-020 | `critical` | Map Interested/Excited chips to Later interest: The plan turns interest chips into standalone statuses, which distorts collection semantics, default saves, filters, and any downstream persistence logic tied to `Later`.
- PRD-026 | `critical` | Re-add preserves My Data and refreshes public data: The row-deletion approach means a user can lose prior tags, ratings, and Scoop history when they remove and later re-add a show, which is a direct durability regression.
- PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract: Ask UI integration depends on this exact machine-readable contract, so the custom token format would break mentioned-show rendering and deterministic resolution.
- PRD-024 | `critical` | Default save to Later/Interested except rating-save Done: Because the implicit-save defaults are modeled incorrectly, common entry points like tagging produce the wrong relationship state and mis-shape the collection from first save onward.
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces: Without a single shared AI contract, Ask, Scoop, Alchemy, and Explore Similar are likely to drift on spoilers, domain limits, honesty, and actionability, creating QA-visible inconsistency.

### 5. Coverage Narrative

#### Overall Posture
The plan is structurally sound as an implementation blueprint, but it is not yet spec-tight. It covers the infrastructure baseline, page inventory, and broad feature set well, yet it leaves several fundamental product semantics only partially aligned, especially around collection state modeling and AI behavior contracts.

#### Strength Clusters
The strongest coverage clusters are Benchmark Runtime & Isolation, Collection Home & Search, and the page-level shell for Show Detail and export. Those sections are concrete about Next.js/Supabase setup, namespace and user partitioning, migrations, API routes, home/search scaffolding, and the core screen inventory.

#### Weakness Clusters
The biggest gaps cluster in Ask Chat, Concepts/Explore Similar/Alchemy, AI Voice/Persona/Quality, and Settings & Export, with an additional cross-cutting weakness in Collection Data & Persistence. The pattern is consistent: the plan usually names the feature, but it often stops short of the exact behavioral contracts, counts, fallback rules, and canonical data semantics that the PRD freezes.

#### Risk Assessment
If executed as written, the first thing QA and stakeholders would notice is that the app feels broadly complete but behaves “off” in the highest-touch flows. Interest/status state would not match the product model, Ask would not emit the required mentioned-show contract or starter/summarization behaviors, and concept-driven discovery would feel thinner and less deterministic than the PRD requires.

#### Remediation Guidance
The remaining work is mostly planning precision, not wholesale replanning. The plan needs a corrected canonical data model for status versus interest, explicit acceptance criteria for AI output contracts and fallbacks, exact UX/state rules for Ask and Explore Similar, and tighter settings/person-detail specifications so the implementation team is not left to infer benchmark-critical behavior.
