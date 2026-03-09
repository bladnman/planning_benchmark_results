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

##### Benchmark Runtime & Isolation

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

##### Collection Data & Persistence

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

##### App Navigation & Discover Shell

- PRD-038 | `important` | Provide filters panel and main screen destinations | `product_prd.md > 6. App Structure & Navigation`
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-040 | `important` | Keep Settings in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-041 | `important` | Offer Search, Ask, Alchemy discover modes | `product_prd.md > 6. App Structure & Navigation`

##### Collection Home & Search

- PRD-042 | `important` | Show only library items matching active filters | `product_prd.md > 7.1 Collection Home`
- PRD-043 | `important` | Group home into Active, Excited, Interested, Others | `product_prd.md > 7.1 Collection Home`
- PRD-044 | `important` | Support All, tag, genre, decade, score, media filters | `product_prd.md > 4.5 Filters (Ways to View the Collection)`
- PRD-045 | `important` | Render poster, title, and My Data badges | `product_prd.md > 7.1 Collection Home`
- PRD-046 | `detail` | Provide empty-library and empty-filter states | `product_prd.md > 7.1 Collection Home`
- PRD-047 | `important` | Search by title or keywords | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-048 | `important` | Use poster grid with collection markers | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-049 | `detail` | Auto-open Search when setting is enabled | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-050 | `important` | Keep Search non-AI in tone | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`

##### Show Detail & Relationship UX

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

##### Ask Chat

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

##### Concepts, Explore Similar & Alchemy

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

##### AI Voice, Persona & Quality

- PRD-085 | `important` | Keep one consistent AI persona across surfaces | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`
- PRD-087 | `important` | Make AI warm, joyful, and light in critique | `supporting_docs/ai_voice_personality.md > 2. Non-Negotiable Voice Pillars`
- PRD-088 | `important` | Structure Scoop as personal taste mini-review | `supporting_docs/ai_voice_personality.md > 4.1 Scoop (Show Detail "The Scoop")`
- PRD-089 | `important` | Keep Ask brisk and dialogue-like by default | `supporting_docs/ai_voice_personality.md > 4.2 Ask (Find → Ask)`
- PRD-090 | `important` | Feed AI the right surface-specific context inputs | `supporting_docs/ai_prompting_context.md > 2. Shared Inputs (Typical)`
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity | `supporting_docs/discovery_quality_bar.md > 4. Scoring Rubric (Quick)`

##### Person Detail

- PRD-092 | `important` | Show person gallery, name, and bio | `product_prd.md > 7.6 Person Detail Page`
- PRD-093 | `important` | Include ratings, genres, and projects-by-year analytics | `product_prd.md > 7.6 Person Detail Page`
- PRD-094 | `important` | Group filmography by year | `product_prd.md > 7.6 Person Detail Page`
- PRD-095 | `important` | Open Show Detail from selected credit | `product_prd.md > 7.6 Person Detail Page`

##### Settings & Export

- PRD-096 | `important` | Include font size and Search-on-launch settings | `product_prd.md > 7.7 Settings & Your Data`
- PRD-097 | `important` | Support username, model, and API-key settings safely | `product_prd.md > 7.7 Settings & Your Data`
- PRD-098 | `critical` | Export saved shows and My Data as zip | `product_prd.md > 7.7 Settings & Your Data`
- PRD-099 | `important` | Encode export dates using ISO-8601 | `product_prd.md > 7.7 Settings & Your Data`

Total: 99 requirements (30 critical, 67 important, 2 detail) across 10 functional areas

### 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | Tech Stack (Mandated by Infra Rider) |  |
| PRD-002 | Use Supabase official client libraries | critical | partial | Tech Stack (Mandated by Infra Rider); Repository Layout (`lib/supabase/` clients) | The plan commits to Supabase and local client wrappers, but it never explicitly says the implementation will use the official Supabase client libraries. |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Environment Configuration |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | Environment Configuration |  |
| PRD-005 | Configure build through env without code edits | critical | full | Environment Configuration |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | Environment Configuration |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | Infra Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | Database Schema (Supabase Migrations) |  |
| PRD-009 | Use one stable namespace per build | critical | full | Identity & Isolation Model |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | Identity & Isolation Model |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | Identity & Isolation Model |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | Identity & Isolation Model |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | Identity & Isolation Model |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | Identity & Isolation Model |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Cross-Cutting Rules Summary |  |
| PRD-016 | Make client cache safe to discard | critical | full | Cross-Cutting Rules Summary |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Tech Stack (Mandated by Infra Rider) |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | Cross-Cutting Rules Summary ("User's version wins everywhere"); Feature Implementation: Search; Feature Implementation: Collection Home | The plan states the principle, but it does not concretely specify user-overlay behavior for every show surface, especially recommendation and mention-driven surfaces. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | Database Schema (Supabase Migrations); Feature Implementation: Show Detail |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | Saving & Business Rules |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | Feature Implementation: Show Detail; App Structure & Navigation |  |
| PRD-022 | Define collection membership by assigned status | critical | full | Saving & Business Rules; Feature Implementation: Collection Home |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | Saving & Business Rules |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | Saving & Business Rules |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | Saving & Business Rules |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | missing | none | The plan defines the opposite behavior: removed shows re-add as fresh saves instead of restoring prior My Data before refreshing public data. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | Saving & Business Rules |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | Merge Logic; Feature Implementation: AI Scoop; Feature Implementation: Settings | Timestamps are used for merge conflict resolution and freshness, but the plan never specifies timestamp-driven sorting behavior. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | Feature Implementation: AI Scoop |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | Feature Implementation: Ask Chat; Feature Implementation: Alchemy | The plan describes in-session context handling, but it does not explicitly clear Ask history, mentioned shows, or Alchemy results on leave/reset. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | Cross-Cutting Rules Summary ("Discovery is always actionable"); Feature Implementation: Explore Similar; Feature Implementation: Ask Chat |  |
| PRD-032 | Show collection and rating tile indicators | important | full | Feature Implementation: Collection Home; Feature Implementation: Search |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | `cloud_settings` table; Feature Implementation: Settings; Merge Logic | Conflict resolution is sketched, but duplicate-item detection/merge and full cross-device library consistency behavior are not concretely planned. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | Data Continuity & Migration |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | Data Layer; Feature Implementation: Collection Home; Feature Implementation: Settings |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | External Catalog Client; Show Mapper |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | Merge Logic |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | App Structure & Navigation |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | App Structure & Navigation |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | App Structure & Navigation |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | App Structure & Navigation |  |
| PRD-042 | Show only library items matching active filters | important | full | Feature Implementation: Collection Home |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | Feature Implementation: Collection Home |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | Feature Implementation: Collection Home |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | Feature Implementation: Collection Home |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | Feature Implementation: Collection Home |  |
| PRD-047 | Search by title or keywords | important | full | Feature Implementation: Search |  |
| PRD-048 | Use poster grid with collection markers | important | full | Feature Implementation: Search |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | Feature Implementation: Search |  |
| PRD-050 | Keep Search non-AI in tone | important | partial | Feature Implementation: Search | Search is implemented as plain catalog lookup, but the user-facing non-AI tone constraint is only implied rather than explicitly preserved. |
| PRD-051 | Preserve Show Detail narrative section order | important | full | Feature Implementation: Show Detail |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | Feature Implementation: Show Detail |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | Feature Implementation: Show Detail |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | Feature Implementation: Show Detail |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | Feature Implementation: Show Detail |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | Feature Implementation: Show Detail |  |
| PRD-057 | Show overview early for fast scanning | important | full | Feature Implementation: Show Detail |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | Feature Implementation: Show Detail; Feature Implementation: AI Scoop |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | Feature Implementation: Show Detail; Feature Implementation: Ask Chat |  |
| PRD-060 | Include traditional recommendations strand | important | full | Feature Implementation: Show Detail |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | Feature Implementation: Explore Similar |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | Feature Implementation: Show Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | Feature Implementation: Show Detail |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | Feature Implementation: Show Detail |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | Feature Implementation: Ask Chat |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | Feature Implementation: Ask Chat |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | Feature Implementation: Ask Chat |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | Feature Implementation: Ask Chat |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | Feature Implementation: Ask Chat |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | Feature Implementation: Ask Chat |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | Feature Implementation: Ask Chat |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | Feature Implementation: Ask Chat |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | Feature Implementation: Ask Chat |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | AI Prompts (Shared rules) |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | Feature Implementation: Explore Similar ("pick the ingredients you want more of"); Concept Quality Constraints | The plan treats concepts as shared qualities and ingredients, but it never explicitly forbids genre-like outputs. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | Concept Quality Constraints; `concepts.ts` |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | Concept Quality Constraints; `concepts.ts` |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | full | Feature Implementation: Explore Similar; Feature Implementation: Alchemy |  |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | Feature Implementation: Explore Similar; `recs.ts` |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | Feature Implementation: Alchemy |  |
| PRD-081 | Clear downstream results when inputs change | important | full | Feature Implementation: Alchemy |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | Feature Implementation: Alchemy; `concepts.ts`; Concept Quality Constraints | Shared multi-show concepts are covered, but the plan keeps the same ~8 concept count instead of specifying a larger multi-show option pool. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `recs.ts`; Feature Implementation: Explore Similar; Feature Implementation: Alchemy |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | `recs.ts`; Cross-Cutting Rules Summary ("Taste-aware AI") | The plan requires concept-grounded reasons, but it does not define how recommendation generation avoids only-safe adjacency and preserves surprise without betrayal. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | partial | AI Prompts (Shared rules); Feature Implementation: AI Scoop; Feature Implementation: Ask Chat | Shared AI rules exist, but the plan does not explicitly unify Scoop, Ask, Explore Similar, and Alchemy under one persona contract. |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | AI Prompts (Shared rules) |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | Feature Implementation: AI Scoop; Feature Implementation: Ask Chat | Warmth appears in surface-specific notes, but the joy-forward and light-in-critique voice bar is not consistently carried across AI surfaces. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | Feature Implementation: AI Scoop |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | Feature Implementation: Ask Chat |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | `scoop.ts`; `ask.ts`; `recs.ts` |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | Concept Quality Constraints (from discovery_quality_bar.md) | The plan lists quality constraints, but it does not define a validation loop or scoring rubric that can fail discovery outputs when they miss the quality bar. |
| PRD-092 | Show person gallery, name, and bio | important | full | Feature Implementation: Person Detail |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | Feature Implementation: Person Detail |  |
| PRD-094 | Group filmography by year | important | full | Feature Implementation: Person Detail |  |
| PRD-095 | Open Show Detail from selected credit | important | full | Feature Implementation: Person Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | Feature Implementation: Settings |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | Feature Implementation: Settings |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | Feature Implementation: Settings |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | Feature Implementation: Settings |  |

### 3. Coverage Scores

**Overall score:**

score = (86 × 1.0 + 12 × 0.5) / 99 × 100 = 92.9%

**Score by severity tier:**

Critical:  (27 × 1.0 + 2 × 0.5) / 30 × 100 = 93.3%  (28.0 of 30 critical requirements)
Important: (57 × 1.0 + 10 × 0.5) / 67 × 100 = 92.5%  (62.0 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   (86 × 1.0 + 12 × 0.5) / 99 × 100 = 92.9%  (92.0 of 99 total requirements)

### 4. Top Gaps

- `PRD-026` | `critical` | Re-add preserves My Data and refreshes public data: The plan explicitly says removed shows re-add as fresh saves, which would discard prior ratings, tags, status, interest, and Scoop instead of restoring them.
- `PRD-018` | `critical` | Overlay saved user data on every show appearance: Without explicit overlay rules for every show surface, the same title can present inconsistent status/rating state across search, recommendations, mentions, and detail entry points.
- `PRD-002` | `critical` | Use Supabase official client libraries: Leaving the client-library choice implicit creates avoidable baseline drift in a benchmark where the persistence stack is part of the hard contract.
- `PRD-033` | `important` | Sync libraries/settings consistently and merge duplicates: Without duplicate merge semantics and cross-device consistency rules, multi-device use can produce conflicting settings or duplicate library items.
- `PRD-091` | `important` | Validate discovery with rubric and hard-fail integrity: Without an explicit validation loop, AI discovery can look functionally complete while still missing the quality bar on taste, voice, or real-show integrity.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with one meaningful critical contradiction and a concentrated cluster of under-specified AI quality and data-lifecycle behaviors. The structural build plan is ready enough to guide implementation, but it is not fully execution-ready without closing the remaining semantic gaps.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, App Navigation & Discover Shell, Show Detail & Relationship UX, Ask Chat, Person Detail, and Settings & Export. Those areas are concrete about runtime baseline, persistence boundaries, route structure, detail-page sequencing, Ask contracts, person analytics, and export behavior, so an implementation team would have clear direction in the most operationally important surfaces.

#### Weakness Clusters

The remaining gaps cluster in two places. First, Collection Data & Persistence is weaker on edge-case lifecycle semantics: overlaying saved state everywhere, using timestamps for all intended behaviors, session-only AI state, duplicate merges, and especially remove/re-add continuity. Second, AI Voice, Persona & Quality plus Concepts/Alchemy are weaker on qualitative contracts: keeping concepts non-genre, preserving one persona across surfaces, protecting surprise-without-betrayal, and formalizing validation instead of leaving quality to prompt intent.

#### Risk Assessment

If this plan were executed as-is, the first stakeholder-visible failure would likely be trust erosion around state continuity and AI polish. Users could lose My Data when they remove and later re-save a title, encounter inconsistent saved-state overlays on some discovery surfaces, and see AI outputs that are functionally wired but not reliably on-brand, surprising, or quality-gated.

#### Remediation Guidance

The remaining work is mostly planning precision, not wholesale redesign. Tighten the server/data sections with explicit lifecycle semantics for remove/re-add, timestamp-driven sorting, session-only AI state, and duplicate merges; then tighten the AI sections with a single cross-surface persona contract, explicit concept anti-patterns, recommendation quality targets, and a concrete evaluation rubric that can block low-integrity discovery behavior.
