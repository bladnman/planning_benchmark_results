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
| PRD-001 | Use Next.js latest stable runtime | critical | full | §1 Architecture Overview; §2.1 Initialize |  |
| PRD-002 | Use Supabase official client libraries | critical | full | §1 Architecture Overview; §5.1 Supabase Clients |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | §2.3 Environment Variables |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | §14 Compliance Checklist |  |
| PRD-005 | Configure build through env without code edits | critical | full | §2.3 Environment Variables; §14 Compliance Checklist |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | §2.3 Environment Variables; §5.1 Supabase Clients; §14 Compliance Checklist |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | §12.4 Scripts |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | §3 Database Schema & Migrations; §12.4 Scripts |  |
| PRD-009 | Use one stable namespace per build | critical | full | §1 Architecture Overview; §4.1 Auth Middleware |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | §3.2 Migration 002: Test Data Reset Function; §12.3 Namespace Isolation Test |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | §3.1 Migration 001: Core Schema; §4.1 Auth Middleware |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | §1 Architecture Overview; §5.2 Show Repository |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | §4.1 Auth Middleware |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | §4.1 Auth Middleware; §14 Compliance Checklist |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | §1 Architecture Overview |  |
| PRD-016 | Make client cache safe to discard | critical | full | §1 Architecture Overview; §10.4 Local UI State |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | §14 Compliance Checklist |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | §10.1 User Data Overlay Rule |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | §3.1 Migration 001: Core Schema; §9.3 Show Detail |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | §8.5 Auto-Save Business Logic; §9.3 Show Detail |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | §3.1 Migration 001: Core Schema; §9.1 Collection Home |  |
| PRD-022 | Define collection membership by assigned status | critical | partial | §8.5 Auto-Save Business Logic; §9.1 Collection Home | The plan relies on status-driven saves and grouping, but it never explicitly defines collection membership as the presence of an assigned status. |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | §8.5 Auto-Save Business Logic; §9.3 Show Detail |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | §8.5 Auto-Save Business Logic |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | §5.2 Show Repository; §9.6 Removal Confirmation Dialog |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | §5.3 Merge Logic; §10.1 User Data Overlay Rule |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | §3.1 Migration 001: Core Schema |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | §5.3 Merge Logic; §10.5 Data Continuity (Migrations) | The plan uses timestamps for merge safety and freshness, but it never assigns them to sorting behavior. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | §9.3 Show Detail |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | partial | §9.2 Find/Discover Hub; §10.4 Local UI State | Ask and Alchemy are modeled as UI flows, but the plan never explicitly states that their state is session-only and cleared on leave or reset. |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | §7.4 Show Resolution Pipeline |  |
| PRD-032 | Show collection and rating tile indicators | important | full | §9.1 Collection Home; §10.1 User Data Overlay Rule |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | §5.3 Merge Logic; §5.4 Settings Repository | It sketches conflict resolution, but it does not spell out cross-device library sync behavior or duplicate-item merge handling. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | §10.5 Data Continuity (Migrations) |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | §3.1 Migration 001: Core Schema; §10.4 Local UI State |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | partial | §3.1 Migration 001: Core Schema; §6.2 TMDB → Show Mapper | Provider IDs are persisted, but the plan never explicitly calls out detail-only fetch fields as transient and non-persistent. |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | §5.3 Merge Logic |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | §2.2 Directory Layout; §9.1 Collection Home |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | §2.2 Directory Layout; `components/layout/TopNav.tsx` |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | §2.2 Directory Layout; `components/layout/TopNav.tsx` |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | §9.2 Find/Discover Hub |  |
| PRD-042 | Show only library items matching active filters | important | full | §9.1 Collection Home |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | §9.1 Collection Home |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | §5.2 Show Repository; §9.1 Collection Home |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | §9.1 Collection Home |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | §9.1 Collection Home |  |
| PRD-047 | Search by title or keywords | important | full | §6.1 TMDB Client; §9.2 Search Mode |  |
| PRD-048 | Use poster grid with collection markers | important | full | §9.2 Search Mode |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | §9.2 Search Mode; §9.5 Settings |  |
| PRD-050 | Keep Search non-AI in tone | important | partial | §9.2 Search Mode | The plan keeps Search structurally separate from AI, but it never explicitly carries forward the non-AI tone rule that should guide copy and interaction design. |
| PRD-051 | Preserve Show Detail narrative section order | important | full | §9.3 Show Detail |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | §9.3 Show Detail |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | §9.3 Show Detail |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | §9.3 Show Detail |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | §8.5 Auto-Save Business Logic; §9.3 Show Detail |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | §8.5 Auto-Save Business Logic; §9.3 Show Detail |  |
| PRD-057 | Show overview early for fast scanning | important | full | §9.3 Show Detail |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | §9.3 Show Detail |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | §9.2 Ask Mode; §9.3 Show Detail |  |
| PRD-060 | Include traditional recommendations strand | important | full | §9.3 Show Detail |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | §9.3 Show Detail |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | §9.3 Show Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | §9.3 Show Detail |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | §9.3 Show Detail; §11 Phase 4: Show Detail | The section order puts primary actions early, but the plan does not describe layout or density tactics that keep the page powerful without feeling busy. |
| PRD-065 | Provide conversational Ask chat interface | important | full | §9.2 Ask Mode |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | §7.2 Ask; §9.2 Ask Mode | It captures spoiler safety and confident picks, but it does not preserve the quality-bar requirement to answer directly within the first few lines. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | §9.2 Ask Mode |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | partial | §7.4 Show Resolution Pipeline; §9.2 Ask Mode | Unresolved mentions may become non-interactive or search-linked, but the user handoff back into Search is not concretely specified. |
| PRD-069 | Show six random starter prompts with refresh | important | full | §9.2 Ask Mode |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | §7.2 Ask; §7.2 Conversation Summarization |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | §9.2 Ask Mode; §9.3 Show Detail |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | §7.2 Ask |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | §7.3 Output Parser; §10.3 Error Handling |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | §7.2 Ask |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | full | §7.2 Concepts |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | §7.2 Concepts |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | §7.2 Concepts |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | §9.2 Alchemy Mode; §9.3 Show Detail | The flow requires concept selection, but it omits the UX guidance that teaches users to pick the taste ingredients they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | §9.3 Show Detail |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | §9.2 Alchemy Mode |  |
| PRD-081 | Clear downstream results when inputs change | important | full | §9.2 Alchemy Mode |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | §7.2 Concepts | The plan requires shared multi-show concepts, but it never expands the concept option pool for Alchemy beyond the single-show default. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | §7.2 Concept-Based Recommendations |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | §7.2 Concept-Based Recommendations; §7.4 Show Resolution Pipeline | It covers taste alignment and resolvability, but it does not preserve the explicit surprising-but-defensible recommendation quality target. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | §7.2 Prompt Templates |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | §7.2 Prompt Templates; §7.2 Ask; §7.2 Scoop; §7.2 Concepts |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | §7.2 Scoop; §7.2 Ask | The persona is defined as fun and chatty, but the plan does not explicitly preserve the warm, joy-forward, light-in-critique balance from the voice spec. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | §7.2 Scoop |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | §7.2 Ask |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | §7.2 Scoop; §7.2 Ask; §7.2 Concepts |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | §7.4 Show Resolution Pipeline; §12.2 Integration Tests | Real-show integrity is tested, but the plan never introduces the discovery scoring rubric or a hard quality gate for voice and taste alignment regressions. |
| PRD-092 | Show person gallery, name, and bio | important | full | §9.4 Person Detail |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | §9.4 Person Detail |  |
| PRD-094 | Group filmography by year | important | full | §9.4 Person Detail |  |
| PRD-095 | Open Show Detail from selected credit | important | full | §9.4 Person Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | §9.5 Settings |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | §9.5 Settings |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | §8.4 Settings & Data; §9.5 Settings |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | §9.5 Settings |  |

## 3. Coverage Scores

Critical:  (29 × 1.0 + 1 × 0.5) / 30 × 100 = 98.3%  (29.5 of 30 critical requirements)
Important: (54 × 1.0 + 13 × 0.5) / 67 × 100 = 90.3%  (60.5 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   (85 × 1.0 + 14 × 0.5) / 99 × 100 = 92.9%  (92.0 of 99 total requirements)

## 4. Top Gaps

1. PRD-022 | critical | Define collection membership by assigned status: The plan never codifies status as the single membership invariant. Without that rule, engineers can treat partially saved rows, transient records, or cleanup paths inconsistently, which risks collection drift and removal bugs.
2. PRD-091 | important | Validate discovery with rubric and hard-fail integrity: The AI stack is specified, but the plan does not preserve the rubric that decides whether recommendations are actually good. Without that gate, discovery can regress in tone or taste alignment while still looking technically complete.
3. PRD-033 | important | Sync libraries/settings consistently and merge duplicates: Cross-device consistency is only sketched at a high level. If duplicate detection and merge semantics are left implicit, synced libraries and settings can diverge in hard-to-debug ways.
4. PRD-084 | important | Deliver surprising but defensible taste-aligned recommendations: Alchemy and Explore Similar may be taste-aware but still too safe. Without an explicit surprise-without-betrayal target, the recommendation engine can collapse into generic adjacent picks.
5. PRD-064 | important | Keep primary actions early and page not overwhelming: The plan preserves Detail-page section order, but not the anti-clutter intent behind it. That makes the app vulnerable to a screen that technically includes everything yet feels heavier and harder to scan than the spec intends.

## 5. Coverage Narrative

#### Overall Posture

This is a strong, implementation-ready plan with high explicit coverage and no completely unaddressed catalog requirements. The core architecture, data model, major routes, and user-facing feature map are all concrete. The remaining issues are mostly underspecified behavioral contracts and quality bars rather than missing feature surfaces.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, App Navigation & Discover Shell, Person Detail, Settings & Export, and the structural parts of Show Detail & Relationship UX. Those areas name concrete schema choices, routes, components, and build phases, which means an implementer can execute them with relatively little ambiguity.

#### Weakness Clusters

The partial coverage clusters in three places: persistence semantics that still need crisp invariants (collection membership, session-only AI state, sync and duplicate handling), AI quality contracts (direct Ask answers, recommendation surprise quality, voice warmth, rubric-driven validation), and softer experience guardrails (Search tone, concept-picking guidance, keeping Detail powerful without feeling crowded). This is a pattern, not random scatter.

#### Risk Assessment

If this plan were executed as-is, the product would likely ship with the right screens and plumbing but still feel mechanically correct in a few critical moments. Product QA would most likely notice AI discovery quality drift first, followed by inconsistent interpretations of collection membership, sync behavior, and Search or Detail experience rules that were implied but not pinned down.

#### Remediation Guidance

The remaining work is mostly plan sharpening, not a major rewrite. Add explicit invariants for collection membership and session-only AI state, spell out sync and duplicate-merge behavior, convert the AI voice and discovery docs into testable acceptance criteria, and add a few UX-level notes for Search tone, concept guidance, and Detail-page density management.
