# Implementation Plan — Personal TV/Movie Companion (Benchmark Build)

This plan describes **how** we will build the product defined in `docs/prd/product_prd.md` under the constraints of `docs/prd/infra_rider_prd.md`, while honoring the voice, concept, detail, and quality specs in `docs/prd/supporting_docs/`.

It is a plan only — no source code is produced in this step.

---

## 0. Guiding Principles

1. **Backend is source of truth.** Clients cache for performance; correctness never depends on local storage (PRD §8, Infra §6).
2. **Identity + namespace are first-class on every row.** Every user-owned record carries `namespace_id` and `user_id` from day one, even in single-user benchmark mode (PRD §8, Infra §4).
3. **Dev identity today, OAuth later.** The auth boundary is a thin seam behind a `getCurrentUser(req)` abstraction; replacing it with Google OAuth must not require a schema change (Infra §5).
4. **User overlay always wins.** Every rendering of a show checks for an overlaid version and merges `my*` fields on top of catalog data (PRD §4.1, Storage merge rules).
5. **AI surfaces share one persona.** Prompts are centralized, surfaces differ by *mode* not *personality* (AI Voice §2).
6. **Recs must resolve to real shows.** `externalId + title` mapping is the non-negotiable integrity gate (PRD §5.8, Quality §1.5).
7. **Implicit saves must feel natural, not sneaky.** Rating → `Done`; tagging → `Later + Interested`; status chip → explicit status (PRD §5.2, §5.3).
8. **No Docker required.** The reference path uses hosted Supabase; local Supabase is optional (Infra §8).

---

## 1. Technology Stack

Mandated by the Infrastructure Rider:

- **Next.js (latest stable)** — App Router. Server Components for data-owning routes; Route Handlers (`app/api/**/route.ts`) for AI streaming and mutations; Client Components for interactive surfaces.
- **Supabase** — Postgres + Auth stub + Row-Level Security + Storage (only for the export ZIP, optional). Accessed via `@supabase/supabase-js` and `@supabase/ssr`.

Additional choices (justified, swappable):

- **TypeScript** throughout.
- **Tailwind CSS + shadcn/ui** for fast, coherent UI primitives. Fits "powerful but not overwhelming" (Detail §4).
- **React Query (TanStack Query)** on the client for cache/stale coordination on top of server-fetched data; keeps "cache is disposable" (Infra §6.2).
- **Zod** for schema validation at API boundaries and for AI structured outputs.
- **Vercel AI SDK** (`ai` package) for streaming chat + structured object generation. Default model: Anthropic Claude via API key from env; provider is pluggable.
- **External catalog**: TMDB (The Movie Database) REST v3. API key via env. All external fields in `product_prd.md` §4.1 map cleanly to TMDB.
- **Testing**: Vitest (unit), Playwright (E2E with namespace-scoped seed/reset). Tests hit real Supabase in a dedicated test namespace.
- **Schema management**: Supabase CLI migrations (`supabase/migrations/*.sql`). Deterministic, checked into repo.

---

## 2. Repository Layout

```
/
├─ .env.example                   # all required env vars (Infra §3.1)
├─ .env.local                     # gitignored
├─ .gitignore                     # excludes .env* except .env.example
├─ package.json                   # scripts: dev, build, test, test:e2e, test:reset, db:migrate, db:seed
├─ README.md                      # one-page quickstart (env, commands)
├─ next.config.ts
├─ tsconfig.json
├─ tailwind.config.ts
├─ supabase/
│  ├─ migrations/                 # timestamped .sql files (authoritative schema)
│  ├─ seed.sql                    # optional baseline seed
│  └─ functions/                  # (reserved; none required initially)
├─ src/
│  ├─ app/                        # Next.js App Router
│  │  ├─ layout.tsx               # root shell, theme, font-size
│  │  ├─ page.tsx                 # redirect → /collection
│  │  ├─ collection/              # Home (§7.1)
│  │  │  ├─ page.tsx
│  │  │  └─ [filter]/page.tsx     # tag, genre, decade, score, myStatus
│  │  ├─ show/[id]/page.tsx       # Detail (§7.5)
│  │  ├─ person/[id]/page.tsx     # Person Detail (§7.6)
│  │  ├─ find/
│  │  │  ├─ page.tsx              # mode switcher
│  │  │  ├─ search/page.tsx
│  │  │  ├─ ask/page.tsx
│  │  │  └─ alchemy/page.tsx
│  │  ├─ settings/page.tsx
│  │  └─ api/
│  │     ├─ shows/                # CRUD + overlay
│  │     ├─ my-data/              # status/interest/tags/rating mutations
│  │     ├─ catalog/              # TMDB proxy (search, details, person, recs, providers)
│  │     ├─ ai/
│  │     │  ├─ scoop/route.ts     # streaming
│  │     │  ├─ ask/route.ts       # streaming + mentions structured output
│  │     │  ├─ concepts/route.ts  # single + multi-show
│  │     │  └─ recommend/route.ts # concept-based recs
│  │     ├─ export/route.ts       # zip download
│  │     └─ admin/
│  │        └─ reset/route.ts     # namespace-scoped destructive reset
│  ├─ components/
│  │  ├─ layout/                  # Sidebar (filters), TopBar, MediaTypeToggle
│  │  ├─ show/                    # Tile, StatusChips, InterestChips, TagPicker, RatingBar, BadgeRow
│  │  ├─ detail/                  # HeaderMedia, FactsRow, ScoopToggle, ExploreSimilar, CastStrand, SeasonsList, BudgetRevenue, StreamingAvailability
│  │  ├─ discover/                # SearchGrid, AskChat, AlchemyBuilder, MentionedShowsStrip
│  │  ├─ person/                  # Gallery, FilmographyByYear, AnalyticsCharts
│  │  └─ ui/                      # shadcn primitives
│  ├─ lib/
│  │  ├─ supabase/
│  │  │  ├─ server.ts             # server client w/ namespace + user context
│  │  │  ├─ browser.ts            # anon client
│  │  │  └─ admin.ts              # service-role (server only; used only for reset)
│  │  ├─ identity/
│  │  │  ├─ namespace.ts          # resolve NAMESPACE_ID once at boot
│  │  │  └─ user.ts               # getCurrentUser(req) — dev injection today, OAuth seam
│  │  ├─ catalog/
│  │  │  ├─ tmdb.ts               # typed client
│  │  │  └─ map.ts                # TMDB → Show mapping
│  │  ├─ domain/
│  │  │  ├─ show.ts               # Show type, merge rules (selectFirstNonEmpty + timestamp)
│  │  │  ├─ status.ts             # auto-save / default rules
│  │  │  ├─ filters.ts            # filter predicates + grouping
│  │  │  └─ collection.ts         # grouping for Home (§7.1)
│  │  ├─ ai/
│  │  │  ├─ persona.ts            # shared persona preamble (Voice §2)
│  │  │  ├─ scoop.ts              # surface prompt + parser
│  │  │  ├─ ask.ts                # chat + mentions structured schema
│  │  │  ├─ concepts.ts           # single + multi prompt
│  │  │  ├─ recommend.ts          # concept → recs prompt + resolver
│  │  │  ├─ summarize.ts          # chat summarization (after ~10 turns)
│  │  │  └─ resolve.ts            # externalId + title → catalog Show
│  │  ├─ export/
│  │  │  └─ zip.ts                # JSON + ISO-8601 dates
│  │  └─ validators/              # zod schemas per API route
│  ├─ hooks/                      # useCollection, useShow, useAskSession, useAlchemy
│  └─ styles/
├─ scripts/
│  ├─ reset-namespace.ts          # wraps admin/reset; used by test:reset
│  ├─ seed-dev.ts                 # optional baseline shows for demo
│  └─ smoke.ts                    # post-deploy health check
└─ tests/
   ├─ unit/                       # merge rules, auto-save, filters, mapping
   ├─ contract/                   # Zod schemas for AI outputs, route handlers
   └─ e2e/                        # Playwright: journeys (§9)
```

---

## 3. Environment Variable Interface (`.env.example`)

All configuration via env — no code edits required to run (Infra §3.1).

```
# --- Supabase ---
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=        # browser-safe
SUPABASE_SERVICE_ROLE_KEY=            # SERVER ONLY; never exposed to client

# --- Build / run isolation ---
NAMESPACE_ID=                         # required; stable per build/run (Infra §4.1)
APP_MODE=development                  # development | production; gates dev identity

# --- Dev identity injection (Infra §5.1) ---
DEV_DEFAULT_USER_ID=00000000-0000-0000-0000-000000000001
DEV_ACCEPT_USER_HEADER=true           # accept X-User-Id in development only

# --- AI provider ---
AI_PROVIDER=anthropic                 # anthropic | openai
ANTHROPIC_API_KEY=
OPENAI_API_KEY=
AI_MODEL=claude-opus-4-5-20251001

# --- External catalog ---
TMDB_API_KEY=
TMDB_REGION=US                        # for provider availability

# --- Feature flags / tunables ---
AI_SCOOP_TTL_HOURS=4
CHAT_SUMMARIZE_AFTER=10
```

`.gitignore` excludes `.env*` except `.env.example`.

---

## 4. Data Model (Supabase / Postgres)

Every user-scoped table carries `namespace_id UUID` + `user_id UUID` with a composite index. Row-Level Security (RLS) enforces isolation so a bug in application code cannot leak across namespaces/users.

### 4.1 Tables

**`shows`** — one row per (namespace, user, show). This is the *user overlay*, not the global catalog.

```
id                 UUID PK default gen_random_uuid()
namespace_id       UUID NOT NULL
user_id            UUID NOT NULL
external_id        TEXT NOT NULL        -- TMDB id, string form
show_type          TEXT NOT NULL CHECK IN ('movie','tv','person','unknown')
title              TEXT NOT NULL

-- Catalog snapshot (cached; refreshed on detail view)
external_ids       JSONB                -- {imdb: "tt...", tmdb: "123"}
overview           TEXT
genres             TEXT[] NOT NULL DEFAULT '{}'
tagline            TEXT
homepage           TEXT
original_language  TEXT
spoken_languages   TEXT[] NOT NULL DEFAULT '{}'
languages          TEXT[] NOT NULL DEFAULT '{}'
poster_url         TEXT
backdrop_url       TEXT
logo_url           TEXT
network_logos      TEXT[] NOT NULL DEFAULT '{}'
vote_average       DOUBLE PRECISION
vote_count         INTEGER
popularity         DOUBLE PRECISION
last_air_date      TIMESTAMPTZ
first_air_date     TIMESTAMPTZ
release_date       TIMESTAMPTZ
runtime            INTEGER
budget             BIGINT
revenue            BIGINT
series_status      TEXT
number_of_episodes INTEGER
number_of_seasons  INTEGER
episode_run_time   INTEGER[] NOT NULL DEFAULT '{}'
last_episode_run_time INTEGER
provider_data      JSONB                -- {countries: {US: {flatrate:[...], ...}}}

-- User overlay ("my*")
my_tags            TEXT[] NOT NULL DEFAULT '{}'
my_tags_update_date        TIMESTAMPTZ
my_score           DOUBLE PRECISION
my_score_update_date       TIMESTAMPTZ
my_status          TEXT CHECK IN ('active','next','later','done','quit','wait')
my_status_update_date      TIMESTAMPTZ
my_interest        TEXT CHECK IN ('excited','interested')
my_interest_update_date    TIMESTAMPTZ

-- AI overlay
ai_scoop           TEXT
ai_scoop_update_date       TIMESTAMPTZ

-- Management
details_update_date        TIMESTAMPTZ
creation_date      TIMESTAMPTZ NOT NULL DEFAULT now()
is_test            BOOLEAN NOT NULL DEFAULT false

UNIQUE (namespace_id, user_id, external_id, show_type)
INDEX (namespace_id, user_id)
INDEX (namespace_id, user_id, my_status)
```

**`user_settings`** — maps to `CloudSettings` in the schema spec.

```
namespace_id       UUID NOT NULL
user_id            UUID NOT NULL
id                 TEXT NOT NULL DEFAULT 'globalSettings'
user_name          TEXT NOT NULL
catalog_api_key    TEXT           -- optional user-supplied override
ai_api_key         TEXT           -- optional user-supplied override (never required; env fallback)
ai_model           TEXT NOT NULL
version            DOUBLE PRECISION NOT NULL -- epoch seconds
PRIMARY KEY (namespace_id, user_id, id)
```

**`local_ui_state`** — server mirror of UI prefs (so device clears are safe, Infra §6.2).

```
namespace_id, user_id, auto_search, font_size,
hide_status_removal_confirmation, status_removal_count, last_selected_filter (JSONB)
PRIMARY KEY (namespace_id, user_id)
```

**`app_metadata`** — tracks `data_model_version` per namespace. Used by migrations/continuity (§5.11).

```
namespace_id UUID PK, data_model_version INT NOT NULL DEFAULT 3
```

**`namespaces`** (admin bookkeeping) — registry of known namespaces for reset tooling. Not required for user flows; handy for admin UX.

### 4.2 RLS Policy Shape

For each user-scoped table:

```sql
ALTER TABLE shows ENABLE ROW LEVEL SECURITY;

CREATE POLICY "tenant isolation" ON shows
  USING (namespace_id = current_setting('app.namespace_id')::uuid
      AND user_id     = current_setting('app.user_id')::uuid)
  WITH CHECK (namespace_id = current_setting('app.namespace_id')::uuid
      AND user_id     = current_setting('app.user_id')::uuid);
```

Every server request binds `app.namespace_id` and `app.user_id` via `SET LOCAL` at the start of the transaction. Service-role code paths (admin reset only) bypass RLS explicitly and still filter by `namespace_id`.

### 4.3 Why not two tables (catalog vs overlay)?

The storage schema spec intentionally models one `Show` per user that carries both catalog snapshot and overlay. We preserve that shape: it simplifies the "display user-overlaid version" rule (§4.1) and the merge policy (§5.5, §5.10). A catalog cache for *unsaved* shows lives purely in-memory / in Next.js fetch cache.

---

## 5. Identity, Auth, and Isolation

### 5.1 Namespace resolution
`NAMESPACE_ID` env var is read once at server boot (`lib/identity/namespace.ts`). Every server entry point calls `withTenant(req, handler)` which opens a Supabase transaction and applies `SET LOCAL app.namespace_id = $NAMESPACE_ID`.

### 5.2 User resolution (dev injection today, OAuth tomorrow)
`getCurrentUser(req)`:
1. If `APP_MODE !== production` and `DEV_ACCEPT_USER_HEADER=true` and request has `X-User-Id`, use it.
2. Else fall back to `DEV_DEFAULT_USER_ID`.
3. Future: replace this body with `supabase.auth.getUser()` — no schema change, no call-site change (Infra §5.2).

### 5.3 Multi-user in single-user mode
The UI exposes a single user, but every record is keyed by `(namespace_id, user_id)`. No UI surface stores "the user" implicitly; always flows through `getCurrentUser`.

### 5.4 Destructive testing
`POST /api/admin/reset` requires an internal shared secret (env `ADMIN_RESET_TOKEN`), is gated by `APP_MODE !== production`, and only deletes rows where `namespace_id = $NAMESPACE_ID`. This powers `npm run test:reset` (Infra §7).

---

## 6. External Catalog Layer (TMDB)

Single typed client in `lib/catalog/tmdb.ts` with the endpoints we need:

- `search/multi`, `movie/{id}`, `tv/{id}` (with `append_to_response=credits,images,videos,recommendations,similar,watch/providers,external_ids`)
- `person/{id}`, `person/{id}/combined_credits`, `person/{id}/images`
- `movie/{id}/watch/providers`, `tv/{id}/watch/providers`

`lib/catalog/map.ts` encapsulates the *external catalog → Show* mapping per the storage schema spec §"Field mapping rules":

- TMDB `media_type`/endpoint → `show_type`.
- Genre IDs → display names via TMDB genre lookups (cached).
- Best logo: prefer English, then highest vote-average (deterministic tiebreak by TMDB order).
- Parse dates through a list of accepted formats.
- Transient fields (`cast`, `crew`, `seasons`, `images.*`, `videos`, `recommendations`, `similar`) attached to the response object but **not persisted**.

Catalog refresh is triggered on Detail view load and merged into an existing overlay via the §7 merge rules below.

---

## 7. Domain Logic (Pure, Testable)

### 7.1 Merge rules (`lib/domain/show.ts`)

Per storage spec "Merge / overwrite policy":

- **Non-my fields**: `selectFirstNonEmpty(newValue, oldValue)` — never overwrite non-empty with empty/null.
- **My fields**: per-field timestamp comparison (`myTagsUpdateDate`, etc.) — newer wins; if only one side has a date, that side wins.
- `details_update_date = now()` after merge.
- `creation_date` is set once.

### 7.2 Auto-save & defaults (`lib/domain/status.ts`)

Encapsulates PRD §5.2–§5.3:

- Setting any status → save.
- Setting interest chip (Interested/Excited) → `my_status=later`, `my_interest=X`, save.
- Rating an unsaved show → save with `my_status=done`.
- Tagging an unsaved show → save with `my_status=later`, `my_interest=interested`.
- Clearing status → delete row (§5.4) after confirmation; suppression flag respected.

All update-date fields set to `now()` on the corresponding field change.

### 7.3 Filters & grouping (`lib/domain/filters.ts`, `collection.ts`)

- Available filters: All, per-tag, "No tags" (if any tagless shows exist), genre, decade, community-score band, media-type toggle (layered).
- Home grouping order (PRD §7.1):
  1. Active (prominent tiles)
  2. Later + Excited
  3. Later + Interested
  4. Other (Wait, Quit, Done, unclassified Later)
- Media-type toggle filters on top of any selected filter.

### 7.4 Tag library
Derived view: `SELECT DISTINCT unnest(my_tags) FROM shows WHERE ... ORDER BY tag`. Tag filters are lazy-derived from the user's collection.

---

## 8. AI Surfaces

### 8.1 Shared persona (`lib/ai/persona.ts`)

A single persona preamble captures AI Voice §2–§5: joy-forward, opinionated, vibe-first, spoiler-safe, specific, concise by default. Every surface prompt imports this preamble then adds mode-specific instructions. Search never invokes AI.

### 8.2 Surface contracts

Each surface in `lib/ai/*` exports:
- `buildPrompt(input)` — pure function; returns system + messages.
- `parse(response)` — Zod-validated parsing; retry-with-stricter-format on first failure (AI Prompting §5).
- `resolve(candidates)` — for surfaces that return show references.

| Surface           | Streams | Structured output                | Size target                        | Resolves to catalog? |
|-------------------|---------|----------------------------------|------------------------------------|---------------------|
| Scoop             | yes     | Sectioned markdown (parsed)      | 150–350 words                      | N/A                 |
| Ask (general)     | yes     | Free-form                        | 1–3 tight paragraphs               | N/A                 |
| Ask w/ Mentions   | yes     | `{commentary, showList}`         | Same as Ask + mentions strip       | yes                 |
| Concepts (single) | no      | Bullet list                      | 8 bullets, 1–3 words each          | N/A                 |
| Concepts (multi)  | no      | Bullet list (shared across inputs)| larger pool (capped at 8 selectable)| N/A                 |
| Recs (Explore Sim)| no      | `[{title, externalId, mediaType, reason}]` | 5 recs                  | yes (required)      |
| Recs (Alchemy)    | no      | same shape                        | 6 recs                             | yes (required)      |

### 8.3 Show reference resolution (`lib/ai/resolve.ts`)

Implements PRD §5.8:
1. If AI provides `externalId`, look it up in TMDB directly.
2. Verify title matches case-insensitively (accept first match).
3. On mismatch or missing ID, search TMDB and accept first case-insensitive title match with matching media type.
4. On failure, return non-interactive placeholder + "open in Search" affordance.

### 8.4 Chat context management (`lib/ai/summarize.ts`)

Ask sessions are ephemeral (§5.7). After `CHAT_SUMMARIZE_AFTER` (default 10) turns, older turns are summarized into 1–2 sentences using the same persona (AI Prompting §4). The summary replaces prior turns in context.

### 8.5 Scoop persistence (§5.7)

- Regenerate when `now - ai_scoop_update_date > AI_SCOOP_TTL_HOURS`.
- Persist to `shows.ai_scoop` **only if** the show is in the collection.
- Unsaved show: serve ephemerally (not written back) until/unless user saves.

### 8.6 Quality gate (Discovery Quality Bar)

- **Real-show integrity is non-negotiable**: every rec that passes through the UI has been resolved to a catalog row *before* being rendered as interactive. Unresolved recs render non-interactive.
- Concept validator rejects placeholder generics ("good characters", "funny", etc.) via a blocklist heuristic + length check.
- A lightweight post-parse validator flags concept counts outside 8 (single-show) as retry-worthy.

---

## 9. Feature Implementation Map

| Feature                      | Route(s)                                | Key components                                             | Notes                                                  |
|-----------------------------|-----------------------------------------|------------------------------------------------------------|--------------------------------------------------------|
| Collection Home §7.1        | `/collection`, `/collection/[filter]`   | Sidebar filters, MediaTypeToggle, StatusGroup, Tile        | Grouping order is fixed; filter layered on toggle.     |
| Search §7.2                 | `/find/search`                          | SearchGrid, Tile, in-collection badge                      | Auto-open on launch if `auto_search=true`.             |
| Ask §7.3                    | `/find/ask`                             | AskChat, MentionedShowsStrip, StarterPrompts               | Streaming; ephemeral session; 6 random starters.       |
| Ask about a show §7.3       | `/find/ask?seedShowId=...`              | AskChat with seeded system context                         | Seed from Detail CTA.                                  |
| Alchemy §7.4                | `/find/alchemy`                         | AlchemyBuilder (pick shows → concepts → results)           | Up to 8 concepts; 6 recs; chain via "More Alchemy!".   |
| Show Detail §7.5            | `/show/[id]`                            | HeaderMedia, FactsRow, StatusChips, InterestChips, TagPicker, RatingBar, ScoopToggle, ExploreSimilar, Recommendations, CastStrand, SeasonsList, BudgetRevenue, StreamingAvailability | All toolbar actions fire auto-save rules. |
| Explore Similar §4.8        | inline in `/show/[id]`                  | GetConcepts → ConceptChips (cap 8) → ExploreShows (5 recs) | Clears downstream on re-selection.                     |
| Person Detail §7.6          | `/person/[id]`                          | Gallery, Bio, AnalyticsCharts, FilmographyByYear           | Credit tap → `/show/[id]`.                             |
| Settings §7.7               | `/settings`                             | Forms for font size, auto-search, user name, API keys, AI model, Export | Font-size setting applies globally via CSS var. |
| Export §7.7                 | `GET /api/export`                       | server ZIP builder                                         | JSON + ISO-8601 dates; all user-owned rows + settings. |
| Import (open question)      | not implemented; surfaced in open Qs    | —                                                          | Listed under §10 open items.                           |

### 9.1 Tile indicator badges (§5.9)
Every `Tile` component computes in-collection (from overlay lookup) and user-rating badges from the shared hook `useShowOverlay(externalId)`. The lookup is a single batched query across the visible tile set.

### 9.2 Destructive confirmation (§5.4)
`StatusChips` consults `local_ui_state.hide_status_removal_confirmation`. Confirmation modal increments `status_removal_count`; after N (≥3) it offers "don't ask again".

---

## 10. API Surface (Route Handlers)

All handlers are `withTenant` wrappers + Zod validation + RLS-bound Supabase client.

**Shows / overlay**
- `GET  /api/shows` — list (supports filter & media-type query params; grouped server-side for Home).
- `GET  /api/shows/[externalId]` — single overlay + refreshed catalog merge.
- `PATCH /api/my-data/[externalId]` — partial update of `my_status | my_interest | my_tags | my_score`; applies auto-save rules and returns the merged row.
- `DELETE /api/my-data/[externalId]` — clears status + all my_* fields (collection removal per §5.4).

**Catalog proxy**
- `GET /api/catalog/search?q=`
- `GET /api/catalog/movie/[id]`, `GET /api/catalog/tv/[id]`
- `GET /api/catalog/person/[id]`
- `GET /api/catalog/providers/[id]?type=movie|tv`

These are thin server-side wrappers so the TMDB key never reaches the browser.

**AI**
- `POST /api/ai/scoop` — `{externalId, showType}` → streamed text; server decides persist vs ephemeral based on membership.
- `POST /api/ai/ask` — `{sessionMessages[], seedShowId?}` → streamed text; final chunk includes structured `showList`.
- `POST /api/ai/concepts` — `{shows: [{externalId, showType}], count?}` → `[string]`.
- `POST /api/ai/recommend` — `{shows, concepts, count}` → `[{title, externalId, mediaType, reason}]` post-resolution.

**Settings & export**
- `GET /api/settings`, `PUT /api/settings`
- `GET /api/local-ui-state`, `PUT /api/local-ui-state`
- `GET /api/export` → `application/zip`

**Admin**
- `POST /api/admin/reset` — header `X-Admin-Token` required; deletes all rows in namespace; refuses in production.

---

## 11. Migrations & Data Continuity (§5.11)

- `supabase/migrations/0001_init.sql` creates all tables, RLS, indexes, functions.
- `app_metadata.data_model_version` starts at 3 (matching the storage spec default).
- Every breaking migration is additive-then-swap: add new columns nullable, backfill, then drop old in a later migration — never destructive in one step.
- Each migration file documents rationale in a header comment.
- `npm run db:migrate` wraps `supabase db push`. CI runs migrations before tests on a scratch branch.

---

## 12. Scripts & One-Command DX (Infra §3.2)

```
npm run dev          # next dev
npm run build        # next build
npm run start        # next start
npm test             # vitest unit + contract
npm run test:e2e     # playwright
npm run test:reset   # node scripts/reset-namespace.ts
npm run db:migrate   # supabase migrations up
npm run db:seed      # optional dev seed
npm run export:smoke # fetch /api/export for the dev user, assert non-empty zip
```

A single `README.md` quickstart: "copy `.env.example` → `.env.local`, fill in values, `npm install`, `npm run db:migrate`, `npm run dev`."

---

## 13. Testing Strategy

### 13.1 Unit (Vitest, no DB)
- Merge rules: `selectFirstNonEmpty` matrix; my-field timestamp resolution matrix.
- Auto-save rules: all §5.2–§5.3 cases plus clearing.
- Filter predicates & grouping order.
- AI parsers: Scoop sections, `commentary/showList` split, concept blocklist, rec schema.

### 13.2 Contract (Vitest + msw)
- TMDB mapping → Show with fixture payloads for movie, TV, and ambiguous entries.
- AI structured output parsing with golden fixtures; retry-on-malformed path.

### 13.3 E2E (Playwright, real Supabase + dev identity)
Each test seeds/resets a namespace (via `test:reset`) and runs a PRD §9 journey:
1. Build collection: search → save Interested → verify Home grouping.
2. Rate-to-save: rate unsaved → assert `my_status=done`.
3. Tag-to-save: tag unsaved → assert `my_status=later, my_interest=interested`.
4. Maintain: update status from Detail → verify Home re-grouping.
5. Tag-driven org: add tag → assert sidebar gains tag filter.
6. Ask discovery: send prompt → mention resolves → save a rec.
7. Explore Similar: Get Concepts → Select → Explore Shows → save.
8. Alchemy: 3 shows → Conceptualize → Alchemize → chain.
9. Talent deep-dive: Detail → Person → credit → Detail.
10. Backup: `/api/export` returns ZIP with expected manifest.

### 13.4 Golden set hook (Quality Bar §3)
Tests log AI outputs with surface + scenario tags so the Golden Set can be populated incrementally without code changes.

### 13.5 Security / isolation
- RLS tests: with `user A` context, attempting to read `user B` rows returns zero.
- Reset confined to namespace: create data in namespace `n1` and `n2`, reset `n1`, assert `n2` intact.

---

## 14. Observability & Error Handling

- Structured logs on all API routes (`namespace_id`, `user_id`, route, duration, status).
- AI calls log provider, model, token usage (when available), parse success.
- User-facing errors degrade gracefully:
  - AI failure → banner + "try again" + Search handoff.
  - Catalog failure → retry once, then show cached overlay only.
  - Export failure → show error, don't partially write.

---

## 15. Performance Notes

- Home: grouped server-render + streaming tiles; overlay lookup is a single SELECT with composite index hit.
- Detail: parallelize `getOverlay` and `getCatalogDetails`; merge in the server component; stream Scoop once user taps the toggle.
- Ask: server-sent events stream; mentions resolved server-side as stream ends.
- Tile batches: `useShowOverlay` batches within a frame to avoid N+1.

---

## 16. Open Questions (from PRD §10, deferred)

These are deliberately NOT implemented in this round but the data model supports them:

1. Promote `Next` status to first-class UI.
2. Named custom lists beyond tags.
3. AI Scoop on unsaved show → implicit save?
4. Explicit "Unrated" vs nil.
5. Import/Restore from export ZIP.
6. Persisted Alchemy sessions ("saved blends").
7. Explicit `myStatus` filters in sidebar (the `FilterType` enum already includes it).

We will surface these as `// TODO(open-question-N)` anchors in code so they're discoverable.

---

## 17. Build Phases (Sequenced Execution)

**Phase 1 — Skeleton & infra (ship: env-driven empty app).**
- Repo scaffold, Next.js, Tailwind, shadcn, Supabase client wiring.
- Migrations 0001 (tables + RLS); namespace + dev-user plumbing; `withTenant`.
- `.env.example`, scripts, README.
- Admin reset route + `test:reset`.
- Smoke E2E: "app loads, empty collection renders".

**Phase 2 — Catalog + Collection core.**
- TMDB client + mapping.
- `shows` overlay CRUD with merge rules.
- Collection Home with grouping + media-type toggle + All Shows filter.
- Tile + badges.
- Search page + in-collection badge.

**Phase 3 — Show Detail + auto-save rules.**
- Detail page sections in PRD order.
- Status/Interest chips, Rating bar, Tag picker — all hitting auto-save logic.
- Confirmation flow for status removal + `hide_status_removal_confirmation`.
- Sidebar tag filters (derived).

**Phase 4 — AI surfaces.**
- Persona + streaming plumbing.
- Scoop (with TTL + saved/unsaved persistence rule).
- Ask (general) + mentioned-shows structured output + resolver.
- Ask about a show (seeded session).

**Phase 5 — Concept-driven discovery.**
- Concepts endpoint (single + multi).
- Explore Similar on Detail.
- Alchemy page (pick → concepts → recs → chain).
- Concept quality gates (blocklist, diversity heuristic).

**Phase 6 — Person, Settings, Export.**
- Person Detail + analytics charts + filmography-by-year.
- Settings page (font size, auto-search, username, AI model, API keys).
- Export ZIP endpoint.

**Phase 7 — Polish + quality bar.**
- Full journey E2E suite.
- Error states, empty states, loading skeletons.
- Accessibility pass (keyboard nav on chips, aria labels).
- Performance pass (batched overlay, streaming Scoop).

Each phase ends with: `npm run test:reset && npm test && npm run test:e2e` green.

---

## 18. Compliance Check vs Infra Rider §9

| Criterion                                                     | How this plan satisfies it                                |
|---------------------------------------------------------------|----------------------------------------------------------|
| `.env.example`, configurable without code edits               | §3                                                        |
| Repeatable runs without data collisions                       | `namespace_id` column + RLS + admin reset (§4, §5, §12)   |
| All user-owned records associated with `user_id`              | Every user-scoped table has `user_id NOT NULL` (§4)       |
| Destructive tests without global teardown                     | `test:reset` scoped to `NAMESPACE_ID` (§5.4, §12)         |
| Path to real OAuth without schema redesign                    | `getCurrentUser` seam (§5.2); `user_id` is opaque UUID    |
| No Docker required                                            | Hosted Supabase path; local optional                      |
| Client-only key is anon; service role server-only             | §3 env layout; `admin.ts` server-only                     |

---

## 19. Deliverable for This Step

This plan. No source code has been created. Implementation begins in Phase 1 under a separate step per `CLAUDE.md` workflow.
