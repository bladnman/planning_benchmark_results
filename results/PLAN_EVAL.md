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
| PRD-001 | Use Next.js latest stable runtime | critical | full | 1. Technology Stack & Project Foundation |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 1. Technology Stack & Project Foundation |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 11. Environment & Configuration > .env.example |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | 11. Environment & Configuration > .gitignore | The ignore rules cover common `.env` files but do not clearly exclude `.env*` secrets generically while preserving only `.env.example`. |
| PRD-005 | Configure build through env without code edits | critical | full | 11. Environment & Configuration > .env.example |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 1.2 Rationale; 11. Environment & Configuration > .env.example |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 13. Scripts & Developer Experience |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 12. Database Migrations |  |
| PRD-009 | Use one stable namespace per build | critical | full | 3.1 Namespace Resolution |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 3.3 Combined Partition; 13. Scripts & Developer Experience > npm run test:reset |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 2.1 `shows` Table; 2.2 `cloud_settings` Table; 3.2 User Identity |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 3.3 Combined Partition |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | 3.2 User Identity |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 3.4 Auth Migration Path |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | 2.5 Row-Level Security; 7.1 Architecture |  |
| PRD-016 | Make client cache safe to discard | critical | full | 2.5 Row-Level Security; 9.5 Persistence |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | 1.2 Rationale; 13. Scripts & Developer Experience > npm run db:migrate |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | partial | 4.4 Catalog API Routes; 7.4 Show Resolution Pipeline; 8.4 Find/Discover Hub > Search | The plan marks or resolves saved items, but it does not explicitly merge full status/tags/rating/Scoop overlays everywhere a show can appear. |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | 2.1 `shows` Table; 8.3 Show Detail Page |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 6.1 Auto-Save Triggers; 8.3 Show Detail Page |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 2.1 `shows` Table; 9.2 Filter Sections in Sidebar |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 2.1 `shows` Table; 6.2 Removal Flow |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 6.1 Auto-Save Triggers |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 6.1 Auto-Save Triggers |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 6.2 Removal Flow |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | partial | 6.2 Removal Flow; 5. Show Merge Logic | Hard-deleting the row clears My Data, but the plan never defines a strategy that would let a later re-add restore prior user data while refreshing catalog fields. |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 2.1 `shows` Table |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | 5.2 My Fields: Timestamp-Based Resolution; 7.5 `POST /api/ai/scoop` | Timestamps drive conflict resolution and Scoop freshness, but no timestamp-driven sorting behavior is planned. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 7.5 `POST /api/ai/scoop` |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | 8.4 Find/Discover Hub > Ask; 8.4 Find/Discover Hub > Alchemy | The flows are described as session-retained, but the plan does not explicitly clear chat/history/results when the user leaves or resets those surfaces. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 7.4 Show Resolution Pipeline; 7.5 `POST /api/ai/recommendations` |  |
| PRD-032 | Show collection and rating tile indicators | important | partial | 8.2 Collection Home; 8.4 Find/Discover Hub > Search | Tile badges are fully specified on Home, but Search and other discovery tiles only call out in-collection marking, not rating indicators. |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 5.2 My Fields: Timestamp-Based Resolution; 2.2 `cloud_settings` Table | The plan covers per-field merge for shows but does not specify duplicate detection or end-to-end conflict handling for synced settings/library duplicates. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | 15.4 Data Continuity Across Versions |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | 2.2 `cloud_settings` Table; 8.6 Settings; 9.5 Persistence |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | 2.1 `shows` Table; 4.4 Catalog API Routes |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 5. Show Merge Logic |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 8.1 Root Layout |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | 8.1 Root Layout |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | 8.1 Root Layout |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 8.4 Find/Discover Hub |  |
| PRD-042 | Show only library items matching active filters | important | full | 8.2 Collection Home; 9.3 Filter Application |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 8.2 Collection Home |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 9. Sidebar Filter System |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | 8.2 Collection Home |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 8.2 Collection Home > Empty states |  |
| PRD-047 | Search by title or keywords | important | full | 8.4 Find/Discover Hub > Search |  |
| PRD-048 | Use poster grid with collection markers | important | full | 8.4 Find/Discover Hub > Search |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | 8.4 Find/Discover Hub > Search; 8.6 Settings |  |
| PRD-050 | Keep Search non-AI in tone | important | full | 8.4 Find/Discover Hub > Search |  |
| PRD-051 | Preserve Show Detail narrative section order | important | full | 8.3 Show Detail Page |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | 8.3 Show Detail Page |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 8.3 Show Detail Page |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 8.3 Show Detail Page |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 8.3 Show Detail Page |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 8.3 Show Detail Page |  |
| PRD-057 | Show overview early for fast scanning | important | full | 8.3 Show Detail Page |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | 8.3 Show Detail Page |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 8.3 Show Detail Page |  |
| PRD-060 | Include traditional recommendations strand | important | full | 8.3 Show Detail Page |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 8.3 Show Detail Page |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 8.3 Show Detail Page |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 8.3 Show Detail Page |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 8.3 Show Detail Page | The section order is deliberate, but the plan does not turn the “powerful but not overwhelming” principle into concrete anti-clutter layout rules. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 8.4 Find/Discover Hub > Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | 7.3 Prompt Builders > `ask.ts` |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 8.4 Find/Discover Hub > Ask |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 8.4 Find/Discover Hub > Ask; 7.4 Show Resolution Pipeline |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 8.4 Find/Discover Hub > Ask |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | 7.3 Prompt Builders > `summarize.ts`; 8.4 Find/Discover Hub > Ask |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 7.3 Prompt Builders > `ask.ts`; 8.4 Find/Discover Hub > Ask |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | 7.3 Prompt Builders > `ask.ts`; 7.6 Structured Output Parsing & Fallbacks |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 7.6 Structured Output Parsing & Fallbacks |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | partial | 7.3 Prompt Builders > `ask.ts` | The prompt constrains Ask to TV/movies, but it does not describe the user-visible redirect or handoff behavior for out-of-domain requests. |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | 7.3 Prompt Builders > `concepts.ts` |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 7.3 Prompt Builders > `concepts.ts` |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | 7.3 Prompt Builders > `concepts.ts` |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 8.3 Show Detail Page; 8.4 Find/Discover Hub > Alchemy | The plan requires concept selection, but it omits the guidance copy that helps users choose the “ingredients” they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 8.3 Show Detail Page; 7.5 `POST /api/ai/recommendations` |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 8.4 Find/Discover Hub > Alchemy |  |
| PRD-081 | Clear downstream results when inputs change | important | full | 8.4 Find/Discover Hub > Alchemy |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | 7.3 Prompt Builders > `concepts.ts` |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 7.3 Prompt Builders > `recommendations.ts` |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 7.3 Prompt Builders > `recommendations.ts` | The recommendation contract covers concept citation and real-title resolution, but it does not define the “surprising but defensible” quality bar. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | 7.3 Prompt Builders |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | 7.1 Architecture; 7.3 Prompt Builders |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | 7.3 Prompt Builders |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 7.3 Prompt Builders > `scoop.ts` |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | 7.3 Prompt Builders > `ask.ts` |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 7.3 Prompt Builders |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | missing | none | The plan does not add any rubric-based discovery validation or hard-fail acceptance checks to prove outputs meet the quality bar and real-show integrity standard. |
| PRD-092 | Show person gallery, name, and bio | important | full | 8.5 Person Detail; 4.4 Catalog API Routes > `GET /api/catalog/person/[id]` |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 8.5 Person Detail |  |
| PRD-094 | Group filmography by year | important | full | 8.5 Person Detail |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 8.5 Person Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 8.6 Settings |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 8.6 Settings |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 8.6 Settings; 10. Data Export |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 8.6 Settings; 10. Data Export |  |

### 3. Coverage Scores

Critical:  (28 × 1.0 + 2 × 0.5) / 30 × 100 = 96.67%  (29.0 of 30 critical requirements)
Important: (57 × 1.0 + 9 × 0.5) / 67 × 100 = 91.79%  (61.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.00%  (2.0 of 2 detail requirements)
Overall:   (87 × 1.0 + 11 × 0.5) / 99 × 100 = 93.43%  (92.5 of 99 total requirements)

### 4. Top Gaps

1. PRD-018 | `critical` | Overlay saved user data on every show appearance
This gap matters because search, AI mentions, and recommendations can show a title without the user's real status/rating/tag context, making discovery surfaces feel inconsistent and untrustworthy.

2. PRD-026 | `critical` | Re-add preserves My Data and refreshes public data
This gap matters because the current delete-first plan can permanently discard the user's prior relationship data, so removing and later re-adding a show risks losing curation work.

3. PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity
This gap matters because the app's differentiator is taste-aware AI, and without explicit validation criteria the implementation can ship outputs that are formally parseable but off-brand or low-integrity.

4. PRD-033 | `important` | Sync libraries/settings consistently and merge duplicates
This gap matters because cross-device or repeated-save flows can drift into inconsistent settings or duplicate library state unless merge behavior is defined beyond per-row show timestamps.

5. PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations
This gap matters because the recommendation engine could collapse into safe, obvious picks, which weakens the product's core promise of discovery that feels insightful rather than generic.

### 5. Coverage Narrative

#### Overall Posture
The plan is structurally strong and unusually detailed: it covers the full application surface, names concrete routes, tables, API contracts, and sequence of implementation. The remaining issues are real, but they are concentrated in cross-cutting behavioral guarantees and AI quality controls rather than core app scaffolding.

#### Strength Clusters
The strongest coverage is in Benchmark Runtime & Isolation, Show Detail & Relationship UX, and the main application shell across Collection Home, Search, Person Detail, and Settings & Export. The plan is also strong on the mechanics of Ask, Concepts, and Alchemy: it specifies routes, prompt builders, response contracts, and end-to-end data flow with enough concreteness to implement directly.

#### Weakness Clusters
The partial and missing items are not random. They cluster around cross-surface consistency rules and qualitative AI product contracts: full overlay semantics wherever shows appear, removal/re-add continuity, session-clearing behavior, sync/duplicate handling, and the quality bar for discovery outputs. In other words, the plan is better at page architecture than at the last 10% of product-behavior guarantees.

#### Risk Assessment
If executed as-is, the app would likely look complete and mostly work, but QA and early users would notice subtle trust breaks first: saved shows would not always carry the same personal state across discovery surfaces, removing and later re-adding a show could lose prior curation, and AI recommendations could be mechanically valid while drifting toward generic or insufficiently defended picks.

#### Remediation Guidance
The plan needs a small number of high-leverage additions: explicit cross-surface overlay rules for all show render paths, a defined re-add/tombstone strategy or alternate removal model, explicit session reset semantics for Ask and Alchemy, sync and duplicate-resolution rules for libraries and settings, and a rubric-backed discovery validation section with hard-fail checks for real-show integrity and on-brand recommendation quality.
