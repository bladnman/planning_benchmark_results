# Implementation Plan — Personal TV/Movie Companion

This plan turns `docs/prd/` into an executable build blueprint. It is organized so that each subsequent implementation step (scaffold → schema → catalog → library → detail → AI surfaces → discovery → polish) is a discrete, testable slice that preserves the product's "taste made visible" heart while meeting every rider constraint.

> **Scope discipline.** The rider requires Next.js + Supabase for this round, but the product PRD is technology-agnostic. The plan keeps all product rules behind service boundaries so a future provider swap touches only adapters, not feature code.

---

## 1. Guiding Principles (Non‑Negotiables)

These shape every downstream decision and are copied verbatim from the PRD/rider so the plan cannot drift.

1. **User's version always wins.** Wherever a show appears (lists, search, AI output, recommendations), if a user copy exists, render the overlay (status/interest/tags/rating/scoop). Merge conflicts resolve per‑field by newest `*UpdateDate`.
2. **Backend is source of truth.** Clearing client storage or reinstalling must never lose user data. Cache is disposable.
3. **Identity is explicit even in single‑user benchmarks.** Every user‑owned record is scoped by `(namespace_id, user_id)`. `user_id` is opaque/stable.
4. **Builds are isolated by namespace.** Destructive tests scope to a namespace; no global teardown is required.
5. **Discovery is actionable.** Every AI recommendation must map to a real, selectable Show or be clearly handed off to Search.
6. **Spoiler‑safe + on‑brand AI voice.** Fun, opinionated friend; vibe‑first, not encyclopedic; never drift outside TV/movies.
7. **Auto‑saves feel natural.** Rating → Done, Tag → Later+Interested, status chips → immediate save; removal always confirmed.
8. **Config‑only runtime.** Fill `.env`, run scripts. No source edits to boot.

A "definition of done" for every feature below is: these principles still hold after it ships.

---

## 2. Architecture Overview

### 2.1 Layered Decomposition

```
UI (Next.js App Router pages & components)
  │
  ▼  server actions / route handlers
Feature Services (collection, discovery, ai, catalog, person)
  │           │                │
  ▼           ▼                ▼
Catalog     AI           Persistence
Adapter   Adapter     (Supabase client: RLS‑scoped)
  │         │                │
  ▼         ▼                ▼
TMDB‑like Provider      Postgres + Storage
(external  (OpenAI‑      (namespaced rows)
 catalog)   compat API)
```

- **UI layer** is presentational; it calls server actions or `/api/*` route handlers for anything that needs secrets (AI provider, service‑role DB ops, catalog writes).
- **Feature services** are pure TS modules that express product rules (save triggers, defaults, merge policy, concept counts, etc.). They are the layer protected by unit tests.
- **Adapters** isolate the three replaceable edges: persistence (Supabase today), AI provider, catalog provider.
- **Identity** is injected at the edge of every server entrypoint: `(namespace_id, user_id)` derived from request headers/session and threaded into services.

### 2.2 Why this split

- Rider explicitly calls out swappable persistence/auth; the adapter seam protects the benchmark behavior from vendor churn.
- Feature services become the home of PRD rules (e.g. `saveShow`, `removeShow`, `resolveRecommendations`) so rebuild parity is measurable without reading UI code.
- Server‑only adapters keep secrets off the client (rider §3.1).

### 2.3 Runtime shape (Next.js specifics)

- Next.js **App Router** (latest stable).
- **Server Components** for library, detail, person, settings — they read Supabase directly with the anon or scoped key.
- **Client Components** for interactive bits: status/interest chips, rating bar, tag picker, Ask chat, Alchemy flow, Scoop streaming toggle, concept pickers.
- **Route handlers** (`app/api/...`) for: AI streaming (Scoop/Ask), concept generation, concept‑based recs, catalog search/detail proxy (to keep catalog key server‑side), namespace reset.
- **Server actions** for short write ops (save status, set tag, rate show, remove show, toggle settings).

---

## 3. Tech Stack & Tooling

| Concern | Choice | Rationale |
|---|---|---|
| Runtime | Next.js (latest stable) | Rider baseline. |
| Language | TypeScript (strict) | Safer refactors across adapters. |
| DB | Supabase Postgres | Rider baseline. Hosted preferred; local optional. No Docker assumed. |
| Auth in benchmark | Dev identity injection (`X-User-Id` header + dev user selector) | Rider §5.1; disabled in production mode. |
| AI provider | OpenAI‑compatible chat/completions + streaming | PRD/rider neutral; key via env. |
| Catalog provider | TMDB‑compatible adapter (IDs and image paths) | Widest real‑data coverage for a grounded benchmark. |
| Styling | Tailwind + small headless UI primitives | Fast, clean, avoids heavy vendor lock‑in. |
| Testing | Vitest (unit) + Playwright (e2e against a namespaced run) | Namespaced destructive tests required (rider §7). |
| Lint/format | ESLint + Prettier | Standard. |
| Package mgr | npm | Matches rider script examples; no Docker needed. |

### 3.1 Required scripts (package.json)

Minimum set meeting rider §3.2:

- `dev` → `next dev`
- `build` → `next build`
- `start` → `next start`
- `test` → `vitest run`
- `test:watch` → `vitest`
- `test:e2e` → `playwright test`
- `test:reset` → `tsx scripts/reset-namespace.ts` (deletes all rows where `namespace_id = $NAMESPACE_ID`)
- `db:migrate` → apply SQL migrations via Supabase CLI (or a thin psql runner if CLI unavailable)
- `db:seed` → idempotent namespaced seed for demo/golden‑set shows
- `typecheck` → `tsc --noEmit`

---

## 4. Environment & Configuration

### 4.1 `.env.example`

Committed (rider §3.1). Real `.env*` files go in `.gitignore` except `.env.example`.

```
# Supabase (required)
NEXT_PUBLIC_SUPABASE_URL=         # project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=    # browser/anon key (RLS‑safe)
SUPABASE_SERVICE_ROLE_KEY=        # server‑only; used by reset + migrations

# Build/run isolation (required)
NAMESPACE_ID=                     # stable per build, e.g. "bench-2026-04-16-alpha"

# Dev identity injection (benchmark mode)
DEV_DEFAULT_USER_ID=              # opaque stable string used when no X-User-Id header
DEV_IDENTITY_ENABLED=true         # MUST be false/absent in production mode

# External catalog (required for real discovery)
CATALOG_PROVIDER=tmdb
CATALOG_API_KEY=                  # server‑only (never ship to client)
CATALOG_IMAGE_BASE=https://image.tmdb.org/t/p

# AI provider (required for Scoop/Ask/Concepts)
AI_PROVIDER=openai
AI_API_KEY=                       # server‑only
AI_MODEL=gpt-4o-mini              # configurable per build
AI_BASE_URL=                      # optional for OpenAI‑compatible endpoints
```

### 4.2 Secret hygiene

- Service role + catalog + AI keys are **server‑only**; only exposed via route handlers / server actions.
- Browser code uses the `NEXT_PUBLIC_SUPABASE_ANON_KEY` with RLS enforcing per‑namespace/per‑user access.
- No secret is ever logged; client error telemetry strips keys.

### 4.3 Namespace + identity resolution

Central helper `lib/identity.ts` (server‑only):

```
resolveIdentity(request) → {
  namespaceId: env.NAMESPACE_ID,
  userId:
    env.DEV_IDENTITY_ENABLED
      ? (request.headers['x-user-id'] ?? env.DEV_DEFAULT_USER_ID)
      : getOAuthSubject(request)          // stub now; real later
}
```

All feature services require `{ namespaceId, userId }` in their signature so no server code can forget to scope a query.

---

## 5. Data Model (Supabase Postgres)

The schema mirrors `storage-schema.md` semantics but stores rows server‑side and replaces local‑only blobs with relational tables. All user‑owned tables have `(namespace_id, user_id)` in the PK or as indexed columns + RLS.

### 5.1 Tables

#### `catalog_show`
Public catalog cache (no namespace/user). Keyed by provider ID.
```
id              text PRIMARY KEY        -- "<provider>:<externalId>"
provider        text NOT NULL
external_id     text NOT NULL
show_type       text NOT NULL CHECK (show_type IN ('movie','tv','person','unknown'))
title           text NOT NULL
overview        text
tagline         text
genres          text[] NOT NULL DEFAULT '{}'
original_language text
spoken_languages text[] NOT NULL DEFAULT '{}'
languages        text[] NOT NULL DEFAULT '{}'
poster_url       text
backdrop_url     text
logo_url         text
vote_average     double precision
vote_count       integer
popularity       double precision
first_air_date   timestamptz
last_air_date    timestamptz
release_date     timestamptz
runtime          integer
budget           bigint
revenue          bigint
series_status    text
number_of_episodes integer
number_of_seasons  integer
episode_run_time  integer[] NOT NULL DEFAULT '{}'
homepage         text
provider_data    jsonb           -- streaming availability
details_update_date timestamptz NOT NULL DEFAULT now()
UNIQUE (provider, external_id)
```

#### `user_show`  (the overlay; one row per saved show per user per namespace)
```
namespace_id     text NOT NULL
user_id          text NOT NULL
show_id          text NOT NULL REFERENCES catalog_show(id) ON DELETE RESTRICT
my_tags          text[] NOT NULL DEFAULT '{}'
my_tags_update_date      timestamptz
my_score         double precision
my_score_update_date     timestamptz
my_status        text CHECK (my_status IN ('active','next','later','done','quit','wait'))
my_status_update_date    timestamptz
my_interest      text CHECK (my_interest IN ('excited','interested'))
my_interest_update_date  timestamptz
ai_scoop         text
ai_scoop_update_date     timestamptz
creation_date    timestamptz NOT NULL DEFAULT now()
is_test          boolean NOT NULL DEFAULT false
PRIMARY KEY (namespace_id, user_id, show_id)
```

Indexes:
- `(namespace_id, user_id, my_status)` — status sections on Home.
- GIN on `my_tags` — tag filter.
- `(namespace_id, user_id)` — full library fetches.

#### `user_settings`  (≈ `CloudSettings`)
```
namespace_id      text NOT NULL
user_id           text NOT NULL
user_name         text NOT NULL
ai_model          text NOT NULL DEFAULT 'gpt-4o-mini'
ai_api_key        text           -- optional user‑entered key; plaintext acceptable in benchmark
catalog_api_key   text           -- optional
version           double precision NOT NULL      -- conflict resolution epoch seconds
PRIMARY KEY (namespace_id, user_id)
```

#### `user_ui_state`
```
namespace_id                  text NOT NULL
user_id                       text NOT NULL
auto_search                   boolean NOT NULL DEFAULT false
font_size                     text NOT NULL DEFAULT 'M'
hide_status_removal_confirmation boolean NOT NULL DEFAULT false
status_removal_count          integer NOT NULL DEFAULT 0
last_selected_filter          jsonb     -- {type,label,value}
PRIMARY KEY (namespace_id, user_id)
```

#### `ai_scoop_cache`  (optional, unsaved‑show case)
Scoop persistence rule (PRD §5.7): **persists only when show is in collection**. For unsaved shows we may cache briefly, keyed by `(namespace_id, user_id, show_id)` with a short TTL. If we want to avoid it, we skip this table and only write Scoop into `user_show.ai_scoop` on saved shows — simpler, matches PRD intent.

Decision: **skip this table** in v1. Scoop for unsaved shows lives only in the current session (route handler stream + client cache). Saved shows persist `ai_scoop` + `ai_scoop_update_date` on `user_show`.

#### `app_metadata`
```
namespace_id        text PRIMARY KEY
data_model_version  integer NOT NULL
```

### 5.2 Row Level Security (RLS)

- Enable RLS on every `user_*` table.
- Policy shape (read/write):
  ```
  USING (namespace_id = current_setting('app.namespace_id', true)
         AND user_id  = current_setting('app.user_id', true))
  ```
- Server helper sets these GUC values per request via `set_config()` right after `resolveIdentity`.
- Service‑role key is used **only** by the reset script and migrations (bypasses RLS).

### 5.3 Migrations & seed

- SQL migrations in `supabase/migrations/*.sql` (numbered).
- `scripts/reset-namespace.ts` runs `DELETE FROM user_show/user_settings/user_ui_state WHERE namespace_id = $1` using the service role key (rider §7).
- `scripts/seed.ts` creates a default user (`DEV_DEFAULT_USER_ID`) row in `user_settings`/`user_ui_state` for the current namespace, and optionally inserts a handful of catalog shows for smoke tests.

### 5.4 Merge rules (centralized in a pure module)

`lib/merge.ts` implements:

- **Non‑my fields** → `selectFirstNonEmpty(newValue, oldValue)` (never overwrite non‑empty with empty/nil).
- **My fields** → newer `*UpdateDate` wins; if one side has no date, the side with a date wins.
- Used by: catalog refresh into `catalog_show`, and any future sync path that merges overlay edits.

Unit tests live alongside and enumerate both cases.

---

## 6. External Catalog Adapter

### 6.1 Contract

```ts
interface CatalogAdapter {
  searchShows(q: string, opts?: {mediaType?: 'movie'|'tv'|'all'}): Promise<CatalogShow[]>;
  getShow(id: string): Promise<CatalogShow>;              // full details incl. transient
  getSimilar(id: string): Promise<CatalogShow[]>;         // traditional recs strand
  getProviders(id: string): Promise<ProviderData>;
  getPerson(id: string): Promise<CatalogPerson>;
  getPersonCredits(id: string): Promise<CatalogCredit[]>;
  resolveByTitleAndMaybeId(title: string, externalId?: string, mediaType?: 'movie'|'tv'): Promise<CatalogShow | null>;
}
```

### 6.2 Transient vs persisted

Per schema doc: `cast`, `crew`, `seasons`, `images.*`, `videos`, `recommendations`, `similar` are **transient** — fetched on demand by the Detail/Person route handlers, attached to the render, never written to `catalog_show` except as core fields listed in §5.1.

### 6.3 Resolver used by AI recommendations

Implements PRD §5.8: "accepts the first result whose title matches case‑insensitively" when an external ID is present, else falls back to title search. Returned `catalogShow` is then upserted into `catalog_show` so subsequent saves are fast.

### 6.4 Image URL resolution

Adapter emits fully‑qualified poster/backdrop/logo URLs so neither DB nor UI stores partial paths. "Best logo" rule is deterministic: English preferred, then highest vote_average; implementation hidden in adapter.

---

## 7. AI Integration

### 7.1 Provider adapter

```ts
interface AIAdapter {
  chat(opts: { system: string; messages: ChatTurn[]; model?: string; stream?: boolean; responseFormat?: 'text'|'json' }): AsyncIterable<string> | Promise<string>;
}
```

Single adapter backs every surface (Scoop, Ask, Concepts, Concept‑based recs). Model is configurable per request; default from `AI_MODEL`.

### 7.2 Surface prompts (contracts, not literal text)

Each surface owns a module that builds system + user messages from PRD inputs. Prompts live in `lib/ai/prompts/<surface>.ts` so voice tuning is centralized.

#### Scoop  (`surfaces/scoop.ts`)
- Inputs: show (title, year, genres, vote avg, overview), user flag "wants spoilers? no".
- Output: long‑form text, streamed. Target 150–350 words with: personal take · honest stack‑up · "The Scoop" paragraph (centerpiece) · fit/warnings · verdict.
- Persistence: only if show is in collection → write `ai_scoop` + `ai_scoop_update_date` at stream end.
- Freshness: regenerate if `now - ai_scoop_update_date > 4h` on demand (button copy changes per PRD detail‑page spec).

#### Ask — general  (`surfaces/ask.ts`)
- Inputs: short rolling window of turns (summarize turns older than ~10), library digest (titles + status + interest + top tags), last user question.
- Output: **structured object** `{ commentary: string, showList: string }` where `showList` uses the strict format `Title::externalId::mediaType;;...` (PRD §3.2 contract).
- Parser: strict; on parse failure → **retry once** with stricter formatting directive; on second failure → fall back to unstructured `commentary` + Search handoff badge.
- UI renders `commentary` as chat text and `showList` parsed → a "mentioned shows" horizontal strip. Tapping resolves via catalog; fallback to Search.

#### Ask about this show  (`surfaces/ask.ts` variant)
- Same contract as Ask, but seeded with the current show's summary as the first assistant/context message. Entry from Detail must carry the show id.

#### Concepts — single show  (`surfaces/concepts.ts`)
- Inputs: one show. Output: **bulleted list only**, 8 concepts, 1–3 words each, evocative, diverse across axes, strongest first, spoiler‑free, no explanations. Reject outputs containing generic fillers ("good writing", "great characters") — validator re‑prompts once.

#### Concepts — multi show (Alchemy)  (`surfaces/concepts.ts`)
- Inputs: 2+ shows. Same output format, but a larger pool (≥12 suggested) so the UI can cap selection at 8. Requirement: concepts represent **shared commonality** across all inputs.

#### Concept‑based recs — Explore Similar  (`surfaces/concept_recs.ts`)
- Inputs: one show + selected concepts. Output JSON list of 5 items, each `{ title, externalId?, mediaType, reason }`. `reason` explicitly cites selected concepts.
- Every item resolved via catalog adapter before returning to UI.

#### Concept‑based recs — Alchemy  (`surfaces/concept_recs.ts`)
- Inputs: 2+ shows + selected concepts (≤8). Output 6 items, same shape. Chainable: a second round can use the returned shows as inputs.

### 7.3 Summarization

Ask and Alchemy chat contexts summarize turns older than ~10 into 1–2 sentences that preserve the persona/tone (PRD AI prompting §4). Summarization uses the same persona prompt to avoid sterile drift.

### 7.4 Voice guardrails

A small `voice.ts` validator inspects outputs for smells (encyclopedic tone, generic concepts, out‑of‑domain content) and can trigger a single re‑prompt. If the second attempt still fails, return the best of the two with a debug log.

### 7.5 Streaming

Scoop and Ask stream via `TransformStream`s from a route handler. UI shows "Generating…" state per Detail spec §3.4. Concept lists and rec lists are short enough to be non‑streaming.

---

## 8. Feature Breakdown

Each feature maps to PRD sections and has a concrete slice (UI + service + test).

### 8.1 App shell & navigation

- Persistent left sidebar (or drawer on mobile) with: **All Shows**, tag filters (one per tag in library + "No tags" if any tagless exist), data filters (genre, decade, community score), media‑type toggle (All/Movies/TV).
- Persistent entry points to **Find/Discover** (Search/Ask/Alchemy) and **Settings**.
- Route map:
  - `/` → Collection Home (respects last filter from `user_ui_state`).
  - `/show/[id]` → Show Detail.
  - `/person/[id]` → Person Detail.
  - `/find` → Discover hub with mode switch (`?mode=search|ask|alchemy`).
  - `/settings` → Settings & Your Data.

### 8.2 Collection Home (PRD §7.1)

- Server component fetches user library via a single SQL query ordered by status sections.
- Groups rendered top‑to‑bottom:
  1. **Active** — larger/prominent tiles.
  2. **Excited** (Later + Excited).
  3. **Interested** (Later + Interested).
  4. **Other** (collapsed) — Wait, Quit, Done, unclassified Later.
- Tiles show poster, title, in‑collection dot and rating indicator when `my_score` exists.
- Media‑type toggle + filter stack apply on top.
- Empty states: "No shows yet — try Search or Ask" and "No results found".
- Implements `lastSelectedFilter` persistence.

### 8.3 Show Detail (PRD §7.5, detail‑page spec)

Narrative order locked to the spec:
1. Header media (backdrop/poster/logo; inline trailer if available — but never block reading; graceful fallback).
2. Core facts (year, runtime or seasons/episodes) + community score.
3. **Toolbar** status/interest chips + My Rating bar (both in toolbar, not scroll body, per §3.3) + Tags picker.
4. Overview + Scoop toggle.
5. "Ask about this show" CTA (seeds Ask context).
6. Genres + languages.
7. Recommendations strand (traditional).
8. Explore Similar (Get Concepts → chip select → Explore Shows).
9. Streaming providers ("Stream It").
10. Cast + Crew strands (→ Person Detail).
11. Seasons (TV only).
12. Budget/Revenue (movies only, when available).

Auto‑save rules wired here (PRD §5.2–§5.3):
- Setting any status → save; defaults `Later + Interested` when no explicit status.
- Rating an unsaved show → save as **Done**.
- Adding a tag to an unsaved show → save as **Later + Interested**.
- Reselecting active status → confirmation dialog (with "don't ask again" that bumps `status_removal_count` and sets `hide_status_removal_confirmation`) → removal clears all My Data.

Scoop behavior:
- Button copy: "Give me the scoop!" (none) / "Show the scoop" (cached) / header "The Scoop" (open).
- Stream from `/api/ai/scoop?showId=...`; on completion, if saved, persist.
- Freshness: regenerate on demand if older than 4h.

Explore Similar:
- `/api/ai/concepts?showId=...` returns 8 concepts. UI chip select (cap ≤8).
- Changing selection clears downstream recs.
- `/api/ai/concept-recs?showId=...&concepts=...` returns 5 resolved recs.

### 8.4 Person Detail (PRD §7.6)

- Image gallery, name, bio.
- Filmography grouped by year.
- Analytics: average project ratings, top genres, projects‑by‑year (computed client‑side from credits — no persistence).
- Tapping a credit opens Show Detail; catalog adapter ensures the show is upserted into `catalog_show` when first opened.

### 8.5 Find — Search (PRD §7.2)

- Text input → debounced search via `/api/catalog/search` (server calls catalog adapter using the secret key).
- Results grid with in‑collection badges and rating badges (computed by joining against `user_show`).
- "Search on launch" setting redirects `/` → `/find?mode=search` at first navigation when enabled.

### 8.6 Find — Ask (PRD §7.3)

- Chat UI with user/assistant turns.
- Welcome view: 6 random prompts from an 80‑item starter list (seeded client‑side; "refresh" reshuffles).
- Turns streamed from `/api/ai/ask` which handles the structured output contract and summarization.
- Mentioned shows strip under the latest assistant message; session‑only.
- "Ask about this show" from Detail passes `?seedShowId=...`.

### 8.7 Find — Alchemy (PRD §7.4)

Wizard flow on one page with three cards:

1. **Pick shows** — search+add from library + catalog, min 2.
2. **Conceptualize Shows** → fetches concepts via `/api/ai/concepts` (multi‑show). Chips; select up to 8.
3. **Alchemize!** → `/api/ai/concept-recs` with 6 resolved recs; each has a reason.
4. **More Alchemy!** chains the returned recs as new inputs (clears prior concepts/results).

State is ephemeral (PRD §5.7); leaving the page clears it.

### 8.8 Settings & Your Data (PRD §7.7)

- Profile: username, font size, Search on launch toggle.
- AI: model picker + optional API key override (stored in `user_settings`; benchmark may also read from env).
- Integrations: catalog API key override (same).
- Data:
  - **Export My Data** → `.zip` containing a JSON backup of `user_show` + `user_settings` + `user_ui_state`, dates ISO‑8601. Generated server‑side at `/api/export`.
  - **Import/Restore** — left as a flagged open question per PRD §10; we scaffold the route but return 501 with a clear message so the UI can be added later without migration churn.

### 8.9 Tile/list indicators (PRD §5.9)

- In‑collection dot whenever `my_status` exists.
- Rating indicator whenever `my_score` exists.
- Both computed in a single join query when listing catalog results, so they appear on Search, traditional recommendations, Ask mentioned shows, and Alchemy results automatically.

---

## 9. Business Rules — Centralization Map

All of these live in `lib/collection/rules.ts` and are unit‑tested.

| Rule | Source | Implementation |
|---|---|---|
| Save triggers (status/interest/tag/rating) | PRD §5.2 | `saveShow(trigger, patch)` service entrypoint |
| Default status/interest | PRD §5.3 | `defaultsForTrigger(trigger)` pure function |
| Rating → Done on first save | PRD §5.3 exception | `defaultsForTrigger('rating')` returns `{status:'done'}` |
| Removal semantics | PRD §5.4 | `removeShow(showId)` clears all My fields and deletes the row; emits confirmation token usage |
| Re‑add preserves My Data | PRD §5.5 | `saveShow` upserts without clobbering existing My fields |
| Timestamps | PRD §5.6 | Every My‑field write sets its matching `*UpdateDate` |
| AI persistence table | PRD §5.7 | Scoop persisted only on saved shows; Ask/Alchemy session‑only |
| Recs → real shows | PRD §5.8 | `resolveRecommendations()` uses catalog adapter by ID then title |
| Merge policy | schema doc §merge | `mergeCatalogIntoStored()` with explicit timestamp resolution |
| Concept counts | concept_system §6 | Explore Similar 5 / Alchemy 6 |
| Concept quality | concept_system §4 | validator rejects generic stubs, re‑prompts once |

---

## 10. Identity, Isolation, Destructive Testing

### 10.1 Namespace threading

- Every service accepts `{ namespaceId, userId }`. No service has a default.
- RLS GUCs set per request (`app.namespace_id`, `app.user_id`) so even a bug that forgets to filter is caught by the DB.

### 10.2 Dev identity injection

- Route handlers honor `X-User-Id`; if missing or `DEV_IDENTITY_ENABLED=false`, use `DEV_DEFAULT_USER_ID`.
- Production mode (`DEV_IDENTITY_ENABLED=false`) requires a real auth subject — today stubbed to reject; later replaced with OAuth without schema changes.

### 10.3 Namespace reset

- `npm run test:reset` runs `scripts/reset-namespace.ts`: deletes all rows where `namespace_id = $NAMESPACE_ID` across `user_show`, `user_settings`, `user_ui_state`, `app_metadata`. Uses service role.
- Playwright e2e suite seeds the namespace before each run and resets after.
- No global teardown is ever required (rider §7).

### 10.4 Cloud‑agent compatibility

- All scripts pure Node/TS. Supabase CLI is optional (migrations fall back to psql‑over‑HTTPS if needed).
- Docker is never required (rider §8).

---

## 11. Continuity & Migrations (PRD §5.11)

- `app_metadata.data_model_version` tracks per‑namespace model version.
- Each SQL migration bumps the version in a single transaction so a partially‑applied migration can be re‑run safely.
- Import paths (future) will honor older schemas by mapping through `lib/migrations/legacy.ts`.

---

## 12. Testing Strategy

### 12.1 Unit (Vitest)

- **Rules:** every row of the table in §9 has tests.
- **Merge:** covers all four quadrants (new empty vs old value, etc.) and timestamp ties.
- **AI parsers:** `showList` parser fuzz tests (trailing separators, bad IDs, empty commentary).
- **Catalog resolver:** external‑ID hit, ID miss with title hit, total miss.

### 12.2 Integration (route handler level)

- Save triggers correctly auto‑save and set defaults.
- Removal clears My Data and deletes the row.
- Filter queries honor `(namespace_id, user_id)` scoping (RLS bypass attempt rejected).

### 12.3 End‑to‑end (Playwright)

Each test scopes to the run's namespace and resets before/after.
- **Rate‑to‑save:** search → open show → rating slider → show appears on Home under Done.
- **Tag‑to‑save:** new tag on unsaved show → Home shows it under Interested; sidebar gains the tag filter.
- **Status + removal:** Interested → Active → reselect Active → confirmation → show removed and My Data cleared.
- **Scoop:** generate → persists only when show is saved; shows "Show the scoop" on revisit; regenerates after TTL.
- **Ask mentioned shows:** fake AI response includes two items → strip renders two tiles → tapping opens Detail.
- **Alchemy chain:** 3 favorites → concepts → 8‑cap enforced → 6 recs → chain round clears concepts.
- **Person deep‑dive:** Detail → cast → Person → credit → Detail.
- **Export:** Settings → Export → zip contains JSON with ISO dates.

### 12.4 AI quality sampling

A `scripts/ai-eval.ts` takes a small golden set (Scoop on 5 shows, Concepts on 5 shows, Explore Similar on 5 shows, Alchemy on 3 sets) and prints outputs for human scoring against the `discovery_quality_bar.md` rubric (voice ≥1, taste ≥1, real‑show integrity =2, total ≥7). This is manual but deterministic to re‑run.

---

## 13. Repository Layout

```
app/
  layout.tsx
  page.tsx                              # Collection Home
  show/[id]/page.tsx                    # Show Detail
  person/[id]/page.tsx                  # Person Detail
  find/page.tsx                         # Discover hub w/ mode switch
  settings/page.tsx
  api/
    catalog/search/route.ts
    catalog/show/[id]/route.ts
    catalog/person/[id]/route.ts
    ai/scoop/route.ts                   # streaming
    ai/ask/route.ts                     # streaming; structured output
    ai/concepts/route.ts                # single + multi
    ai/concept-recs/route.ts
    export/route.ts
lib/
  identity.ts
  supabase/{server.ts, browser.ts, policies.sql}
  adapters/
    catalog/{index.ts, tmdb.ts}
    ai/{index.ts, openai.ts}
  collection/
    rules.ts                            # save/remove/defaults
    queries.ts                          # home/detail read paths
    merge.ts
  ai/
    surfaces/{scoop.ts, ask.ts, concepts.ts, concept_recs.ts}
    voice.ts                            # validators + re‑prompt
    summarize.ts
  ui/
    chips/*, tiles/*, pickers/*, streaming/*
scripts/
  reset-namespace.ts
  seed.ts
  ai-eval.ts
supabase/migrations/
  0001_init.sql
  0002_rls.sql
  ...
tests/
  unit/**/*.test.ts
  e2e/**/*.spec.ts
.env.example
package.json
README.md                               # configure → run → test loop
```

---

## 14. Build Phases (Execution Order)

Each phase ends in a runnable, testable state.

### Phase 0 — Scaffold & config
- Init Next.js TS project; add Tailwind, Vitest, Playwright, ESLint/Prettier.
- Commit `.env.example`, `.gitignore` excluding `.env*` (keep example).
- Add base scripts (`dev`, `build`, `test`, `test:reset`, `db:migrate`, `db:seed`, `typecheck`).
- README with a "fill env → run" quickstart.

### Phase 1 — Supabase + identity
- SQL migrations for tables in §5. RLS + policy SQL. `app_metadata` initial row per namespace.
- `lib/identity.ts`, middleware that sets GUCs on each server request.
- Seed + reset scripts. Verify reset via a manual row in `user_show`.

### Phase 2 — Catalog adapter + Show Detail (no AI yet)
- TMDB‑compatible adapter with cache‑through writes into `catalog_show`.
- Show Detail page with header media, core facts, overview, genres, streaming providers, recommendations strand, cast/crew strands, seasons (TV), budget/revenue (movies).
- Status/Interest chips, My Rating bar, Tags picker with auto‑save rules.
- Unit tests for rules; e2e for rate‑to‑save and tag‑to‑save.

### Phase 3 — Collection Home + filters
- Home groups (Active / Excited / Interested / Other), media toggle.
- Sidebar with tag filters, data filters, media toggle.
- Persist `lastSelectedFilter`; empty states.
- E2e: filter interactions, removal confirmation flow.

### Phase 4 — Find → Search
- Search route handler + page; debounce; in‑collection and rating badges on tiles; `auto_search` setting respected.

### Phase 5 — Person Detail
- Person adapter endpoints; credits grouped by year; charts (ratings/genres/by‑year) computed client‑side.

### Phase 6 — AI surface: Scoop
- AI adapter + Scoop prompt module; streaming route handler; UI toggle with the three states; persistence rule (saved shows only) + 4h freshness.

### Phase 7 — AI surface: Ask (general + "Ask about this show")
- Structured output contract + strict parser + one‑retry fallback; mentioned shows strip via catalog resolver; welcome prompts (6 from 80 starters, refresh).
- Summarization after ~10 turns.

### Phase 8 — AI: Concepts + Explore Similar
- Single‑show concept endpoint (8 concepts, validator).
- Explore Similar: chip select, 5 resolved recs, reason per rec.

### Phase 9 — AI: Alchemy
- Multi‑show concept endpoint (shared concepts), 6 resolved recs, chainable rounds.
- UI wizard.

### Phase 10 — Settings & Export
- Settings page with Cloud/Local/UI settings.
- Export to zip (JSON with ISO dates).
- Import returns 501 with a clear todo.

### Phase 11 — Polish + quality bar
- Voice validators pass across sample runs.
- Playwright e2e suite green.
- Manual AI quality sweep against the rubric.
- README updated with run/namespace/reset instructions.

---

## 15. Risk Register & Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| AI structured output drift | Mentioned shows strip breaks | Strict parser + one retry + unstructured fallback + Search handoff |
| Catalog API rate limits | Slow/failed searches and resolves | Adapter‑level caching (per request) + Postgres upsert of full shows; exponential backoff |
| RLS misconfiguration | Cross‑namespace data leak | RLS + GUC‑based policy + integration test that directly tries cross‑tenant reads |
| Scoop freshness edge cases | Regenerate loops | Pure `shouldRegenerate(ai_scoop_update_date, now)` unit‑tested with ±TTL cases |
| Secret leakage | Key exposed to browser | Server‑only adapters; lint rule forbidding direct secret access in client code |
| Image/logo flakiness | Ugly UI fallbacks | Deterministic "best logo" rule in adapter; poster fallback; `next/image` with graceful `onError` |
| Concept quality | Generic outputs break vibe | Validator + re‑prompt; e2e spot checks against rubric |
| Alchemy chaining state leak | Leftover recs/concepts | Clear ephemeral state on any input change; session‑only (PRD §5.7) |

---

## 16. Open Questions Parked (PRD §10)

These are **not** built in v1 but scaffolded or deferred cleanly:

- Next as first‑class status in UI — `my_status` already supports `'next'`; UI omitted.
- Named custom lists beyond tags — no schema changes planned; revisit with a `user_list` table.
- Scoop on unsaved shows implicitly saving — deliberately **not** auto‑saving; spec says persist only when in collection.
- Clearing rating → explicit unrated state — stored as `NULL` for now; flag for UX review.
- Import/Restore — route returns 501; UI hidden behind a feature flag.
- Saving/sharing Alchemy sessions — session‑only today; a future `alchemy_session` table could add this without schema conflict.
- Explicit `my_status` filters in sidebar — allowed by `FilterType`; not surfaced.

---

## 17. Success Criteria (Rider §9 + PRD)

A build passes when all of these are simultaneously true:

1. `.env.example` + scripts let a fresh checkout run without code edits.
2. All destructive tests scope to `NAMESPACE_ID` and `npm run test:reset` is idempotent.
3. Every user‑owned row is associated with `user_id` and RLS blocks cross‑tenant reads.
4. Swapping Supabase or AI or catalog requires only adapter replacement — no schema redesign.
5. Every PRD feature in §7 is reachable and passes its listed auto‑save/removal/merge rules.
6. AI surfaces pass the `discovery_quality_bar.md` thresholds (voice ≥1, taste ≥1, real‑show integrity =2, total ≥7).
7. Backend is authoritative: clearing browser storage does not lose user data.
8. Clearing client cache/reinstall never loses user data within a namespace.

---

## Appendix A — PRD Trace (coverage check)

Ensures every PRD section has an owning phase/module.

| PRD | Lands in |
|---|---|
| §1 Product Summary | Architecture §2, Principles §1 |
| §2 Goals | Testing §12 (success metrics informed) |
| §3 Non‑goals | Scope guardrails (no offline, no on‑device only) |
| §4.1 Show | `catalog_show` + `user_show` |
| §4.2 Status | Detail chips + rules §9 |
| §4.3 Interest | Same |
| §4.4 Tags | Detail picker + sidebar filters |
| §4.5 Filters | Home §8.2 + sidebar |
| §4.6 Ask | Phase 7 |
| §4.7 Alchemy | Phase 9 |
| §4.8 Explore Similar | Phase 8 |
| §4.9 Scoop | Phase 6 |
| §4.10 Person | Phase 5 |
| §5 Data behaviors | §9 rules module |
| §6 Navigation | §8.1 shell |
| §7 Major features | Phases 2–10 |
| §8 Cross‑cutting | §1 Principles |
| §9 User journeys | Playwright suites §12.3 |
| §10 Open questions | §16 |
| §11 Out of scope | Respected |
| Infra rider §2 baseline | Phase 0 + 1 |
| Infra rider §3 deliverables | Scripts + `.env.example` + migrations |
| Infra rider §4 identity | §10 |
| Infra rider §5 auth policy | §10.2 |
| Infra rider §6 data ownership | §2 (backend as source of truth) |
| Infra rider §7 destructive tests | §10.3 |
| Infra rider §8 cloud‑agent | §3 tooling |
| Infra rider §9 success | §17 |
| AI prompting + voice specs | §7 + voice.ts validators |
| Concept system spec | §7.2 Concepts + §8.7 Alchemy + §8.3 Explore Similar |
| Detail page spec | §8.3 |
| Discovery quality bar | §12.4 |
| Storage schema | §5 |

---

*End of plan. Implementation begins at Phase 0 in a follow‑up step.*
