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
| PRD-001 | Use Next.js latest stable runtime | critical | full | Technology Stack: "Next.js (latest stable)"; Phase 1.1 initializes Next.js App Router |  |
| PRD-002 | Use Supabase official client libraries | critical | full | Technology Stack: "Supabase (hosted)"; Phase 1.4 Supabase Client Setup |  |
| PRD-003 | Ship `.env.example` with required variables | critical | full | Phase 1.2 Environment Variables lists `.env.example` with required vars and comments |  |
| PRD-004 | Ignore `.env*` secrets except example | important | full | Phase 1.2 `.gitignore` additions exclude `.env`, `.env.local`, `.env.*.local` |  |
| PRD-005 | Configure build through env without code edits | critical | full | Phase 1.2 env variables cover Supabase, namespace, catalog, AI, and app config |  |
| PRD-006 | Keep secrets out of repo and server-only | critical | full | Phase 1.4 server client uses service role server-only; checklist says AI key never committed |  |
| PRD-007 | Provide app, test, reset command scripts | critical | full | Phase 1.3 npm scripts includes `dev`, `test`, and `test:reset` |  |
| PRD-008 | Include repeatable schema evolution artifacts | critical | full | Phase 2 Migration Files and Phase 13.2 forward-only `supabase/migrations/` |  |
| PRD-009 | Use one stable namespace per build | critical | full | Architectural Decision 2: `namespace_id` stable per build/run via `NAMESPACE_ID` |  |
| PRD-010 | Isolate namespaces and scope destructive resets | critical | full | Identity model uses namespace predicates; Phase 15.2 reset deletes by namespace only |  |
| PRD-011 | Attach every user record to `user_id` | critical | full | Shows, cloud_settings, and app_metadata tables include `user_id`; all queries use partition |  |
| PRD-012 | Partition persisted data by namespace and user | critical | full | Architectural Decision 2: all Supabase queries include `namespace_id` and `user_id` |  |
| PRD-013 | Support documented dev auth injection, prod-gated | important | full | Phase 3.2 accepts `X-User-Id` only in development/test and validates real auth in production |  |
| PRD-014 | Real OAuth later needs no schema redesign | important | full | Phase 3.3 maps OAuth user id into existing `user_id` with no schema changes |  |
| PRD-015 | Keep backend as persisted source of truth | critical | full | Architectural Decision 3: all user-owned data lives in Supabase |  |
| PRD-016 | Make client cache safe to discard | critical | full | Architectural Decision 3: clearing client cache never loses data |  |
| PRD-017 | Avoid Docker requirement for cloud-agent compatibility | important | full | Technology Stack: Docker explicitly not required; hosted Supabase path |  |
| PRD-018 | Overlay saved user data on every show appearance | critical | full | Phase 14.1 applies overlay in lists, search, AI recommendations, and person filmography |  |
| PRD-019 | Support visible statuses plus hidden `Next` | important | full | Migration enum includes `next`; UI chips expose Active, Interested, Excited, Done, Wait, Quit |  |
| PRD-020 | Map Interested/Excited chips to Later interest | critical | full | Phase 8.1 Status Toolbar maps Interested/Excited to `later` plus `myInterest` |  |
| PRD-021 | Support free-form multi-tag personal tag library | important | full | `my_tags` array, TagPicker, and Filter Panel "one entry per distinct tag" |  |
| PRD-022 | Define collection membership by assigned status | critical | full | CRUD and status removal model make persisted collection entries status-owned; DELETE removes item |  |
| PRD-023 | Save shows from status, interest, rating, tagging | critical | full | Phase 5.2 Auto-Save Rules covers rating, tag, and status/interest updates |  |
| PRD-024 | Default save to Later/Interested except rating-save Done | critical | full | Phase 5.3 Default Values states Later/Interested, with rating exception Done |  |
| PRD-025 | Removing status deletes show and all My Data | critical | full | Phase 14.2 StatusRemovalModal executes DELETE that clears all My Data server-side |  |
| PRD-026 | Re-add preserves My Data and refreshes public data | critical | full | Phase 4.3 `mergeCatalogShow` preserves user fields and Phase 5.1 upsert merges fresh catalog data |  |
| PRD-027 | Track per-field My Data modification timestamps | critical | full | Migration fields and Phase 5.4 set `*_update_date` for each PATCH route |  |
| PRD-028 | Use timestamps for sorting, sync, freshness | important | partial | Phase 4.3 uses timestamps for merge conflicts; Phase 8.2 uses `aiScoopUpdateDate` for 4h freshness | The plan does not specify timestamp-based sorting behavior for collection views or recent-update ordering. |
| PRD-029 | Persist Scoop only for saved shows, 4h freshness | critical | full | Phase 8.2 persists scoop only if in collection and regenerates after 4 hours |  |
| PRD-030 | Keep Ask and Alchemy state session-only | important | full | Ask reset clears session; Alchemy changing inputs clears concepts/results with no persistence tables |  |
| PRD-031 | Resolve AI recommendations to real selectable shows | critical | full | Phase 14.3 resolves by external ID and renders found items as tappable ShowTiles |  |
| PRD-032 | Show collection and rating tile indicators | important | full | Phase 7.2 Show Tile has in-collection and user rating badges |  |
| PRD-033 | Sync libraries/settings consistently and merge duplicates | important | partial | Supabase is source of truth; cloud_settings exists; merge logic uses timestamps for stored shows | Duplicate detection/transparent merge and explicit cross-device settings conflict policy are not planned. |
| PRD-034 | Preserve saved libraries across data-model upgrades | critical | full | Phase 13 migration tracking uses additive, idempotent migrations and backfills |  |
| PRD-035 | Persist synced settings, local settings, UI state | important | full | Phase 2.2 local settings and UI state; Phase 12.2 cloud_settings for user/API/model settings |  |
| PRD-036 | Keep provider IDs persisted and detail fetches transient | important | full | Migration stores `provider_data`; Phase 4.2 marks credits/seasons/videos/recs/similar/images transient |  |
| PRD-037 | Merge catalog fields safely and maintain timestamps | critical | full | Phase 4.3 `mergeCatalogShow` uses `selectFirstNonEmpty`, timestamp user fields, `detailsUpdateDate` |  |
| PRD-038 | Provide filters panel and main screen destinations | important | full | Phase 6.2 Filter Panel plus routes for home, detail, find, person, and settings |  |
| PRD-039 | Keep Find/Discover in persistent primary navigation | important | full | Phase 6.3 Primary Navigation includes Find/Discover |  |
| PRD-040 | Keep Settings in persistent primary navigation | important | full | Phase 6.3 Primary Navigation includes Settings |  |
| PRD-041 | Offer Search, Ask, Alchemy discover modes | important | full | Phase 9.1 FindHub has Search, Ask, and Alchemy tabs |  |
| PRD-042 | Show only library items matching active filters | important | full | Phase 7.1 filter parameter reduces the show pool before grouping |  |
| PRD-043 | Group home into Active, Excited, Interested, Others | important | full | Phase 7.1 Status Grouping lists Active, Excited, Interested, Other |  |
| PRD-044 | Support All, tag, genre, decade, score, media filters | important | full | Phase 6.2 Filter Panel includes All, tags, genre, decade, community score, and media type |  |
| PRD-045 | Render poster, title, and My Data badges | important | full | Phase 7.2 Show Tile includes poster image, title, collection badge, and rating badge |  |
| PRD-046 | Provide empty-library and empty-filter states | detail | full | Phase 7.3 Empty States covers no collection and no filtered results |  |
| PRD-047 | Search by title or keywords | important | full | Phase 9.2 Search Mode text input calls catalog search query endpoint |  |
| PRD-048 | Use poster grid with collection markers | important | full | Phase 9.2 Search results are poster grid with in-collection indicator |  |
| PRD-049 | Auto-open Search when setting is enabled | detail | full | Phase 9.2 auto-opens/focuses Search when `autoSearch = true` |  |
| PRD-050 | Keep Search non-AI in tone | important | full | Phase 9.2 Search is a catalog text input/grid, separate from AI routes |  |
| PRD-051 | Preserve Show Detail narrative section order | important | partial | Phase 8 says it follows the narrative hierarchy and lists all sections | The listed order inserts status/rating before tags and overview, so it does not preserve the exact detail-page narrative order from the spec. |
| PRD-052 | Prioritize motion-rich header with graceful fallback | important | full | Phase 8.1 Header Media Carousel uses backdrops/posters/logos/trailers with poster fallback |  |
| PRD-053 | Surface year, runtime/seasons, and community score early | important | full | Phase 8.1 Core Facts Row includes year, runtime/seasons/episodes, and score bar |  |
| PRD-054 | Place status/interest controls in toolbar | important | full | Phase 8.1 Status/Interest Toolbar is near top and not in scroll body |  |
| PRD-055 | Auto-save unsaved tagged show as Later/Interested | critical | full | Phase 5.2 tag update auto-saves as `later` plus `interested` |  |
| PRD-056 | Auto-save unsaved rated show as Done | critical | full | Phase 5.2 rating update auto-saves as `done` |  |
| PRD-057 | Show overview early for fast scanning | important | full | Phase 8.1 places Overview before AI discovery, providers, cast, seasons, and financials |  |
| PRD-058 | Scoop shows correct states and progressive feedback | important | full | Phase 8.2 defines no_scoop, cached_scoop, open, streaming, and Generating state |  |
| PRD-059 | Ask-about-show deep-link seeds Ask context | important | full | Phase 8.1 CTA navigates with `showId`; Phase 9.3 fetches show data and seeds context |  |
| PRD-060 | Include traditional recommendations strand | important | full | Phase 8.1 includes Traditional Recommendations Strand |  |
| PRD-061 | Explore Similar uses CTA-first concept flow | important | full | Phase 8.3 starts with Get Concepts, then select concepts, then Explore Shows |  |
| PRD-062 | Include streaming availability and person-linking credits | important | full | Phase 8.1 includes Streaming Availability and Cast/Crew strands linking to Person Detail |  |
| PRD-063 | Gate seasons to TV and financials to movies | important | full | Phase 8.1 includes Seasons Panel TV only and Budget vs Revenue for movies |  |
| PRD-064 | Keep primary actions early and page not overwhelming | important | full | Phase 8.1 clusters status, rating, tags, scoop, Ask, and Explore before long-tail info |  |
| PRD-065 | Provide conversational Ask chat interface | important | full | Phase 9.3 Ask Mode provides chat UI with user typing and streaming assistant turns |  |
| PRD-066 | Answer directly with confident, spoiler-safe recommendations | important | partial | Phase 10.5 shared persona includes spoiler-safe and opinionated honesty; Ask prompt picks favorites confidently | The plan does not encode the direct-answer-within-first-lines acceptance bar for Ask responses. |
| PRD-067 | Show horizontal mentioned-shows strip from chat | important | full | Phase 9.3 `MentionedShowsStrip` updates as shows are parsed |  |
| PRD-068 | Open Detail from mentions or Search fallback | important | partial | Phase 9.3 parses mentioned shows and falls back to commentary plus Search handoff on failure | The plan does not explicitly specify tapping a mentioned-show strip item opens Show Detail when resolution succeeds. |
| PRD-069 | Show six random starter prompts with refresh | important | full | Phase 9.3 welcome view has six random starter prompts and Refresh |  |
| PRD-070 | Summarize older turns while preserving voice | important | full | Phase 10.2 summarization endpoint condenses older turns into same persona |  |
| PRD-071 | Seed Ask-about-show sessions with show handoff | important | full | Phase 9.3 `Ask About a Show` fetches show data and seeds system context |  |
| PRD-072 | Emit `commentary` plus exact `showList` contract | critical | full | Phase 9.3 structured output uses `commentary` and exact `Title::externalId::mediaType;;...` format |  |
| PRD-073 | Retry malformed mention output once, then fallback | important | full | Phase 9.3 invalid format retries once, then falls back to commentary plus Search handoff |  |
| PRD-074 | Redirect Ask back into TV/movie domain | important | full | Phase 10.5 shared prompt rules include TV/movie domain restriction |  |
| PRD-075 | Treat concepts as taste ingredients, not genres | important | partial | Phase 10.3 requires evocative concepts, varied axes, and no generic placeholders | The plan does not explicitly state that concepts are taste ingredients rather than genre labels or plot categories. |
| PRD-076 | Return bullet-only, 1-3 word, non-generic concepts | important | partial | Phase 10.3 returns 1-3 word evocative concepts with no generic placeholders | The concept API output is specified as JSON string arrays, but the prompt/output contract does not preserve the bullet-only requirement. |
| PRD-077 | Order concepts by strongest aha and varied axes | important | full | Phase 10.3 concepts are ordered by strength and varied across axes |  |
| PRD-078 | Require concept selection and guide ingredient picking | important | full | Phase 8.3 requires selecting 1+ concepts; Phase 8.1/8.3 use concept chips before recommendations |  |
| PRD-079 | Return exactly five Explore Similar recommendations | important | full | Phase 8.3 returns five show objects; Phase 10.4 counts are five for explore |  |
| PRD-080 | Support full Alchemy loop with chaining | important | full | Phase 9.4 covers select shows, conceptualize, alchemize, and More Alchemy chaining |  |
| PRD-081 | Clear downstream results when inputs change | important | full | Phase 9.4 says changing selected shows or concepts clears downstream results |  |
| PRD-082 | Generate shared multi-show concepts with larger option pool | important | full | Phase 9.4 and Phase 10.3 use multi-show concepts with 8+ options |  |
| PRD-083 | Cite selected concepts in concise recommendation reasons | important | full | Phase 10.4 recommendation output includes reason; Phase 9.4 reasons reference selected concepts |  |
| PRD-084 | Deliver surprising but defensible taste-aligned recommendations | important | partial | Phase 10.4 grounds recommendations in concepts, source shows, and user library with reasons | The plan lacks an explicit quality bar for pleasantly unexpected but defensible recommendations. |
| PRD-085 | Keep one consistent AI persona across surfaces | important | full | Phase 10.5 imports one shared persona header from `base.ts` for every surface |  |
| PRD-086 | Enforce shared AI guardrails across all surfaces | critical | full | Phase 10.5 shared rules cover spoiler safety, honesty, and TV/movie restriction |  |
| PRD-087 | Make AI warm, joyful, and light in critique | important | full | Phase 10.5 uses a "fun, chatty TV/movie nerd friend" persona and opinionated honesty |  |
| PRD-088 | Structure Scoop as personal taste mini-review | important | full | Phase 10.1 Scoop includes personal take, stack-up, centerpiece, fit/warnings, and verdict |  |
| PRD-089 | Keep Ask brisk and dialogue-like by default | important | full | Phase 9.3 chat UI and Phase 10.5 surface prompt use friend-like dialogue |  |
| PRD-090 | Feed AI the right surface-specific context inputs | important | full | Phase 10 routes pass show context, user library, selected concepts, source shows, and recent turns |  |
| PRD-091 | Validate discovery with rubric and hard-fail integrity | important | partial | Phase 14.3 resolves AI recommendations to real catalog items and Phase 15.3 tests the parser | The plan does not include discovery-rubric validation, golden-set checks, or hard-fail scoring for AI output quality. |
| PRD-092 | Show person gallery, name, and bio | important | full | Phase 11 Person Detail includes profile image/gallery, name, and bio |  |
| PRD-093 | Include ratings, genres, and projects-by-year analytics | important | full | Phase 11 Analytics Charts include average ratings, top genres, and projects by year |  |
| PRD-094 | Group filmography by year | important | full | Phase 11 Filmography groups credits by year descending |  |
| PRD-095 | Open Show Detail from selected credit | important | full | Phase 11 filmography items are tappable ShowTiles opening Show Detail |  |
| PRD-096 | Include font size and Search-on-launch settings | important | full | Phase 12.1 includes font size and Search on launch local settings |  |
| PRD-097 | Support username, model, and API-key settings safely | important | full | Phase 12.2 includes username, AI key, AI model, catalog key, masking, env precedence, and no commits |  |
| PRD-098 | Export saved shows and My Data as zip | critical | full | Phase 12.3 `Export My Data` builds a ZIP with shows and My Data |  |
| PRD-099 | Encode export dates using ISO-8601 | important | full | Phase 12.3 states saved shows export dates are encoded ISO-8601 |  |

### 3. Coverage Scores

```
Critical:  (30 x 1.0 + 0 x 0.5) / 30 x 100 = 100.00%  (30 of 30 critical requirements)
Important: (58 x 1.0 + 9 x 0.5) / 67 x 100 = 93.28%  (62.5 of 67 important requirements)
Detail:    (2 x 1.0 + 0 x 0.5) / 2 x 100 = 100.00%  (2 of 2 detail requirements)
Overall:   95.45% (99 total requirements)
```

### 4. Top Gaps

1. PRD-033 (`important`) - Sync libraries/settings consistently and merge duplicates
   The plan uses Supabase as the source of truth and timestamp merge logic for shows, but it does not define duplicate detection or settings conflict behavior. Without that, cross-device usage can produce stale settings, duplicate collection entries, or silent divergence that users experience as lost trust in their library.

2. PRD-091 (`important`) - Validate discovery with rubric and hard-fail integrity
   The plan resolves AI recommendations to catalog items, but it does not include the discovery quality rubric or golden-set validation loop. Without this, AI can technically return resolvable shows while still regressing on voice, taste alignment, surprise, or specificity.

3. PRD-051 (`important`) - Preserve Show Detail narrative section order
   The plan includes the right detail-page ingredients, but its ordering diverges from the supporting detail-page hierarchy. That matters because the spec is not just a component inventory; it defines the first-15-seconds reading path and emotional progression of the page.

4. PRD-066 (`important`) - Answer directly with confident, spoiler-safe recommendations
   The plan captures spoiler safety and confident persona, but it does not turn the "direct answer within first 3-5 lines" quality bar into a prompt, acceptance criterion, or test. Ask could become conversational yet still feel evasive or slow to answer.

5. PRD-076 (`important`) - Return bullet-only, 1-3 word, non-generic concepts
   The plan covers short, evocative, non-generic concepts, but it changes the visible/AI contract into JSON arrays without specifying bullet-only model output. That weakens parser predictability and risks concept responses that include explanations or extra formatting.

### 5. Coverage Narrative

#### Overall Posture

This is a strong implementation plan with no critical misses. It covers the mandated Next.js/Supabase architecture, namespace/user isolation, schema shape, save semantics, detail and discovery flows, AI contracts, settings, export, and major testing concerns. The remaining issues are not structural blockers, but they are meaningful because they sit around polish-sensitive product contracts: sync integrity, detail-page storytelling, and AI quality assurance.

#### Strength Clusters

The plan is strongest in Benchmark Runtime & Isolation, Collection Data & Persistence, App Navigation & Discover Shell, Collection Home & Search, Person Detail, and Settings & Export. The infra rider is especially well covered: environment config, server-only secrets, Supabase persistence, namespace isolation, `user_id`, reset scripts, migrations, and no-Docker compatibility all have concrete implementation details. Core collection behavior is also well represented, including auto-save defaults, removal semantics, overlay rules, AI recommendation resolution, and data export.

#### Weakness Clusters

The partial items cluster around quality contracts rather than raw feature presence. The plan often names the correct component or API route, but misses the stricter acceptance language behind a few requirements: timestamp-based sorting, duplicate merge policy, exact detail-page narrative order, Ask directness, concept output shape, surprise-but-defensible recommendation quality, and rubric-based AI validation. These are the kinds of gaps that show up in QA and product review after the app technically works.

#### Risk Assessment

If executed as-is, the most likely failure mode is a product that is functionally complete but uneven in trust and taste. Users would probably see the app save, search, chat, recommend, and export correctly, but stakeholders may notice that discovery quality is not systematically validated, Ask can be charming without answering fast enough, and the detail page may feel busier or less intentional than the spec's narrative hierarchy. Cross-device users may also expose sync and duplicate handling gaps before single-user benchmark testing does.

#### Remediation Guidance

The remaining work is mostly specification tightening. Add explicit timestamp sorting behavior, duplicate merge rules, and settings conflict resolution. Restore or deliberately justify the detail-page narrative order against the supporting spec. Convert the AI quality bar into prompt contracts, acceptance criteria, and tests/golden scenarios, including Ask directness, concept formatting, surprise-without-betrayal, and rubric hard-fail handling for real-show integrity. These changes do not require a new architecture; they require sharper behavioral acceptance criteria inside the existing plan.
