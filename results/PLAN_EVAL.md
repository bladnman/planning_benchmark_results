# Plan Evaluation

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
| PRD-001 | Use Next.js latest stable runtime | critical | full | 2.1 Runtime and app structure |  |
| PRD-002 | Use Supabase official client libraries | critical | partial | 3.1 Persistence model; Phase 0: Supabase client setup | The plan commits to Supabase, but it never explicitly requires the official Supabase client libraries rather than wrappers or custom SDKs. |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Phase 0: env parsing, .env.example |  |
| PRD-004 | Ignore `.env*` secrets except example | important | partial | Phase 0: .gitignore validation | The plan mentions .gitignore validation, but it does not explicitly require ignoring `.env*` secrets while preserving `.env.example`. |
| PRD-005 | Configure build through env without code edits | critical | full | Product Readout; Phase 0 exit criteria: config-only setup |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | 2.1 Design rules; Phase 0 exit criteria: no secrets in source |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | Product Readout; 3.6 Namespace isolation and destructive reset |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | 3.6 Deliverables: migration files |  |
| PRD-009 | Use one stable namespace per build | critical | full | 4.1 Namespace resolution |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | 3.6 Namespace isolation and destructive reset; Phase 1 |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | 3.2 users; 4.2 User resolution |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | Product Readout; 3.3 saved_shows |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | partial | 4.2 User resolution | The dev-only selector/header override is planned and gated, but the plan does not call out a concrete documentation deliverable for how benchmark users invoke it. |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | 3.2 users; 4.3 Future auth migration |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Product Readout |  |
| PRD-016 | Make client cache safe to discard | critical | full | Product Readout; 3.4 user_settings |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | missing | none | The plan never states that Docker must remain optional or that hosted Supabase is the primary path for cloud-agent runs. |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | 1. Product Readout; 5.2 Canonical show mapper |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | Planned scope choices; 3.5 Business rules |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | 3.3 saved_shows; 8.1 Shared UI primitives |  |
| PRD-022 | Define collection membership by assigned status | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | 3.3 saved_shows; 10.1 Unit tests |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | full | 3.3 Important indexes; 3.5 Business rules; 10.1 Unit tests |  |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | Planned scope choices; 3.5 Business rules; 6.3 Scoop |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | Planned scope choices; 7.5 Ask state strategy; 7.7 Alchemy |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | 5.3 Recommendation resolution |  |
| PRD-032 | Show collection and rating tile indicators | important | full | 7.2 Collection Home; 7.3 Search |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | 3.4 user_settings; 3.5 Business rules encoded in persistence | The plan covers per-field merge rules, but it does not spell out duplicate detection and transparent merge behavior across synced libraries/settings. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | partial | 3.6 Deliverables: migration files | Migrations are planned, but the plan does not define how existing saved libraries and My Data are carried forward safely during data-model upgrades. |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | 3.4 user_settings |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | 3.3 saved_shows; 5.2 Canonical show mapper |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | 3.5 Business rules encoded in persistence |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | 7.1 App shell and navigation |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | 7.1 App shell and navigation |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | 7.1 App shell and navigation |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | 1. Product Readout; 7.3 Search; 7.5 Ask; 7.7 Alchemy |  |
| PRD-042 | Show only library items matching active filters | important | full | 7.2 Collection Home |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | 7.2 Collection Home |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | 7.2 Collection Home |  |
| PRD-045 | Render poster, title, and My Data badges | important | partial | 7.2 Collection Home | The plan covers poster tiles and My Data badges, but it never explicitly preserves title visibility alongside those badges. |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | 7.2 Collection Home |  |
| PRD-047 | Search by title or keywords | important | partial | 5.1 Catalog adapter; 7.3 Search | Search is described as a live global query flow, but the plan does not explicitly commit to title-or-keyword matching semantics. |
| PRD-048 | Use poster grid with collection markers | important | full | 7.3 Search |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | 7.3 Search |  |
| PRD-050 | Keep Search non-AI in tone | important | full | 7.3 Search |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | 7.4 Show Detail | The section list is close to the spec, but it collapses parts of the prescribed narrative hierarchy instead of preserving it exactly. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | partial | 7.4 Show Detail | Header media is included, but trailer-first emphasis and graceful poster/backdrop fallback behavior are not planned explicitly. |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | 7.4 Show Detail |  |
| PRD-054 | Place status/interest controls in toolbar | important | partial | 7.4 Critical interaction rules | The plan puts status chips early, but it allows placement outside the toolbar rather than locking the control into the toolbar. |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | 3.5 Business rules encoded in persistence; 7.4 Critical interaction rules |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | 3.5 Business rules encoded in persistence; 7.4 Critical interaction rules |  |
| PRD-057 | Show overview early for fast scanning | important | full | 7.4 Show Detail |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | partial | 6.3 Scoop; 7.4 Critical interaction rules | The plan covers Scoop persistence and generation, but it does not capture the detailed UI state/copy progression and visible generating feedback contract. |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | 7.4 Critical interaction rules; Phase 5 |  |
| PRD-060 | Include traditional recommendations strand | important | full | 7.4 Show Detail |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | 7.6 Explore Similar |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | 7.4 Show Detail; 7.8 Person Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | 7.4 Show Detail |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | partial | 7.4 Show Detail | The plan organizes the detail page, but it does not convert the “powerful but not overwhelming” principle into concrete layout or information-density constraints. |
| PRD-065 | Provide conversational Ask chat interface | important | full | 7.5 Ask |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | full | 7.5 Ask; 6.5 Voice and quality enforcement |  |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | 7.5 Ask |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | full | 5.3 Recommendation resolution; Phase 5 exit criteria |  |
| PRD-069 | Show six random starter prompts with refresh | important | full | 7.5 Ask |  |
| PRD-070 | Summarize older turns while preserving voice | important | partial | 6.2 Shared AI inputs; 7.5 Ask state strategy | The plan includes summarization, but it does not require summaries to preserve the same conversational persona rather than sounding like a sterile system note. |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | 7.5 Ask |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | partial | 6.3 Ask; 6.4 Parsing and fallback strategy | Structured Ask mentions are planned, but the exact `commentary` plus `showList` output contract is not specified. |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | 6.4 Parsing and fallback strategy |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | 6.5 Voice and quality enforcement |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | 7.6 Explore Similar; 7.7 Alchemy | The plan treats concepts as shared ingredients, but it never explicitly guards against drifting back into genre-style concept generation. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | full | 6.3 Concept generation |  |
| PRD-077 | Order concepts by strongest aha and varied axes | important | partial | 6.3 Concept generation | Specificity is planned, but the plan does not require ordering concepts by strongest “aha” value or deliberate variety across axes. |
| PRD-078 | Require concept selection and guide ingredient picking | important | partial | 7.6 Explore Similar; 7.7 Alchemy | Selection mechanics are covered, but the UX guidance that teaches users to pick ingredients they want more of is missing. |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | 6.3 Concept-based recommendations; 7.6 Explore Similar |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | 7.7 Alchemy |  |
| PRD-081 | Clear downstream results when inputs change | important | full | 7.7 Alchemy |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | partial | 7.7 Alchemy | The plan requires shared multi-show concepts, but it does not specify returning a larger option pool than single-show concept generation. |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | 6.3 Concept-based recommendations; 7.7 Alchemy |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | 10.4 AI quality validation | Taste-alignment checks are planned, but the generation plan does not specify how recommendations should deliberately balance surprise with defensibility. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | Product Readout; 6.5 Voice and quality enforcement |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | 6.1 Provider abstraction; 6.5 Voice and quality enforcement |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | partial | 6.5 Voice and quality enforcement | The plan captures honest friend-forward voice, but it does not encode the joy-forward and light-in-critique standard explicitly. |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | 6.3 Scoop |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | 6.3 Ask; 7.5 Ask |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | 6.2 Shared AI inputs |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | 10.4 AI quality validation | Quality checks are planned, but the rubric thresholds and hard-fail integrity gate are not carried into the plan as explicit acceptance criteria. |
| PRD-092 | Show person gallery, name, and bio | important | full | 7.8 Person Detail |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | 7.8 Person Detail |  |
| PRD-094 | Group filmography by year | important | full | 7.8 Person Detail |  |
| PRD-095 | Open Show Detail from selected credit | important | full | 7.8 Person Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | 7.9 Settings and Your Data |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | 3.4 user_settings; 7.9 Settings and Your Data |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | 7.9 Required data tooling; Phase 7 |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | 7.9 Required data tooling; 10.1 export serialization and ISO date formatting |  |

### 3. Coverage Scores

score = (full_count × 1.0 + partial_count × 0.5) / total_count × 100

Critical:  (27 × 1.0 + 3 × 0.5) / 30 × 100 = 95.0%  (28.5 of 30 critical requirements)
Important: (48 × 1.0 + 18 × 0.5) / 67 × 100 = 85.1%  (57.0 of 67 important requirements)
Detail:    (2 × 1.0 + 0 × 0.5) / 2 × 100 = 100.0%  (2.0 of 2 detail requirements)
Overall:   88.4% (99 total requirements)

### 4. Top Gaps

1. PRD-034 | `critical` | Preserve saved libraries across data-model upgrades — Without an explicit upgrade-continuity plan, a schema change can silently drop or corrupt the user’s saved library, which cuts against one of the product’s core trust promises.
2. PRD-072 | `critical` | Emit `commentary` plus exact `showList` contract — If Ask does not emit the exact mentions payload the UI expects, the mentioned-shows rail becomes unreliable even when the chat text itself looks correct.
3. PRD-002 | `critical` | Use Supabase official client libraries — Using unspecified Supabase clients risks auth, SSR, and session-handling drift in the exact runtime boundary the benchmark is testing.
4. PRD-017 | `important` | Avoid Docker requirement for cloud-agent compatibility — If the build assumes Docker or a local-only stack, cloud agents can fail before product work is even exercised.
5. PRD-033 | `important` | Sync libraries/settings consistently and merge duplicates — Cross-device or multi-session usage can produce duplicate or divergent records unless sync and merge behavior are specified beyond timestamp conflict resolution.

### 5. Coverage Narrative

#### Overall Posture

This is a structurally strong implementation plan with a small set of meaningful specification holes rather than a plan that misses the product. It covers the runtime choice, persistence model, core library behaviors, major surfaces, and testing strategy well enough to start execution, but several benchmark-operational details and AI behavior contracts are still under-specified.

#### Strength Clusters

The strongest coverage is in Collection Data & Persistence, App Navigation & Discover Shell, Collection Home & Search, Person Detail, and Settings & Export. The plan is especially concrete where the product’s durable truth lives: namespace/user isolation, save semantics, overlay precedence, detail/search/home flows, and export basics.

#### Weakness Clusters

The gaps cluster in three places rather than being random. First, benchmark-execution specifics are softer than they should be, especially around official Supabase client use, env-secret ignore rules, dev-auth documentation, and Docker-optional cloud-agent guidance. Second, AI contracts have several partials: exact Ask wire output, conversation-summary voice preservation, concept-generation quality rules, and discovery-quality gating. Third, Show Detail has multiple experiential partials around exact hierarchy, header fallback behavior, Scoop states, and anti-overwhelm constraints.

#### Risk Assessment

If a team executed this plan as-is, they would likely produce a working product skeleton with solid data rules, but QA would quickly find spec drift in the AI-facing seams and some benchmark setup expectations. The first visible issues would probably be generic or poorly ordered concepts, Ask mention payloads that do not match the UI contract, and detail-page behavior that feels close to the original but not exact.

#### Remediation Guidance

The remaining work is mostly specification tightening, not a wholesale re-plan. Add explicit benchmark deliverables for runtime/tooling compliance, spell out the exact AI request/response contracts and quality bars, and sharpen the detail-page UX contract with exact hierarchy, header fallback, toolbar placement, and Scoop state behavior. Also add upgrade-continuity and sync-integrity acceptance criteria so data durability is protected across schema evolution and multi-device usage.
