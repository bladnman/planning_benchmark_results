# Implementation Plan — Personal TV & Movie Companion App

## Context

Build a personal TV/movie companion that lets users curate a library (statuses, interest, tags, ratings, AI Scoop) and drives taste-aware discovery through Search, Ask (AI chat), Alchemy (multi-show concept blending), Explore Similar, and Scoop. Source material is `docs/prd/product_prd.md`, `docs/prd/infra_rider_prd.md`, and the supporting specs under `docs/prd/supporting_docs/` (AI prompting, AI voice, concept system, discovery quality bar, detail page experience, storage schema).

The benchmark rider mandates **Next.js (latest stable) + Supabase**, no Docker requirement, configuration via `.env`, namespace isolation, dev-injectable identity, and a clean migration path to real OAuth later. Frontend must follow the fractal architecture in `INSTRUCTIONS.md` (Pages → Features → Sub-Features, humble components, theme tokens only, co-located hooks/utils, no `index.tsx`).

---

## 1. Tech Stack & Constraints

- **Runtime:** Next.js latest stable, App Router, TypeScript throughout.
- **Persistence:** Supabase (hosted preferred, local optional, Docker optional). Browser uses anon key; service-role key stays server-only.
- **External catalog:** TMDb-style provider via an adapter interface so future providers slot in without schema changes.
- **AI provider:** abstracted behind an adapter (OpenAI / Anthropic / etc.); model + key sourced from env or `cloud_settings` per PRD §7.
- **Styling:** Tailwind + CSS variables driven by `src/theme/` tokens. No hex codes, pixel values, or inline styles in TSX.
- **Identity:** dev mode injects `X-User-Id` header (or fixed default) gated to non-prod; prod path left as a seam ready for OAuth.
- **Isolation:** single `BUILD_NAMESPACE_ID` per process; all persisted tables partitioned by `(namespace_id, user_id)` with RLS enforcing the boundary.

---

## 2. Directory Layout

```
app/                                     # Next.js App Router
  layout.tsx
  (routes)/home/page.tsx
  (routes)/detail/[showId]/page.tsx
  (routes)/person/[personId]/page.tsx
  (routes)/find/page.tsx
  (routes)/settings/page.tsx
  api/
    my-shows/route.ts                    # CRUD for user overlay
    my-shows/[id]/route.ts
    catalog/search/route.ts
    catalog/show/[id]/route.ts
    catalog/person/[id]/route.ts
    ai/scoop/route.ts                    # streams
    ai/ask/route.ts                      # streams, emits structured showList
    ai/concepts/route.ts                 # single- or multi-show
    ai/recommend/route.ts                # explore-similar (5) / alchemy (6)
    export/route.ts                      # zip of JSON backup
    admin/reset-namespace/route.ts       # dev/test only

src/
  config/
    env.ts                               # zod-validated loader
    constants.ts                         # caps, timeouts, concept counts
  theme/
    tokens.ts                            # colors, spacing, radii
    fontScale.ts                         # XS..XXL
  components/                            # primitives: Chip, PosterTile, RatingBar, Strand, EmptyState
  hooks/                                 # useCurrentUser, useNamespace, useDebounced, useStreamingText
  utils/                                 # merge, timestamps, exporters, zip packer
  lib/
    supabase/
      browserClient.ts                   # anon key only
      serverClient.ts                    # service-role, server code only
    identity/
      resolve.ts                         # X-User-Id or DEV_DEFAULT_USER_ID → userId; namespace from env
      middleware.ts
    catalog/
      provider.ts                        # interface
      tmdbAdapter.ts                     # reference implementation
      mapping.ts                         # catalog → Show mapping + merge rules
    ai/
      provider.ts                        # askChat, scoop, concepts, recommend
      prompts/{ask,scoop,concepts,recommend}.ts
      summarizer.ts                      # older-turn summarization, voice-preserving
      showListParser.ts                  # Title::externalId::mediaType;;...
      resolver.ts                        # AI output → real catalog Show
  pages/
    Home/
      Home.tsx
      features/
        FilterSidebar/
        MediaTypeToggle/
        StatusGroupedGrid/
          features/
            ActiveGroup/
            ExcitedGroup/
            InterestedGroup/
            OtherGroup/
    Detail/
      Detail.tsx
      features/
        HeaderMedia/
        CoreFactsRow/
        MyRatingControl/
        MyStatusControl/                 # chips incl. Interested/Excited → Later mapping
        MyTagsControl/
        OverviewWithScoop/
          features/ScoopToggle/, ScoopStream/
        AskAboutShowCta/
        TraditionalRecsStrand/
        ExploreSimilar/
          features/GetConceptsCta/, ConceptChipPicker/, ExploreResults/
        ProvidersStrand/
        CastStrand/, CrewStrand/
        SeasonsStrand/                   # TV only
        BudgetRevenue/                   # movies when available
    Find/
      Find.tsx
      features/
        ModeSwitcher/
        Search/
        Ask/
          features/Welcome/, ChatTranscript/, MentionedShowsStrip/, Composer/
        Alchemy/
          features/InputShowsPicker/, ConceptualizeCta/, ConceptSelection/, AlchemizeCta/, AlchemyResults/
    Person/
      Person.tsx
      features/ImageGallery/, BioBlock/, AnalyticsCharts/, FilmographyGrouped/
    Settings/
      Settings.tsx
      features/AppSettings/, UserSettings/, AiSettings/, IntegrationsSettings/, DataExport/

supabase/
  migrations/000X_*.sql
  seed.sql

scripts/
  reset-namespace.ts
```

---

## 3. Data Model (Supabase)

One row per (namespace, user, show). All types and merge rules derived from `docs/prd/supporting_docs/technical_docs/storage-schema.md|.ts`.

- **`shows`** — identity (`id`, `namespace_id`, `user_id`, `title`, `show_type`, `external_ids` jsonb), catalog meta (overview, genres[], tagline, languages, images), ratings/popularity, dates (`last_air_date`, `first_air_date`, `release_date`), movie fields (`runtime`, `budget`, `revenue`), TV fields (`series_status`, `number_of_episodes`, `number_of_seasons`, `episode_run_time[]`), user overlay (`my_tags[]`, `my_score`, `my_status`, `my_interest`) each paired with `_update_date timestamptz`, `ai_scoop` + `ai_scoop_update_date`, management (`details_update_date`, `creation_date`, `is_test`), `provider_data jsonb`. PK `(namespace_id, user_id, id)`.
- **`cloud_settings`** — one row per `(namespace_id, user_id)`, fields per schema (`user_name`, `version`, `catalog_api_key?`, `ai_api_key?`, `ai_model`).
- **`app_metadata`** — `(namespace_id, data_model_version)`.
- **`local_settings`** / **`ui_state`** — key/value per `(namespace_id, user_id)` (auto_search, font_size, hide_status_removal_confirmation, status_removal_count, last_selected_filter jsonb).
- Dates are `timestamptz`; merge timestamps drive sync conflict resolution.
- **RLS:** every table enforces `namespace_id = current_setting('app.namespace_id')` AND `user_id = auth.uid()` or the dev-injected equivalent. All writes go through the server client so the service role can set the session variables safely.

### Merge policy (`lib/catalog/mapping.ts`)

- Non-my fields: `selectFirstNonEmpty(newValue, oldValue)` — never overwrite non-empty with empty, never overwrite non-nil with nil.
- My fields (`myTags`, `myScore`, `myStatus`, `myInterest`) resolve by newest update timestamp per field.
- `detailsUpdateDate = now` after merge. `creationDate` set only on first insert.
- `aiScoop` persisted only when the row exists (i.e., show is in collection); regenerated when older than 4h.

---

## 4. Identity & Isolation

- `BUILD_NAMESPACE_ID` required at startup — fail fast if missing. Loaded via `lib/config/env.ts`.
- Dev identity: middleware reads `X-User-Id` header; falls back to `DEV_DEFAULT_USER_ID` env. Gated by `NEXT_PUBLIC_ENV !== "production"`.
- Prod identity: seam ready for OAuth (NextAuth or Supabase auth). Replacing the middleware must NOT require schema changes — enforced by treating `user_id` as an opaque string.
- `namespace_id` is never exposed to the UI; it is a pure build-isolation primitive.
- Admin endpoint `/api/admin/reset-namespace` runs `DELETE ... WHERE namespace_id = $1` across all user-owned tables, gated to non-prod.

---

## 5. Business Rules (Collection)

Implicit save triggers (match PRD §5.2):
- Set any status → save with that status.
- Choose `Interested` or `Excited` chip → save as `Later + Interested|Excited`.
- Rate an unsaved show → save as `Done` (rating implies watched).
- Add a tag to an unsaved show → save as `Later + Interested`.

Removal:
- Reselecting the active status triggers a confirm dialog, then deletes the row and clears all my-data + AI Scoop.
- Suppressible after N confirmations via `hide_status_removal_confirmation` / `status_removal_count`.

Display rule:
- Anywhere a show surfaces (Home, Search, Mentioned Shows, Alchemy results, Explore Similar results, Recommendations strand), if a stored overlay exists, the UI renders the overlaid version (status, interest, tags, score, scoop). User edits always win over refreshed catalog data.

Home grouping order:
1. Active (prominent/larger tiles)
2. Excited (Later + Excited)
3. Interested (Later + Interested)
4. Other: Wait / Quit / Done + any Later without interest (collapsed group)

Tile badges: in-collection indicator (when `myStatus != null`), rating indicator (when `myScore != null`). Media-type toggle All / Movies / TV layered on top of any filter.

Filters (sidebar):
- All Shows (default).
- One per tag, plus `No tags` when any tagless shows exist.
- Data filters: genre, decade, community-score ranges.
- Persisted via `ui_state.last_selected_filter`.

---

## 6. AI Surfaces

Behavioral contracts from `ai_prompting_context.md`, `ai_voice_personality.md`, `concept_system.md`, `discovery_quality_bar.md`.

**Shared guardrails:** stay in TV/movies; spoiler-safe by default; opinionated and honest; specific over generic; actionable (real-show resolution); voice pillars (joy-forward, warm, vibe-first, concise unless earned).

**Scoop (Detail → The Scoop)**
- Structured mini-blog: personal take → honest stack-up vs reviews → The Scoop centerpiece → fit/warnings → verdict. Target 150–350 words.
- Streams progressively. Toggle copy per spec: `Give me the scoop!` → `Show the scoop` → `The Scoop` (open).
- Cache freshness 4h; regenerate on demand after expiry.
- Persist (`ai_scoop`, `ai_scoop_update_date`) only if the show is in collection.

**Ask (Find → Ask)**
- Chat UI; welcome view shows 6 random starter prompts drawn from a pool (~80 per AI voice spec), refreshable.
- Older turns summarized after ~10 messages, preserving persona voice via `summarizer.ts`.
- Mentioned shows output as a structured object:
  - `commentary` — user-facing text, no IDs.
  - `showList` — `Title::externalId::mediaType;;Title2::externalId::mediaType;;...` parsed by `showListParser.ts`.
- Mentioned shows row: tapping a resolved show opens Detail; unresolved titles hand off to Search.
- Parse failure: one retry with stricter formatting, then fallback to unstructured commentary + Search handoff.

**Ask About This Show**
- Entry from Detail seeds the conversation with the current show context and switches into Ask mode.

**Concepts (single-show and multi-show)**
- Bullet list only, 1–3 words each, no explanation, no plot, no spoilers.
- Single-show: 8 concepts by default. Multi-show (Alchemy): larger pool, concepts must be shared across all inputs.
- Quality rules: specificity ("hopeful absurdity" pass, "good characters" fail), diversity across structure/vibe/emotion/craft axes, strongest first.

**Explore Similar recommendations**
- Exactly **5** real shows per round. Each reason explicitly names which selected concepts align.

**Alchemy recommendations**
- Flow: pick 2+ starting shows → Conceptualize Shows → select up to 8 concepts → ALCHEMIZE! → review 6 recs → optional More Alchemy! chaining.
- Exactly **6** real shows per round. Recency-biased but classics/hidden gems allowed. Changing inputs clears concepts/results.

**AI → real show resolution** (`lib/ai/resolver.ts`):
- Look up by external ID if provided.
- Else accept the first external-catalog result whose title matches case-insensitively.
- Unresolved titles rendered non-interactively or handed off to Search.
- Resolved show objects may carry a transient `reason` string (not persisted).

**Persistence (per product_prd.md §5.7)**

| Surface | Persisted? | Notes |
|---|---|---|
| AI Scoop | Yes if in collection | 4h freshness |
| Alchemy results/reasons | No | Session only |
| Ask chat history | No | Session only |
| Mentioned shows strip | No | Session only |

Search must have **no AI voice**; it remains a plain catalog search.

---

## 7. Show Detail Narrative Order (from detail_page_experience.md)

1. Header media carousel (trailers when available, graceful fallback to poster/backdrop).
2. Core facts row (year, runtime or seasons/episodes) + community score.
3. Tag chips (My Tags).
4. Overview + Scoop toggle/stream.
5. "Ask about this show" CTA.
6. Genres + languages.
7. Traditional recommendations strand.
8. Explore Similar (Get Concepts → select → Explore Shows).
9. Providers ("Stream It").
10. Cast, Crew strands → Person Detail.
11. Seasons (TV only).
12. Budget vs Revenue (movies when available).

Toolbar holds Status/Interest chips and My Rating so the primary save actions stay above the fold (frictionless save + maintenance).

---

## 8. Person Detail

Gallery, bio, analytics charts (avg project rating, top genres, projects-by-year), filmography grouped by year. Tapping a credit opens that show's Detail page. Uses the same catalog adapter so Person can be reached from any Cast/Crew strand.

---

## 9. Settings

- **App:** font size (XS..XXL drives theme scale), `autoSearch` (open Search on launch).
- **User:** username (synced across devices when sync enabled).
- **AI:** model + API key (synced). In benchmark mode keys may come from env and never be committed.
- **Integrations:** catalog API key (synced).
- **Your data:**
  - Export/Backup → `/api/export` returns a `.zip` holding a single JSON of all shows + My Data + settings for `(namespace_id, user_id)`; ISO-8601 dates.
  - Import/Restore is **out of scope** for v1 (flagged as Open Question in PRD §10).

---

## 10. Schema Migrations & Data Continuity

- Migrations live in `supabase/migrations/`. `app_metadata.data_model_version` tracks the current version (default `3`).
- Upgrades MUST preserve all `my_*` fields, `ai_scoop`, tags, and ratings. Non-`my_*` fields are recoverable via catalog refresh. Transparent, no user intervention.
- `seed.sql` creates a default user row for the active namespace to keep bring-up to a single command.

---

## 11. Scripts & Env Interface (Infra Rider §3)

`.env.example`:
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
BUILD_NAMESPACE_ID=
DEV_DEFAULT_USER_ID=
CATALOG_API_KEY=
AI_API_KEY=
AI_MODEL=
```

`package.json` scripts:
- `dev` → `next dev`
- `build` / `start`
- `test` / `test:watch`
- `test:reset` → calls `/api/admin/reset-namespace` for the current namespace
- `db:migrate`, `db:seed`

No Docker requirement; Supabase connection is pure env configuration.

---

## 12. Testing Strategy

- **Unit:** merge rules (`selectFirstNonEmpty` + timestamp resolution), `showListParser`, AI resolver, saving-trigger rules, removal-confirm suppression logic, export serializer.
- **Integration:** API routes exercised against a real Supabase test project, scoped per namespace. `test:reset` runs between suites so there is no global teardown.
- **Component:** render smoke tests + RTL flows for each chip/toggle (status, interest, tags, rating); each feature in fractal tree has adjacent `*.test.tsx`.
- **AI contract tests:** mocked providers returning golden structured outputs; verify parser + stricter-retry + Search-handoff fallbacks.
- **Quality rubric** (`discovery_quality_bar.md`): automated checks for concept count (=8), each rec reason references a selected concept, real-show resolution rate = 100%. Manual rubric pass against a small sample set.
- Lint clean. Visual testing preferred where it protects a critical interaction.

---

## 13. Build Order (Incremental Delivery)

1. **Foundations** — Next.js scaffold, zod-validated env, theme tokens, Supabase migrations, identity middleware, RLS, `admin/reset-namespace`, dev seed. `.env.example` + scripts in place.
2. **Show + catalog adapter** — Storage shape, mapping/merge rules, `/api/catalog/*`, `/api/my-shows` CRUD.
3. **Home + Find/Search** — FilterSidebar, MediaTypeToggle, StatusGroupedGrid with badges, Search mode, Detail route skeleton.
4. **Detail primary controls** — Status/Interest chips (implicit save, Later mapping, removal confirm), MyRating (rate-to-save-Done), MyTags (tag-to-save-Later-Interested), Overview. Honors display rule everywhere.
5. **AI Scoop** — Provider interface, streaming endpoint, toggle copy states, 4h cache, persist only if in collection.
6. **Explore Similar** — Concepts endpoint (8 bullets), chip picker (capped at Alchemy's 8), Explore Shows (5 recs), resolver to real shows.
7. **Ask (chat)** — Welcome prompts (6 random from ~80), transcript, composer, streaming response, structured `showList` parsing, Mentioned Shows strip, older-turn summarization after ~10 turns, "Ask about this show" seed.
8. **Alchemy** — Input shows picker (library + global catalog), Conceptualize (shared concepts across inputs), concept cap 8, Alchemize (6 recs), chaining with More Alchemy!
9. **Person Detail** — Gallery, bio, analytics, grouped filmography.
10. **Settings & Export** — Font size (XS..XXL), autoSearch, username, AI/catalog keys + model, export zip with ISO-8601 JSON.
11. **Sync polish** — Verify per-field newest-wins conflict resolution across devices; clearing local cache never loses data; duplicates merged transparently.
12. **Hardening** — Empty states, error states, no-AI-voice in Search, accessibility passes, lint, tests green, discovery quality bar rubric check.

---

## 14. Verification

- **Config-only bring-up:** `cp .env.example .env && fill && npm install && npm run db:migrate && npm run db:seed && npm run dev`. App starts, namespace isolated, default user active.
- **Namespace isolation:** two processes with different `BUILD_NAMESPACE_ID` values see disjoint data; `npm run test:reset` only affects the current namespace.
- **Business-rule smoke:** scripted flow — search → rate unsaved (auto-save Done) → toggle Excited (→ Later+Excited) → tag an unsaved show (→ Later+Interested) → remove status (confirm warning) → verify overlay cleared.
- **AI contracts:** replay fixtures — mentioned shows parse, Scoop streams structured sections, concepts list is 8×(1–3 words) specific and diverse, Explore Similar returns 5 resolved shows with concept-citing reasons, Alchemy returns 6.
- **Sync/merge:** insert row, offline-mutate `myStatus` with a later timestamp on one device, older on another; verify latest timestamp wins per field.
- **Export roundtrip:** Settings → Export My Data → unzip → JSON validates against `storage-schema.md` shapes; dates ISO-8601.
- **Quality rubric:** manual pass against `discovery_quality_bar.md` — voice ≥1, taste alignment ≥1, real-show integrity =2, total ≥7/10.

---

## 15. Critical Files (To Read / Reuse)

- `docs/prd/product_prd.md` — canonical product rules.
- `docs/prd/infra_rider_prd.md` — Next.js + Supabase + namespace + no-Docker constraints.
- `docs/prd/supporting_docs/ai_prompting_context.md` — behavioral contracts + structured `showList` format + fallbacks.
- `docs/prd/supporting_docs/ai_voice_personality.md` — tone pillars, surface-specific adaptations, length targets, language patterns.
- `docs/prd/supporting_docs/concept_system.md` — concept taxonomy, generation rules, counts (5 Explore Similar, 6 Alchemy), quality heuristics.
- `docs/prd/supporting_docs/discovery_quality_bar.md` — quality rubric, per-surface minimum bars, non-negotiables (real-show integrity = 2).
- `docs/prd/supporting_docs/detail_page_experience.md` — Detail narrative hierarchy, toolbar controls, Scoop UX states.
- `docs/prd/supporting_docs/technical_docs/storage-schema.md` + `storage-schema.ts` — entity shapes, transient vs persisted, merge policy.
- `INSTRUCTIONS.md` — fractal architecture, humble components, theme discipline.

---

## 16. Assumptions, Deferrals, Open Questions

- AI provider and catalog provider are interchangeable behind adapters; no schema changes needed to swap.
- `Next` status stays in the data model but remains hidden from first-class UI (per PRD §4.2 "Optional/hidden").
- Generating Scoop on an unsaved show does NOT implicitly save it (lean conservative on PRD open question).
- Clearing My Rating stores `null` / absence (keeping parity with schema default).
- Import/Restore, named custom lists beyond tags, saveable Alchemy "blends", explicit `myStatus` sidebar filters — deferred per PRD §10.
- Real OAuth integration deferred; identity middleware is a seam ready for it.
