# Plan Evaluation

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
| PRD-001 | Use Next.js latest stable runtime | `critical` | full | §2.1 Top-level choices \| "Runtime \| Next.js 15 (App Router)" |  |
| PRD-002 | Use Supabase official client libraries | `critical` | full | §2.1 Top-level choices \| "Persistence \| Supabase (Postgres + Auth + RLS)" |  |
| PRD-003 | Ship `.env.example` with required variables | `critical` | full | §3.1 Environment variable interface \| ".env.example (committed, no secrets)" |  |
| PRD-004 | Ignore `.env*` secrets except example | `important` | full | §3.1 Environment variable interface \| ".gitignore must exclude .env, .env.local, .env.*.local" |  |
| PRD-005 | Configure build through env without code edits | `critical` | full | §1 Product Summary \| "Runs configured by environment variables — zero source edits" |  |
| PRD-006 | Keep secrets out of repo and server-only | `critical` | full | §3.1 Environment variable interface and §10.3 Security |  |
| PRD-007 | Provide app, test, reset command scripts | `critical` | full | §3.5 Scripts (package.json) |  |
| PRD-008 | Include repeatable schema evolution artifacts | `critical` | full | §4.5 Migrations & data continuity |  |
| PRD-009 | Use one stable namespace per build | `critical` | full | §3.2 Namespace model \| "stable per build/run" |  |
| PRD-010 | Isolate namespaces and scope destructive resets | `critical` | full | §3.2 Namespace model and §5.1 POST /api/_test/reset |  |
| PRD-011 | Attach every user record to `user_id` | `critical` | full | §3.3 Identity model \| "users table keyed by opaque id" |  |
| PRD-012 | Partition persisted data by namespace and user | `critical` | full | §3.4 Effective partition key and §4.3 Row-Level Security |  |
| PRD-013 | Support documented dev auth injection, prod-gated | `important` | full | §3.3 Identity model \| "Reads X-User-Id header if NEXT_PUBLIC_DEV_MODE=true" |  |
| PRD-014 | Real OAuth later needs no schema redesign | `important` | full | §3.3 Identity model \| "Production swap ... configuration + wiring, not schema redesign" |  |
| PRD-015 | Keep backend as persisted source of truth | `critical` | full | §1 Key invariants item 2 \| "Backend is source of truth" |  |
| PRD-016 | Make client cache safe to discard | `critical` | full | §2.1 Client data layer \| "Cache is disposable" and §6.3 Client state |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | `important` | full | §3.6 Docker is optional |  |
| PRD-018 | Overlay saved user data on every show appearance | `critical` | full | §1 Key invariants item 1 \| "User overlay always wins" |  |
| PRD-019 | Support visible statuses plus hidden `Next` | `important` | full | §4.1 shows schema \| "my_status text -- "active"\|"next"\|"later"\|"done"\|"quit"\|"wait"" |  |
| PRD-020 | Map Interested/Excited chips to Later interest | `critical` | full | §7.5 Relationship controls \| "Interested/Excited chips set status = Later + interest" |  |
| PRD-021 | Support free-form multi-tag personal tag library | `important` | full | §4.1 shows schema \| my_tags fields and §7.10 Tag filters |  |
| PRD-022 | Define collection membership by assigned status | `critical` | full | §4.1 shows schema \| "one row per (namespace, user, show_id) when user has saved" |  |
| PRD-023 | Save shows from status, interest, rating, tagging | `critical` | full | §5.1 Route Handler inventory \| "POST /api/shows/:id ... Applies save triggers + defaults" |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | `critical` | full | §9 Business Rules Matrix \| "Save (rating on unsaved)" and "Default save" |  |
| PRD-025 | Removing status deletes show and all My Data | `critical` | full | §5.1 Route Handler inventory \| "DELETE /api/shows/:id" and §9 "Remove from collection" |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | `critical` | full | §9 Business Rules Matrix \| "Re-add same show" |  |
| PRD-027 | Track per-field My Data modification timestamps | `critical` | full | §4.1 shows schema \| my_*_update_date fields |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | `important` | partial | §4.4 Merge policy and §9 Business Rules Matrix \| "newer timestamp wins per-field" | The plan uses timestamps for conflict resolution and Scoop freshness, but it never defines a recency-oriented sort or view that uses those timestamps for ordering. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | `critical` | full | §7.7 AI Scoop and §8.5 Scoop caching |  |
| PRD-030 | Keep Ask and Alchemy state session-only | `important` | full | §2.1 Top-level choices \| "Session-only state" and §8.6 Session-only vs persisted AI data |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | `critical` | full | §1 Key invariants item 3 and §8.4 AI recommendation resolution |  |
| PRD-032 | Show collection and rating tile indicators | `important` | full | §7.11 Tile indicators |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | `important` | full | §4.1 shows primary key and §9 Business Rules Matrix \| "Sync conflict" |  |
| PRD-034 | Preserve saved libraries across data-model upgrades | `critical` | full | §4.5 Migrations & data continuity |  |
| PRD-035 | Persist synced settings, local settings, UI state | `important` | full | §4.1 cloud_settings / ui_state and §7.9 Settings |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | `important` | full | §4.1 shows schema \| provider_data jsonb and §4.6 TransientShowExtras |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | `critical` | full | §4.4 Merge policy |  |
| PRD-038 | Provide filters panel and main screen destinations | `important` | full | §6.4 Navigation \| "Filters/navigation panel ... Main content area" |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | `important` | full | §6.4 Navigation \| "Persistent Find/Discover" |  |
| PRD-040 | Keep Settings in persistent primary navigation | `important` | full | §6.4 Navigation \| "Persistent ... Settings entry points" |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | `important` | full | §6.1 FindPage / ModeSwitcher and §6.4 Navigation |  |
| PRD-042 | Show only library items matching active filters | `important` | full | §7.1 Collection Home \| "Shows matching the selected filter(s) are displayed" |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | `important` | full | §7.1 Collection Home \| grouped by Active, Excited, Interested, Other |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | `important` | full | §7.10 Filters & views |  |
| PRD-045 | Render poster, title, and My Data badges | `important` | full | §7.1 Collection Home \| "Tiles show poster, title, in-collection badge, rating badge" |  |
| PRD-046 | Provide empty-library and empty-filter states | `detail` | full | §7.1 Collection Home \| "Empty states" |  |
| PRD-047 | Search by title or keywords | `important` | full | §7.2 Search \| "Text search by title/keywords" |  |
| PRD-048 | Use poster grid with collection markers | `important` | full | §7.2 Search and §7.11 Tile indicators |  |
| PRD-049 | Auto-open Search when setting is enabled | `detail` | full | §7.2 Search \| "Search on Launch" and §7.9 Settings |  |
| PRD-050 | Keep Search non-AI in tone | `important` | full | §8.1 Persona \| "Search has no AI voice" |  |
| PRD-051 | Preserve Show Detail narrative section order | `important` | full | §7.5 Show Detail Page \| "Sections in this exact order" |  |
| PRD-052 | Prioritize motion-rich header with graceful fallback | `important` | full | §7.5 Show Detail Page \| "Header media ... trailer inline ... graceful fallback" |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | `important` | full | §7.5 Show Detail Page \| "Core facts row ... + community score bar" |  |
| PRD-054 | Place status/interest controls in toolbar | `important` | full | §7.5 Show Detail Page \| "Relationship controls in the toolbar" |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | `critical` | full | §7.5 Show Detail Page \| "Tag picker ... auto-saves as Later + Interested" |  |
| PRD-056 | Auto-save unsaved rated show as Done | `critical` | full | §7.5 Show Detail Page \| "Rating slider — rating unsaved show auto-saves as Done" |  |
| PRD-057 | Show overview early for fast scanning | `important` | full | §7.5 Show Detail Page \| "Overview text + Scoop toggle/stream" |  |
| PRD-058 | Scoop shows correct states and progressive feedback | `important` | full | §7.7 AI Scoop \| streams; toggle copy; 4-hour freshness |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | `important` | full | §7.3 Ask about a Show and §7.5 Show Detail Page \| "Ask about this show" CTA |  |
| PRD-060 | Include traditional recommendations strand | `important` | full | §7.5 Show Detail Page \| "Recommendations strand" |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | `important` | full | §7.6 Explore Similar \| "Get Concepts" → "Explore Shows" |  |
| PRD-062 | Include streaming availability and person-linking credits | `important` | full | §7.5 Show Detail Page \| sections 9–10 (Streaming availability; Cast, Crew strands → Person Detail) |  |
| PRD-063 | Gate seasons to TV and financials to movies | `important` | full | §7.5 Show Detail Page \| sections 11–12 (TV-only seasons; movie budget/revenue) |  |
| PRD-064 | Keep primary actions early and page not overwhelming | `important` | full | §7.5 Show Detail Page \| actions clustered in toolbar and ordered before long-tail sections |  |
| PRD-065 | Provide conversational Ask chat interface | `important` | full | §7.3 Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | `important` | partial | §7.3 Ask \| "spoiler-safe; opinionated" and §8.2 Ask (no mentions) \| "1–3 tight paragraphs" | The plan covers tone and brevity, but it does not make the quality-bar rule explicit that Ask should answer directly within the first 3–5 lines. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | `important` | full | §7.3 Ask \| "mentioned shows appear in a horizontal strip" |  |
| PRD-068 | Open Detail from mentions or Search fallback | `important` | full | §7.3 Ask \| "Tile tap → Detail; unresolved entries hand off to Search" |  |
| PRD-069 | Show six random starter prompts with refresh | `important` | full | §7.3 Ask \| "6 random starter prompts ... refresh" |  |
| PRD-070 | Summarize older turns while preserving voice | `important` | full | §5.4 Conversation summarization \| "styled in-voice so the feel of the thread is preserved" |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | `important` | full | §7.3 Ask about a Show \| seedShowId / seedShowContext handoff |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | `critical` | full | §5.1 POST /api/ai/ask and §5.5 Mentioned-shows parser |  |
| PRD-073 | Retry malformed mention output once, then fallback | `important` | full | §5.3 AI adapter interface \| "Parse failure → one retry ... then fallback" |  |
| PRD-074 | Redirect Ask back into TV/movie domain | `important` | full | §8.2 Guardrails \| "Redirect back into TV/movies if user strays" |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | `important` | full | §8.3 Concept generation rules \| "structure / tone / emotion / relationship / craft / flavor" |  |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | `important` | full | §8.2 Surface contracts \| "Concepts \| Bullet list only" and §8.3 specificity rule |  |
| PRD-077 | Order concepts by strongest aha and varied axes | `important` | full | §8.3 Concept generation rules \| "Diversity" and "Order by strength" |  |
| PRD-078 | Require concept selection and guide ingredient picking | `important` | partial | §7.6 Explore Similar \| "Select 1+ chips (selection cap aligned with Alchemy's cap of 8)" | The plan requires concept selection, but it omits the UX guidance/copy that helps users understand they are picking the ingredients they want more of. |
| PRD-079 | Return exactly five Explore Similar recommendations | `important` | full | §7.6 Explore Similar \| "Explore Shows" → 5 recommendations |  |
| PRD-080 | Support full Alchemy loop with chaining | `important` | full | §7.4 Alchemy \| steps 1–5 including "More Alchemy!" |  |
| PRD-081 | Clear downstream results when inputs change | `important` | full | §7.4 Alchemy \| "Selecting/unselecting clears downstream recs" and "changing starting shows clears concepts + results" |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | `important` | partial | §7.4 Alchemy and §8.3 Concept generation rules \| "Default count 8, selection cap 8" | It requires shared multi-show concepts, but it never gives Alchemy a larger concept option pool than single-show Explore Similar. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | `important` | full | §7.4 Alchemy \| "reason that names which concept(s) it matches" |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | `important` | partial | §7.4 Alchemy and §8.7 Golden Set / AI quality gate | The plan validates taste alignment and concept-citing reasons, but it does not make "pleasantly unexpected but defensible" a concrete acceptance criterion. |
| PRD-085 | Keep one consistent AI persona across surfaces | `important` | full | §8.1 Persona \| "Single persona across surfaces" |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | `critical` | full | §8.2 Guardrails and §8.1 Persona |  |
| PRD-087 | Make AI warm, joyful, and light in critique | `important` | full | §8.1 Persona \| "Joy-forward + warm" / "even when critical" |  |
| PRD-088 | Structure Scoop as personal taste mini-review | `important` | full | §7.7 AI Scoop \| personal take → stack-up → Scoop paragraph → fit/warnings → verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | `important` | full | §8.1 Persona \| "Short when needed, lush when earned" and §8.2 Ask \| "1–3 tight paragraphs" |  |
| PRD-090 | Feed AI the right surface-specific context inputs | `important` | partial | §5.1 POST /api/ai/ask \| "input {messages, showContext?}" and §8.6 Session-only vs persisted AI data | The plan mentions some inputs, but it does not define a clear per-surface context contract for when to send library/My Data, selected concepts, and summarized conversation state. |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | `important` | full | §8.7 Golden Set / AI quality gate \| "Real-show integrity =2 required" |  |
| PRD-092 | Show person gallery, name, and bio | `important` | full | §7.8 Person Detail \| "Image gallery, name, bio" |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | `important` | full | §7.8 Person Detail \| "Analytics charts: average project ratings, top genres, projects-by-year" |  |
| PRD-094 | Group filmography by year | `important` | full | §7.8 Person Detail \| "Filmography grouped by year" |  |
| PRD-095 | Open Show Detail from selected credit | `important` | full | §7.8 Person Detail \| "tapping a credit opens that show's Detail" |  |
| PRD-096 | Include font size and Search-on-launch settings | `important` | full | §7.9 Settings \| "App: font size (XS..XXL), Search on launch" |  |
| PRD-097 | Support username, model, and API-key settings safely | `important` | full | §7.9 Settings \| "User: username" / "AI: ... AI model selection" / API key handling |  |
| PRD-098 | Export saved shows and My Data as zip | `critical` | full | §4.7 Export format and §7.9 Settings \| "Export My Data" |  |
| PRD-099 | Encode export dates using ISO-8601 | `important` | full | §4.7 Export format \| "ISO-8601 dates" |  |

## 3. Coverage Scores

Overall score:

score = (93 × 1.0 + 6 × 0.5) / 99 × 100 = 96.97%

Score by severity tier:

Critical:  (30 × 1.0 + 0 × 0.5) / 30 × 100 = 100.00%  (30.0 of 30 critical requirements)
Important:  (61 × 1.0 + 6 × 0.5) / 67 × 100 = 95.52%  (64.0 of 67 important requirements)
Detail:  (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.00%  (2.0 of 2 detail requirements)
Overall:   96.97% (99 total requirements)

## 4. Top Gaps

- PRD-090 | `important` | Feed AI the right surface-specific context inputs: Without an explicit per-surface context contract, Ask, Scoop, Explore Similar, and Alchemy can drift into generic outputs or use inconsistent grounding from the user's library and session state.
- PRD-066 | `important` | Answer directly with confident, spoiler-safe recommendations: If Ask is allowed to warm up too slowly, the chat will feel meandering and fail the product's promise of fast, confident conversational discovery.
- PRD-084 | `important` | Deliver surprising but defensible taste-aligned recommendations: If surprise-without-betrayal is not an explicit acceptance bar, discovery can regress into safe obvious picks or random genre adjacency without clearly failing the plan.
- PRD-082 | `important` | Generate shared multi-show concepts with larger option pool: If Alchemy receives no broader concept pool than single-show Explore Similar, multi-input blends will feel constrained and less rewarding despite the extra user effort.
- PRD-078 | `important` | Require concept selection and guide ingredient picking: If the UI requires concept selection without explaining the mental model, users are more likely to make arbitrary picks and get weaker Explore Similar / Alchemy outcomes.

## 5. Coverage Narrative

#### Overall Posture
This is a strong implementation plan with full critical coverage and a credible delivery path across architecture, data, testing, and major product surfaces. The remaining issues are important rather than existential: the plan under-specifies several AI discovery behavior contracts and leaves one timestamp-use detail unresolved.

#### Strength Clusters
The plan is strongest in Benchmark Runtime & Isolation, App Navigation & Discover Shell, Collection Home & Search, Show Detail & Relationship UX, Person Detail, and Settings & Export. It also covers Collection Data & Persistence with unusually concrete depth through schema design, merge rules, RLS, business-rule matrices, migration strategy, export format, and milestone/test alignment.

#### Weakness Clusters
The gaps are not randomly distributed. They cluster around AI behavioral specification: Ask quality-bar specifics, concept-selection guidance, multi-show concept breadth, surprise/taste calibration, and the exact context payload each AI surface should receive. Outside AI, the only notable gap is that timestamps are tracked and used for conflict resolution/freshness, but not clearly used for recency-oriented sorting.

#### Risk Assessment
If this plan were executed as-is, the app would likely function well on core CRUD, navigation, and persistence flows, but the AI discovery surfaces would be the first place a stakeholder or QA reviewer noticed drift from the spec. Ask could feel slower to the point than intended, Alchemy could feel narrower and safer than the product vision, and prompt grounding could vary by surface because the context contract is implied rather than fixed.

#### Remediation Guidance
The remaining planning work is mostly about tighter behavioral contracts, not new architecture. Strengthen §§7–8 with explicit acceptance criteria for Ask response shape, a surface-by-surface context-input matrix, concept-selection UX copy, and a distinct larger concept pool for multi-show Alchemy; strengthen §8.7 so surprise-without-betrayal is evaluated deliberately; and extend the data-behavior plan so timestamps clearly power at least one recency-oriented sort/view in addition to sync and freshness.
