# Plan Evaluation

## 1. Requirements Extraction

### Benchmark Runtime & Isolation

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

### Collection Data & Persistence

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

### App Navigation & Discover Shell

- PRD-038 | `important` | Provide filters panel and main screen destinations | `product_prd.md > 6. App Structure & Navigation`
- PRD-039 | `important` | Keep Find/Discover in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-040 | `important` | Keep Settings in persistent primary navigation | `product_prd.md > 6. App Structure & Navigation`
- PRD-041 | `important` | Offer Search, Ask, Alchemy discover modes | `product_prd.md > 6. App Structure & Navigation`

### Collection Home & Search

- PRD-042 | `important` | Show only library items matching active filters | `product_prd.md > 7.1 Collection Home`
- PRD-043 | `important` | Group home into Active, Excited, Interested, Others | `product_prd.md > 7.1 Collection Home`
- PRD-044 | `important` | Support All, tag, genre, decade, score, media filters | `product_prd.md > 4.5 Filters (Ways to View the Collection)`
- PRD-045 | `important` | Render poster, title, and My Data badges | `product_prd.md > 7.1 Collection Home`
- PRD-046 | `detail` | Provide empty-library and empty-filter states | `product_prd.md > 7.1 Collection Home`
- PRD-047 | `important` | Search by title or keywords | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-048 | `important` | Use poster grid with collection markers | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-049 | `detail` | Auto-open Search when setting is enabled | `product_prd.md > 7.2 Search (Find → Search)`
- PRD-050 | `important` | Keep Search non-AI in tone | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`

### Show Detail & Relationship UX

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

### Ask Chat

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

### Concepts, Explore Similar & Alchemy

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

### AI Voice, Persona & Quality

- PRD-085 | `important` | Keep one consistent AI persona across surfaces | `supporting_docs/ai_voice_personality.md > 1. Persona Summary`
- PRD-086 | `critical` | Enforce shared AI guardrails across all surfaces | `supporting_docs/ai_prompting_context.md > 1. Shared Rules (All AI Surfaces)`
- PRD-087 | `important` | Make AI warm, joyful, and light in critique | `supporting_docs/ai_voice_personality.md > 2. Non-Negotiable Voice Pillars`
- PRD-088 | `important` | Structure Scoop as personal taste mini-review | `supporting_docs/ai_voice_personality.md > 4.1 Scoop (Show Detail "The Scoop")`
- PRD-089 | `important` | Keep Ask brisk and dialogue-like by default | `supporting_docs/ai_voice_personality.md > 4.2 Ask (Find → Ask)`
- PRD-090 | `important` | Feed AI the right surface-specific context inputs | `supporting_docs/ai_prompting_context.md > 2. Shared Inputs (Typical)`
- PRD-091 | `important` | Validate discovery with rubric and hard-fail integrity | `supporting_docs/discovery_quality_bar.md > 4. Scoring Rubric (Quick)`

### Person Detail

- PRD-092 | `important` | Show person gallery, name, and bio | `product_prd.md > 7.6 Person Detail Page`
- PRD-093 | `important` | Include ratings, genres, and projects-by-year analytics | `product_prd.md > 7.6 Person Detail Page`
- PRD-094 | `important` | Group filmography by year | `product_prd.md > 7.6 Person Detail Page`
- PRD-095 | `important` | Open Show Detail from selected credit | `product_prd.md > 7.6 Person Detail Page`

### Settings & Export

- PRD-096 | `important` | Include font size and Search-on-launch settings | `product_prd.md > 7.7 Settings & Your Data`
- PRD-097 | `important` | Support username, model, and API-key settings safely | `product_prd.md > 7.7 Settings & Your Data`
- PRD-098 | `critical` | Export saved shows and My Data as zip | `product_prd.md > 7.7 Settings & Your Data`
- PRD-099 | `important` | Encode export dates using ISO-8601 | `product_prd.md > 7.7 Settings & Your Data`

Total: 99 requirements (30 critical, 67 important, 2 detail) across 10 functional areas

## 2. Coverage Table

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |
| PRD-001 | Use Next.js latest stable runtime | critical | full | `1. Scope` says "Use Next.js latest stable" |  |
| PRD-002 | Use Supabase official client libraries | critical | full | `1. Scope` says "Use Supabase through official client libraries" |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | `2. Environment` lists `.env.example` and required variables |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | `2. Environment` specifies `.gitignore` excludes `.env*` while keeping `.env.example` |  |
| PRD-005 | Configure build through env without code edits | critical | full | `2. Environment` and DoD require app runs after filling `.env` |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | `1. Scope` and `2. Environment` restrict service role to server-only and ban committed secrets |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | `2. Environment` lists `dev`, `test`, `test:e2e`, and `test:reset` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | `3. Supabase Schema` and `14. Migrations` require repeatable migrations |  |
| PRD-009 | Use one stable namespace per build | critical | full | `1. Scope` uses stable `namespace_id` for each benchmark run |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | `3. Reset model` scopes `test:reset` to configured namespace |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | `3. Supabase Schema` scopes all user-owned records by `namespace_id` and `user_id` |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | `1. Scope` says effective partition is always `(namespace_id, user_id)` |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | `2. Environment` documents identity injection; `3. Access model` gates it by benchmark mode |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | `3. Access model` designs opaque `user_id` for future OAuth mapping |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | `1. Scope` treats backend as source of truth for saved data and settings |  |
| PRD-016 | Make client cache safe to discard | critical | full | `1. Scope` limits local storage to disposable UI preferences; DoD covers clearing local storage |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | `2. Environment` says hosted Supabase is primary and Docker/local Supabase optional only |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | `4. Display rule` requires every show-rendering surface to call overlay lookup |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | `4. Domain Model` includes `next`; toolbar chips omit `Next` while covering visible statuses |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | `4. Save/default rules` maps Interested and Excited to `later` plus interest |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `3. Supabase Schema` stores tags arrays and derives tag library from saved rows |  |
| PRD-022 | Define collection membership by assigned status | critical | full | `4. Show model` treats `my_status != null` as collection membership |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | `4. Save/default rules` enumerates all four save triggers |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | `4. Save/default rules` states default `later + interested` except rating-first `done` |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | `4. Save/default rules` clears row, status, interest, tags, rating, and AI Scoop |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | `4. Merge rules` says re-encountering saved show preserves My Data and refreshes metadata |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | `4. Merge rules` and `8. Relationship controls` require corresponding timestamps |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | `4. Merge rules` covers sync conflicts; `8. Scoop` covers 4-hour freshness | The plan does not explicitly use timestamps for collection sorting or recently updated views. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | `8. Scoop` covers 4-hour expiry and saved-only persistence |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | `3. Persistence choices`, `10. Session behavior`, and `11. Alchemy` keep AI session state transient |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | `9. Recommendation resolution` verifies catalog resolution and fallbacks |  |
| PRD-032 | Show collection and rating tile indicators | important | full | `4. Display rule` and `6. Collection Home` require both tile indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | full | `14. Conflict handling` covers timestamp conflicts, settings version, and duplicate merge |  |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | `14. Data continuity` requires preserving saved shows and My Data through migrations |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | `1. Scope`, `3. cloud_settings`, and `6. Collection Home` cover synced settings and local UI state |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | `3. Persistence choices` stores provider IDs only and excludes transient detail fetches |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | `4. Merge rules` uses non-empty public merge and per-field timestamp resolution |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | `6. Application Shell` includes persistent filter panel and main content region |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | `6. Application Shell` includes persistent Find/Discover entry |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | `6. Application Shell` includes persistent Settings entry |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | `7. Find/Discover Hub` defines Search, Ask, and Alchemy mode switcher |  |
| PRD-042 | Show only library items matching active filters | important | full | `6. Collection Home` applies selected sidebar filter plus media-type toggle |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | `6. Collection Home` lists all required groupings |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | `6. Collection Home` lists All, tag, No tags, genre, decade, score, and media toggle |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | `6. Collection Home` says tiles show poster, title, in-collection badge, and user rating badge |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | `6. Collection Home` defines both empty states |  |
| PRD-047 | Search by title or keywords | important | full | `7. Search mode` uses text input by title/keyword |  |
| PRD-048 | Use poster grid with collection markers | important | full | `7. Search mode` uses poster grid and overlays saved My Data/tile badges |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | `7. Search mode` respects Search on Launch setting |  |
| PRD-050 | Keep Search non-AI in tone | important | full | `7. Search mode` says Search tone is straightforward with no AI voice |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | `8. Show Detail Page` says preserve hierarchy and lists all sections | The listed hierarchy inserts toolbar relationship controls into the narrative order and does not exactly match the supporting spec's tag/overview sequence. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | `8. Show Detail Page` includes trailers when available and no-trailer/backdrop/logo states |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | `8. Show Detail Page` places core facts row second |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | `8. Relationship controls` explicitly uses toolbar chips |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | `8. Relationship controls` says tags picker saves unsaved item as Later + Interested |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | `8. Relationship controls` says My Rating bar saves unsaved item as Done |  |
| PRD-057 | Show overview early for fast scanning | important | full | `8. Show Detail Page` places overview plus Scoop immediately after tags |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | `8. Scoop` includes button copy, title, streaming, and freshness states |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | `10. Ask Chat` includes Ask About a Show with seeded show context |  |
| PRD-060 | Include traditional recommendations strand | important | full | `8. Show Detail Page` includes traditional similar/recommended strand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | `11. Explore Similar` defines Get Concepts -> select concepts -> Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | `8. Show Detail Page` includes streaming, cast/crew; `12. Person Detail` is reachable from cast/crew strands |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | `8. Show Detail Page` includes seasons for TV and budget vs revenue for movies |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | `8. Show Detail Page` clusters relationship, Scoop, and concepts early; `17. Risks` mitigates overwhelming detail |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | `10. Ask Chat` includes chat UI with user and assistant turns |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | `9. Shared AI rules` covers spoiler-safety and opinionated recommendations; `10. Ask` says brisk responses | The plan does not explicitly require direct answers within the first 3-5 lines for Ask / Explore Search Chat. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | `10. Ask Chat` renders mentioned shows row from current conversation |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | `10. Ask Chat` opens Detail when resolved or hands off to Search |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | `10. Ask Chat` includes 6 random starter prompts and refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | `10. Session behavior` summarizes older turns in 1-2 sentences while preserving persona |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | `10. Ask Chat` launches Ask About a Show with seeded show context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | `10. Structured mention contract` gives `commentary` and exact `showList` format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | `10. Structured mention contract` retries once then falls back to commentary/Search handoff |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | `9. Shared AI rules` says stay within TV/movies and redirect back |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | `11. Concept generation` defines concepts as core taste ingredients across axes beyond genre |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | `11. Concept generation` requires 1-3 words, bullet-list style, evocative, not generic |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | `11. Concept generation` orders strongest "aha" first and varies axes |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | full | `11. Selection rules` requires selected concept and ingredient-picking copy |  |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | `11. Explore Similar` returns 5 recommendations per round |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | `11. Alchemy` implements 2+ inputs, concepts, recommendations, and More Alchemy chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | `11. Selection rules` and `11. Alchemy` clear downstream recommendations/results on changes |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | `11. Concept generation` says multi-show concepts are shared across all selected inputs | The plan uses "Generate 8 concepts by default" generally and does not specify a larger multi-show option pool for Alchemy. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | `11. Explore Similar` and `11. Alchemy` require reasons to name selected concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | full | `11. Discovery quality acceptance` requires 1-2 surprising but defensible recommendations |  |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | `9. Shared AI rules` uses one fun, chatty TV/movie nerd persona across surfaces |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | `9. Shared AI rules` covers domain, spoiler, honesty, specificity, and actionability guardrails |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | `9. Shared AI rules` and `11. Discovery quality acceptance` require warm, playful, opinionated voice |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | `8. Scoop` requires personal voice, honest reception, 150-350 words, and emotional center |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | `10. Ask Chat` says assistant responses are brisk by default and use bullets |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | `9. AI Provider Layer` includes surface-specific prompt builders and context builders |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | `9. Testing` and `15. Manual/visual checks` use discovery quality rubric and real-show integrity |  |
| PRD-092 | Show person gallery, name, and bio | important | full | `12. Person Detail` fetches profile, image gallery, bio, and credits |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | `12. Person Detail` adds average ratings, top genres, and projects by year |  |
| PRD-094 | Group filmography by year | important | full | `12. Person Detail` groups credits by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | `12. Person Detail` opens Show Detail from selected credit |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | `13. Settings` includes font size/readability and Search on Launch |  |
| PRD-097 | Support username, model, and API-key settings safely | important | partial | `13. Settings` includes username, AI model, key status, env defaults, and optional encrypted user key storage | The plan does not commit to user-editable API-key settings; it treats key storage as optional/status-only for the first implementation. |
| PRD-098 | Export saved shows and My Data as zip | critical | full | `13. Export` generates `.zip` containing saved shows and all My Data |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | `13. Export` says encode dates as ISO-8601 |  |

## 3. Coverage Scores

Overall score:

```
score = (full_count × 1.0 + partial_count × 0.5) / total_count × 100
score = (94 × 1.0 + 5 × 0.5) / 99 × 100 = 97.5%
```

Score by severity tier:

```
Critical:  (30 × 1.0 + 0 × 0.5) / 30 × 100 = 100.0%  (30 of 30 critical requirements)
Important: (62 × 1.0 + 5 × 0.5) / 67 × 100 = 96.3%  (64.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2 of 2 detail requirements)
Overall:   97.5% (99 total requirements)
```

## 4. Top Gaps

1. PRD-097 | `important` | Support username, model, and API-key settings safely

   The plan covers username and model settings well, but it leaves user-editable API-key settings optional/status-only. If stakeholders expect in-app API key management rather than environment-only configuration, Settings will not fully match the PRD.

2. PRD-051 | `important` | Preserve Show Detail narrative section order

   The plan includes every major Show Detail section, but its ordered list is not an exact match for the supporting spec. This matters because the Detail page's first impression depends on a precise rhythm from media to facts to tags, overview, Scoop, and discovery actions.

3. PRD-066 | `important` | Answer directly with confident, spoiler-safe recommendations

   The plan specifies spoiler-safe, opinionated, brisk Ask behavior, but not the "direct answer within first 3-5 lines" quality bar. Without that acceptance criterion, Ask could become charming but too indirect.

4. PRD-028 | `important` | Use timestamps for sorting, sync, freshness

   The plan uses timestamps for conflict resolution and Scoop freshness, but does not explicitly apply them to sorting. This could weaken maintenance workflows where users expect recently updated collection items to surface predictably.

5. PRD-082 | `important` | Generate shared multi-show concepts with larger option pool

   The plan requires shared concepts for multi-show Alchemy, but it defaults concept generation to 8 everywhere. A same-size concept pool may make Alchemy feel less rich than the PRD intends for multi-show blending.

## 5. Coverage Narrative

#### Overall Posture

This is a strong, execution-ready implementation plan with minor but real specificity gaps. It fully covers all critical requirements, including infrastructure, persistence, namespace isolation, source-of-truth behavior, export, real-show recommendation mapping, and destructive test safety. The remaining gaps are important product fidelity details rather than structural blockers.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Collection Home & Search, Ask Chat mechanics, Concepts/Alchemy flow, Person Detail, and Export. It gives concrete architecture, schema, scripts, identity, reset, merge, overlay, AI parser, and testing commitments rather than vague feature names.

#### Weakness Clusters

The partial items cluster around fine-grained product experience contracts: exact Show Detail ordering, Ask response shape, timestamp use for sorting, larger Alchemy concept pools, and the degree of in-app API-key configurability. These are all important-tier requirements, so the plan's core data and architecture are intact while some UX and settings acceptance criteria need tightening.

#### Risk Assessment

If executed as-is, the most likely failure mode is not data loss or missing major screens; it is product feel drifting at the edges. A QA reviewer would probably notice that the app works end to end but Ask may not answer quickly enough, Detail may not follow the exact intended reading order, Alchemy may feel under-supplied with concept options, and Settings may not clearly support user-managed API keys.

#### Remediation Guidance

The plan needs targeted acceptance-criteria additions, not a redesign. Add explicit requirements for timestamp-backed sorting, exact Detail narrative order, direct-first Ask responses, larger multi-show concept generation, and safe user-facing API-key configuration or a clearly documented PRD-compliant benchmark-mode alternative.
