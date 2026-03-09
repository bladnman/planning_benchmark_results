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
| PRD-001 | Use Next.js latest stable runtime | critical | full | 1. Architecture Overview > Stack |  |
| PRD-002 | Use Supabase official client libraries | critical | full | 1. Architecture Overview > Stack |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | 2. Project Structure lists .env.example; 12. Environment Variables |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | 13. Phase 1 includes .env.example + .gitignore; 14. Key Risk Mitigations mentions .env* exclusion | The plan says .gitignore excludes .env* files, but it never explicitly states the required exception that keeps .env.example committed. |
| PRD-005 | Configure build through env without code edits | critical | full | 12. Environment Variables |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 1. Key Architectural Decisions #2; 12. Environment Variables; 14. Key Risk Mitigations |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | 11.1 Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 2. Project Structure > supabase/migrations; 3.3 Migrations Strategy |  |
| PRD-009 | Use one stable namespace per build | critical | full | 4.1 Namespace Resolution |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 3.2 Row-Level Security; 11.3 Namespace Isolation in Tests |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 3.1 Tables > shows and cloud_settings |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | 1. Key Architectural Decisions #3; 3.1 Tables |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | 1. Key Architectural Decisions #4; 4. Identity & Isolation |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 15. Data Continuity & Migration Path |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | 1. Key Architectural Decisions #2; 7. API Route Handlers |  |
| PRD-016 | Make client cache safe to discard | critical | partial | 1. Architecture Overview > minimal client state via React context | The plan keeps client state minimal and transient, but it never explicitly states that clearing browser storage is safe and non-lossy for user data. |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | partial | 1. Architecture Overview > Stack | The stack supports hosted and local Supabase paths, but the plan never explicitly states that Docker is optional rather than required for benchmark execution. |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | 7.2 Catalog Proxy returns merged view; 8.3 Show Tile badges |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | 6.1 Status & Interest enum includes next |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 6.1 Status & Interest |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 3.1 shows.my_tags; 6.4 Filter System |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 10.2 Collection Membership Check |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 6.2 Save Triggers; 10.1 Save Trigger Middleware |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 6.2 Save Triggers; 10.1 Save Trigger Middleware |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 6.3 Removal; 7.1 DELETE /api/shows/[id] |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | 5.3 Merge Logic; 7.1 POST /api/shows upsert |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 3.1 Tables; 10.3 Timestamps |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | 5.3 Merge Logic; 10.4 AI Scoop Freshness | Timestamps are used for merge freshness and Scoop caching, but no user-facing sorting behavior is tied to those timestamps. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | 7.3 AI > POST /api/ai/scoop; 10.4 AI Scoop Freshness |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | 1. Architecture Overview; 8.5 Ask; 8.5 Alchemy | Ask and Alchemy are modeled as transient client state, but explicit reset/clear behavior when leaving those modes is not defined. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 7.3 AI > recommendations; 9.6 Parsing & Fallbacks |  |
| PRD-032 | Show collection and rating tile indicators | important | full | 8.3 Show Tile |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 3.1 cloud_settings; 5.3 Merge Logic; 15. Data Continuity & Migration Path | The plan covers per-field merge logic and settings storage, but it does not define duplicate detection or full cross-device library sync behavior. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | 3.3 Migrations Strategy; 15. Data Continuity & Migration Path |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | partial | 3.1 cloud_settings; 7.4 Settings; 8.7 Settings | Cloud settings are modeled, but concrete persistence for local settings and UI-state keys is not fully specified. |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | 3.1 shows.provider_data; 7.2 Catalog Proxy |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 5.3 Merge Logic; 10.5 Merge on Catalog Refresh |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 8.1 Layout Shell |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | 8.1 TopNav |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | 8.1 TopNav |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 8.5 Find/Discover |  |
| PRD-042 | Show only library items matching active filters | important | full | 8.2 Collection Home |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 8.2 Collection Home status-grouped sections |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 6.4 Filter System; 8.1 MediaTypeToggle |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | 8.3 Show Tile |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 8.2 Collection Home empty states |  |
| PRD-047 | Search by title or keywords | important | full | 7.2 Catalog Proxy search; 8.5 Search |  |
| PRD-048 | Use poster grid with collection markers | important | full | 8.5 Search; 8.3 Show Tile |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | partial | 8.7 Settings > Search on launch toggle | The setting exists, but the launch/navigation behavior that automatically lands the user in Search is not actually described. |
| PRD-050 | Keep Search non-AI in tone | important | full | 8.5 Search explicitly says no AI voice in search |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | 8.4 Show Detail Page ordered section list | The plan says it preserves the narrative hierarchy, but its enumerated Detail page order moves status and rating controls ahead of the specified tags/overview flow. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | 8.4 HeaderCarousel with graceful fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 8.4 CoreFacts |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | 8.4 StatusToolbar |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 8.4 TagSection; 6.2 Save Triggers |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 8.4 RatingBar; 6.2 Save Triggers |  |
| PRD-057 | Show overview early for fast scanning | important | full | 8.4 Overview |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | 8.4 ScoopSection; 10.4 AI Scoop Freshness |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 8.4 AskAboutCTA; 8.5 Ask show-seeded mode |  |
| PRD-060 | Include traditional recommendations strand | important | full | 8.4 RecommendationsStrand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 8.4 ExploreSimilar |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 8.4 StreamingProviders; 8.4 CastCrew |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 8.4 Seasons; 8.4 BudgetRevenue |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 8.4 Show Detail Page section plan | The plan lists the Detail page sections, but it does not explain the layout strategy that keeps the page powerful without feeling overloaded. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 8.5 Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | 9.1 Shared System Context; 9.3 Ask Prompt | Tone and spoiler safety are covered, but the direct-answer-first and confidently-picked recommendation bar is not explicitly encoded. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 8.5 Ask mentioned shows strip |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 7.3 AI > POST /api/ai/ask |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 8.5 Ask welcome view |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | 8.5 Ask; 9.3 Ask Prompt |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 8.5 Ask show-seeded mode |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | 7.3 AI > POST /api/ai/ask; 9.3 Ask Prompt |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 9.6 Parsing & Fallbacks |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | 9.1 Shared System Context |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | 9.4 Concepts Prompt |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 9.4 Concepts Prompt |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | 9.4 Concepts Prompt |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | full | 8.4 ExploreSimilar; 8.5 Alchemy |  |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 7.3 AI > POST /api/ai/recommendations |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 8.5 Alchemy full stepped flow |  |
| PRD-081 | Clear downstream results when inputs change | important | full | 8.5 Alchemy backtracking rules |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | 9.4 Concepts Prompt returns 8 concepts for all concept requests | Multi-show concepts are shared across inputs, but the plan never specifies returning a larger concept pool for Alchemy than for single-show Explore Similar. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 9.5 Recommendations Prompt |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 9.5 Recommendations Prompt | Concept alignment is covered, but the plan does not explicitly encode the product requirement for surprising-but-defensible recommendations. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | 9.1 Shared System Context |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | partial | 9.1 Shared System Context; 7.3 AI route contracts | Several shared prompt rules are present, but the full cross-surface guardrail contract is not carried through as an explicit non-negotiable implementation rule. |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | 9.1 Shared System Context | Warmth is specified, but the stronger joy-forward and light-even-in-critique voice constraints are only partially represented. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 9.2 Scoop Prompt |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | 9.3 Ask Prompt |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 7.3 AI route inputs; 9.2-9.5 Prompt inputs |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | 14. Key Risk Mitigations; 7.3 AI resolution/fallback handling | The plan handles resolution and parser fallback mechanics, but it never defines rubric-based discovery validation or a hard-fail quality gate. |
| PRD-092 | Show person gallery, name, and bio | important | full | 8.6 Person Detail |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 8.6 Person Detail analytics charts |  |
| PRD-094 | Group filmography by year | important | full | 8.6 Person Detail filmography grouped by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 8.6 Person Detail credit links |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 8.7 Settings > App section |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 7.4 Settings; 8.7 Settings |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 7.5 Export; 8.7 Settings > Your Data |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 7.5 Export produces JSON backup with ISO-8601 dates |  |

### 3. Coverage Scores

Overall score:

score = (83 × 1.0 + 16 × 0.5) / 99 × 100 = 91.9%

Critical:  (28 × 1.0 + 2 × 0.5) / 30 × 100 = 96.7%  (29.0 of 30 critical requirements)
Important: (54 × 1.0 + 13 × 0.5) / 67 × 100 = 90.3%  (60.5 of 67 important requirements)
Detail:    (1 × 1.0 + 1 × 0.5) / 2 × 100 = 75.0%  (1.5 of 2 detail requirements)
Overall:   91.9% (99 total requirements)

### 4. Top Gaps

- PRD-016 | `critical` | Make client cache safe to discard. This matters because the benchmark explicitly treats backend persistence as the source of truth; if cache-discard safety is left implicit, clearing browser state or reinstalling can still become a user-data loss bug.
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces. This matters because Ask, Scoop, Explore Similar, and Alchemy can all drift off-domain, lose spoiler safety, or become generic if the shared guardrails are not carried through as an implementation contract.
- PRD-017 | `important` | Avoid Docker requirement for cloud-agent compatibility. This matters because benchmark execution can fail before product behavior is even exercised if the runtime quietly assumes privileged local infrastructure.
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity. This matters because technically parsable AI output can still be low-quality or off-brand unless the plan specifies how discovery quality is judged and when bad output is rejected.
- PRD-051 | `important` | Preserve Show Detail narrative section order. This matters because the Detail page is a deliberately curated experience; changing the choreography can make the page feel busier, slower to scan, and less aligned with the product’s intended emotional arc.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with a small number of material holes rather than a structurally weak plan. The architecture, data model, route surface, and core feature decomposition are all concrete, but a few benchmark-specific execution guarantees and AI-quality contracts are left softer than the PRD requires.

#### Strength Clusters

The plan is strongest in Collection Data & Persistence, App Navigation & Discover Shell, Collection Home & Search, Concepts, Explore Similar & Alchemy, Person Detail, and Settings & Export. Those areas are covered with concrete schema choices, route boundaries, component breakdowns, save-trigger logic, explicit recommendation counts, and export mechanics rather than vague implementation intent.

#### Weakness Clusters

The weaker items are not random; they cluster in two patterns. One cluster is benchmark-operational rigor: cache-discard safety, Docker-free cloud-agent assumptions, and the edges of sync/settings persistence. The second cluster is product-quality contract fidelity: cross-surface AI guardrails, discovery-quality validation, the direct-answer Ask bar, and the exact Detail-page choreography.

#### Risk Assessment

If this plan were executed as-is, the most likely failure mode is a technically functional app that misses a few benchmark and product-quality expectations in review. QA or stakeholders would probably notice that the Detail page flow feels differently ordered than specified and that the AI surfaces have mechanics and parsers, but not the full explicit quality bar that keeps them consistently on-brand under edge cases.

#### Remediation Guidance

The remaining work is mostly specification tightening, not architectural reinvention. The plan needs a clearer execution contract for cloud-agent compatibility and disposable client cache behavior, plus a firmer AI behavior contract that translates the PRD’s voice, guardrail, and quality-bar documents into explicit implementation and validation requirements. The Detail page section order should also be rewritten to match the supporting doc exactly so the UX choreography is unambiguous.
