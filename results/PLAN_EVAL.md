### 1. Requirements Extraction

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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Section 2: "Use Next.js latest stable as the application runtime" |  |
| PRD-002 | Use Supabase official client libraries | critical | full | Section 2: "Use Supabase through official client libraries" |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Section 4 lists `.env.example` variables |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | Section 4: `.gitignore` ignores `.env*` except `.env.example` |  |
| PRD-005 | Configure build through env without code edits | critical | full | Section 4 and Acceptance Checklist: env-only configuration |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | Sections 3.2 and 4: server-only elevated keys and no committed secrets |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | Section 4 package scripts include `dev`, `test`, and `test:reset` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | Sections 4, 5.1, and Phase 0 call for migrations and `db:migrate` |  |
| PRD-009 | Use one stable namespace per build | critical | full | Sections 2 and 5.3 define `namespace_id` from env/run configuration |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | Sections 5.2 and 11.2 cover namespace filtering and scoped reset |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | Sections 2, 5.1, and 5.2 require `user_id` on user records |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | Sections 2 and 5.1 use `(namespace_id, user_id)` constraints |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | Sections 4, 5.2, and 5.3 specify dev-only identity injection and production gating |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | Sections 2, 5.3, and 13 preserve opaque user identity for future OAuth |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Section 2: durable user data server-side and disposable client cache |  |
| PRD-016 | Make client cache safe to discard | critical | full | Sections 2 and 6.2 explicitly require clearing local storage not to lose data |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Section 2 and Section 4: primary path does not require Docker |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | Sections 2, 5.4, and Acceptance Checklist list all appearances |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | Section 6.1 keeps `next` in model but not first-class UI |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | Sections 5.4 and 9.4 map Interested/Excited to `Later + Interest` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | Section 10: free-form tag creation and implicit tag library |  |
| PRD-022 | Define collection membership by assigned status | critical | full | Section 5.4: membership is `my_status` non-null |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | Section 5.4 enumerates all save triggers |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | Section 5.4 states tag/default save and rating-save defaults |  |
| PRD-025 | Removing status deletes show and all My Data | critical | partial | Section 5.4: "deletes the show row... or clears all My Data" | The plan preserves the clearing behavior, but leaves an ambiguous delete-row-or-clear-row choice instead of committing to the PRD's removal semantics. |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | Section 5.4 central merge service preserves user fields and refreshes catalog fields |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | Section 5.1 lists per-field update timestamps |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | Sections 5.4 and 8.4 use timestamps for merge conflicts and Scoop freshness | Sorting by modification timestamps is not explicitly planned. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | Section 8.4: 4-hour freshness and persist only if in collection |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | Sections 5.1, 8.3, and 8.7 keep AI sessions/results non-durable |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | Sections 3.2, 7.3, and 8.7 require catalog resolution |  |
| PRD-032 | Show collection and rating tile indicators | important | full | Section 7.1 displays in-collection and user-rating indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | Sections 5.1 and 5.4 cover synced settings and merge rules | Duplicate detection and transparent merging are not explicitly planned. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | Section 5.1 includes `app_metadata`; Phase 0 includes migrations | The plan does not explicitly require migrations to carry existing saved libraries and My Data forward without loss. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | Sections 5.1, 6.2, and 6.3 cover cloud settings, local settings, and UI state |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | Sections 5.1, 6.1, and 7.2 store provider data while keeping detail fetches transient |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | Section 5.4 implements `selectFirstNonEmpty` and timestamp conflict resolution |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | Sections 9.1 and 9.2 define navigation panel and main outlet |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | Section 9.1: persistent Find/Discover entry |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | Section 9.1: persistent Settings entry |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | Section 9.3 clear mode switcher with Search, Ask, Alchemy |  |
| PRD-042 | Show only library items matching active filters | important | full | Section 9.2 lists filters and media toggle composition |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | Section 9.2 lists the four home groups |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | Section 9.2 lists all required filters and media toggle |  |
| PRD-045 | Render poster, title, and My Data badges | important | partial | Sections 7.1 and 9.2 cover grids, saved indicators, and grouped Home | Home tile poster/title/My Data badge rendering is not explicitly specified as a Collection Home requirement. |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | Section 9.2 includes no-collection and no-results empty states |  |
| PRD-047 | Search by title or keywords | important | full | Section 7.1: text search by title/keyword |  |
| PRD-048 | Use poster grid with collection markers | important | full | Sections 7.1 and Phase 2 specify poster grid and saved indicators |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | Section 9.3 respects Search on Launch setting |  |
| PRD-050 | Keep Search non-AI in tone | important | partial | Section 7.1 implements Search as live external catalog search | The plan implies a non-AI Search mode but does not explicitly state Search should avoid AI voice/persona. |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | Section 9.4 provides a Detail hierarchy | The planned order moves My relationship controls into the numbered page sequence and differs from the exact narrative hierarchy in the detail spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | Sections 7.2 and 9.4 include videos/header carousel and fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | Section 9.4 places core facts and community score second |  |
| PRD-054 | Place status/interest controls in toolbar | important | partial | Section 9.4 places relationship controls near the top | The plan does not explicitly require status/interest controls to live in the toolbar rather than the scroll body. |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | Sections 5.4, 9.4, and 10 specify tag-to-save defaults |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | Sections 5.4 and 9.4 specify rating unsaved saves as Done |  |
| PRD-057 | Show overview early for fast scanning | important | full | Section 9.4 places overview after top relationship elements |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | Section 8.4 includes on-demand generation, streaming, cache, and persistence | The plan covers progression but not the explicit no-scoop, cached-scoop, open, and generating UI states from the spec. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | Sections 8.3 and 9.4 seed Ask with show context from Detail |  |
| PRD-060 | Include traditional recommendations strand | important | full | Sections 7.2 and 9.4 include traditional recommendations |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | Sections 8.6 and 9.4 use Get Concepts, chips, Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | Sections 7.2, 9.4, and 9.5 include providers and credit navigation |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | Sections 7.2 and 9.4 gate seasons and budget/revenue by media type |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | Sections 9.4 and 13 cluster actions early and mitigate page overload |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | Section 8.3 builds a session-only chat surface |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | Sections 8.2 and 8.3 require spoiler safety, opinionated tone, concise dialogue, and bullets |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | Section 8.3 renders mentioned shows in a horizontal strip |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | Sections 7.3 and 8.3 resolve mentions and fall back to Search/non-interactive display |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | Section 8.3: welcome state with 6 random starter prompts and refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | partial | Sections 8.1 and 8.3 summarize older turns after about 10 messages | The plan does not explicitly require summaries to preserve the Ask persona/tone. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | Section 8.3: Ask About This Show enters Ask with seeded show context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | Section 8.3 specifies `commentary`, `showList`, and exact parser format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | Sections 8.3 and 13 specify retry once and fallback |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | Section 8.2 and AI quality validation include TV/movie-domain refusal/redirect |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | Sections 8.5 and 8.6 define concept catalysts and avoid generic genre boilerplate |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | Section 8.5 states bullet-only, 1-3 words, specific, evocative, non-generic |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | Section 8.5 requires diversity across multiple axes | The plan covers varied axes but not ordering concepts by strongest aha first. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | Sections 8.6 and 8.7 require selected concepts before recommendations | The plan requires selection but does not include the "pick the ingredients you want more of" guidance. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | Sections 8.6 and Acceptance Checklist require 5 Explore Similar recs |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | Section 8.7 covers selection, concepts, recs, and More Alchemy chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | Sections 8.6 and 8.7 clear downstream concepts/results on changes |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | missing | none | The plan does not require multi-show concept generation to return a larger option pool than single-show generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | Sections 8.6 and 8.7 require reasons to mention selected concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | Sections 8.1, 8.7, and 11.4 validate taste alignment and concept reasons | The plan covers defensible taste alignment but not the "surprising but defensible" quality target. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | Sections 2 and 8.2 require consistent warm, opinionated AI surfaces |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | Section 8.2 lists shared guardrails for every AI surface |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | Sections 2, 8.2, and 11.4 cover warm tone, opinionated honesty, and voice validation |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | Section 8.4 requires 150-350 word mini taste review with required parts |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | Section 8.3 requires concise dialogue with bullets for recommendations |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | Section 8.1 enumerates library, show, concepts, turns, settings context |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | full | Section 11.4 uses the discovery quality rubric and real-show integrity threshold |  |
| PRD-092 | Show person gallery, name, and bio | important | full | Section 9.5 includes image gallery, name, and bio |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | Section 9.5 includes average ratings, top genres, projects by year |  |
| PRD-094 | Group filmography by year | important | full | Section 9.5 includes filmography grouped by year |  |
| PRD-095 | Open Show Detail from selected credit | important | full | Section 9.5 includes credits that open Show Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | Sections 6.2 and 9.6 include font size and Search on Launch |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | Sections 5.1, 6.3, and 9.6 include username, model, API keys, and secret handling |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | Sections 3.2, 6.3, 9.6, and Acceptance Checklist require zip export |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | Sections 6.1, 6.3, and Acceptance Checklist require ISO-8601 dates |  |

### 3. Coverage Scores

Overall score:

```
score = (85 × 1.0 + 13 × 0.5) / 99 × 100 = 92.4%
```

Critical:  (28 × 1.0 + 2 × 0.5) / 30 × 100 = 96.7%  (29.0 of 30 critical requirements)
Important: (55 × 1.0 + 11 × 0.5) / 67 × 100 = 90.3%  (60.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   92.4% (91.5 of 99 total requirements)

### 4. Top Gaps

1. PRD-034 | `critical` | Preserve saved libraries across data-model upgrades

The plan includes migrations and app metadata, but it does not explicitly state that saved libraries and My Data must survive data-model upgrades. Without this, a technically functional migration path could still regress the product's core durability promise.

2. PRD-025 | `critical` | Removing status deletes show and all My Data

The plan says removal may delete the row or clear My Data, which leaves the implementation choice ambiguous. Without a crisp rule, QA could see inconsistent collection membership, stale rows, or reappearing cleared data.

3. PRD-033 | `important` | Sync libraries/settings consistently and merge duplicates

The plan covers timestamp merges and settings persistence, but duplicate detection and transparent merging are not explicit. Without it, cross-device or repeated catalog imports can create confusing duplicate library entries.

4. PRD-054 | `important` | Place status/interest controls in toolbar

The plan places relationship controls near the top but not explicitly in the toolbar. Without this placement, the Detail page can lose the low-friction save behavior the spec treats as central to the first-15-seconds experience.

5. PRD-082 | `important` | Generate shared multi-show concepts with larger option pool

The plan does not distinguish multi-show concept pool size from single-show generation. Without a larger pool, Alchemy may feel underpowered because users have fewer shared ingredients to steer blended discovery.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with a high readiness posture. It covers the infrastructure baseline, data ownership model, core collection rules, AI contracts, navigation shell, major user journeys, and test strategy with enough specificity that a team could begin implementation from it. The remaining issues are not broad omissions; they are mostly precision gaps where the PRD expects exact UX placement, exact lifecycle semantics, or explicit quality criteria.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, Ask Chat, AI Voice/Persona/Quality, Person Detail, and Settings & Export. It is especially concrete about Next.js/Supabase, environment configuration, namespace/user partitioning, server-side source of truth, AI `showList` parsing, recommendation resolution, export format, and testing coverage.

#### Weakness Clusters

The partial and missing items cluster around exact behavioral fidelity rather than architecture. The biggest patterns are data lifecycle precision, such as removal and upgrade continuity; Detail page placement/state nuances, such as toolbar controls and Scoop states; and concept-discovery quality details, such as aha ordering, ingredient guidance, larger multi-show concept pools, and surprising-but-defensible recommendations.

#### Risk Assessment

If executed as-is, the most likely failure mode is a product that is architecturally solid but slightly off in the places users feel most: Detail save controls may not be as immediate, Alchemy may feel less rich than intended, and edge-case data lifecycle behavior may create trust concerns. QA would probably notice ambiguity around removal semantics, migration continuity, and AI concept quality before finding large missing screens.

#### Remediation Guidance

The plan needs targeted specification tightening, not a rewrite. Add explicit acceptance criteria for removal versus clearing, upgrade migrations that preserve every saved user field, duplicate merge behavior, toolbar placement, Scoop UI states, summary voice preservation, concept ordering, ingredient-picking copy, larger multi-show concept pools, and surprising recommendation quality. These should be added as precise implementation bullets and tests rather than broad architectural changes.
