# Implementation Plan — Personal TV + Movie Companion

> Derived from `docs/prd/product_prd.md`, `docs/prd/infra_rider_prd.md`, and every document under `docs/prd/supporting_docs/` (including `technical_docs/`). This plan is the sole deliverable of Step 1 — no source is written here.

---

## 0. Reading Guide

- **§1 Product Summary in one page** — what we're building and the six-legged stool it stands on.
- **§2 Architecture & Stack** — the cross-cutting decisions (Next.js + Supabase + fractal).
- **§3 Infrastructure, Identity, Isolation** — the rider PRD made concrete.
- **§4 Data Model** — Supabase schema, merge rules, migrations, continuity.
- **§5 Backend / Server Boundary** — route inventory, provider adapters, AI pipeline.
- **§6 Frontend Architecture** — fractal tree, page-by-page feature decomposition.
- **§7 Feature Specifications** — what each page/feature must do, broken down as tasks with acceptance tests.
- **§8 AI System** — voice, surface contracts, structured parsing, resolution, caching.
- **§9 Business Rules Matrix** — every save/remove/merge rule with its test obligation.
- **§10 Non-Functional Requirements** — accessibility, performance, telemetry, security.
- **§11 Testing Strategy** — unit, integration, E2E, AI quality gate, visual.
- **§12 Phasing / Milestones** — M0–M6 with entry/exit criteria.
- **§13 Risks, Open Questions, Out-of-Scope** — the explicit "not now" list and decisions we need before M2.
- **§14 Success Criteria** — how we know we're done against both PRDs.

---

## 1. Product Summary (One Page)

**What.** A personal library + discovery app for TV and movies. The user builds *their* version of every show — status (Active/Later/Wait/Done/Quit), interest level when Later (Interested/Excited), free-form tags, a personal rating, and an optional AI-generated "Scoop" review. The app then uses that taste profile to power four discovery paths: traditional search, conversational AI ("Ask"), concept-blending ("Alchemy"), and per-show "Explore Similar".

**Key invariants (non-negotiable).**
1. **User overlay always wins.** Anywhere a show appears, if the user has saved a version, render with `myStatus / myTags / myScore / aiScoop` overlaid on public metadata.
2. **Backend is source of truth.** Clearing a client cache must not lose user data.
3. **Recommendations must resolve to real catalog shows.** Hallucinated titles are a fail.
4. **Spoiler-safe by default.** Every AI surface assumes no spoilers unless explicitly asked.
5. **Identity is explicit.** Every user-owned record is scoped to `(namespace_id, user_id)`, even in single-user benchmark mode.
6. **Data continuity across updates.** Schema changes run forward migrations that never drop user data.

**Benchmark stack.** Next.js (App Router, latest stable) + Supabase (hosted preferred) + TypeScript + Tailwind (or equivalent token-first styling). No Docker required. Runs configured by environment variables — zero source edits.

---

## 2. Architecture & Stack

### 2.1 Top-level choices

| Concern | Choice | Why |
|---|---|---|
| Runtime | Next.js 15 (App Router) | Rider §2 mandates; gives us RSC for server-first data, Route Handlers for JSON APIs, and a clear UI/server boundary. |
| Persistence | Supabase (Postgres + Auth + RLS) | Rider §2 mandates; RLS is the easiest way to enforce `(namespace_id, user_id)` isolation. |
| Language | TypeScript strict | Single source-of-truth types across server + client. |
| Styling | Tailwind + CSS variables as design tokens in `src/theme/` | Token-first satisfies "no magic numbers, no inline styles" (INSTRUCTIONS.md). |
| Client data layer | TanStack Query (React Query) | Cache is disposable; user data still lives in Supabase. Query keys namespaced by `user_id`. |
| Session-only state | Zustand (one store per session feature: Ask, Alchemy, ExploreSimilar) | Matches PRD §5.7 — "Session only" AI data; cleared on leave. |
| Form/validation | Zod schemas shared server/client | Also used to validate AI structured output at the parser boundary. |
| AI provider | Pluggable adapter, default OpenAI/Anthropic configurable by env | Rider §1 non-goal pins nothing permanent. |
| Catalog provider | Pluggable adapter, default TMDB-style | Same reasoning; swap later without schema redesign. |
| Testing | Vitest (unit/integration) + Playwright (E2E) + a lightweight AI evaluation harness | Tight loop on logic, visual-ish confidence on critical flows, separate signal for AI quality. |

### 2.2 Fractal directory skeleton (from INSTRUCTIONS.md)

```
src/
├── config/                # env wiring, runtime constants, feature flags
├── theme/                 # tokens: color, type scale, spacing, radii, shadows
├── components/            # shared primitives (Button, Chip, Dialog, Poster, etc.)
├── hooks/                 # global hooks (useUser, useNamespace, useFontSize)
├── utils/                 # pure helpers (date parsing, merge, title normalization)
├── lib/
│   ├── supabase/          # server + browser clients, typed DB schema
│   ├── catalog/           # CatalogProvider adapter interface + tmdb impl
│   ├── ai/                # AIProvider adapter + prompt library + output parsers
│   └── auth/              # dev identity injection + future OAuth swap point
├── server/                # server-only modules (route handlers call into these)
│   ├── shows/             # saveShow, removeShow, mergeShow, listShows
│   ├── ai/                # buildScoop, chat, getConcepts, alchemize, exploreSimilar
│   ├── people/            # person lookup + filmography mapping
│   └── export/            # backup zip builder
├── app/                   # Next.js app router
│   ├── layout.tsx
│   ├── (main)/page.tsx            # Home (Collection)
│   ├── (main)/show/[id]/page.tsx  # Show Detail
│   ├── (main)/person/[id]/page.tsx
│   ├── (main)/find/
│   │   ├── page.tsx               # hub switcher (Search/Ask/Alchemy)
│   │   ├── search/page.tsx
│   │   ├── ask/page.tsx
│   │   └── alchemy/page.tsx
│   ├── (main)/settings/page.tsx
│   └── api/                        # Route Handlers (JSON API surface)
└── pages/                 # Page-level feature shells per fractal pattern
    ├── HomePage/
    ├── DetailPage/
    ├── PersonPage/
    ├── FindPage/ (children: SearchMode/, AskMode/, AlchemyMode/)
    └── SettingsPage/
```

**Humble-component rule.** Every `.tsx` contains JSX + prop binding. All logic in a sibling `useThing.ts` hook. Every feature owns its local `constants.ts` and `types.ts` as needed. No hex codes or pixel values in JSX — tokens only.

### 2.3 Module boundaries (the lines we do not cross)

- **UI never imports `lib/supabase` or `lib/ai` directly.** Those live behind Route Handlers or Server Actions invoked through typed client hooks.
- **Server code never imports React.** Keeps the RSC/client split clean.
- **Catalog and AI adapters are the only things allowed to touch network I/O outside `/api`.** This is our swap point for Rider §1's "Supabase today does not mean Supabase forever" guarantee.
- **All DB access includes `namespace_id` in the WHERE clause or fails a runtime assertion.** Enforced by a wrapper `db` helper in `lib/supabase/scoped.ts`.

---

## 3. Infrastructure, Identity & Isolation (Rider PRD made concrete)

### 3.1 Environment variable interface

`.env.example` (committed, no secrets):

```
# Public (browser-safe)
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_NAMESPACE_ID=          # stable per build/run
NEXT_PUBLIC_DEV_MODE=true          # gates dev identity injection

# Server-only (never exposed to client)
SUPABASE_SERVICE_ROLE_KEY=         # server routes + migrations only
AI_PROVIDER=openai|anthropic|mock
AI_API_KEY=
AI_MODEL=                          # provider-specific model id
CATALOG_PROVIDER=tmdb|mock
CATALOG_API_KEY=
DEFAULT_USER_ID=                   # optional; fallback when no header provided
```

`.gitignore` must exclude `.env`, `.env.local`, `.env.*.local`; keep `.env.example` tracked. A repo-root script `scripts/check-env.ts` runs on `dev` and `build` to fail fast if required keys are missing.

### 3.2 Namespace model (Rider §4.1)

- Every persisted row has `namespace_id text not null`.
- Server reads `NEXT_PUBLIC_NAMESPACE_ID` on boot; all queries pass it explicitly. There is no "global" data.
- RLS policy on every table: `namespace_id = current_setting('app.namespace_id')`, which we set via `set_config('app.namespace_id', $1, true)` at the start of each request.
- `npm run test:reset` invokes a SQL function `reset_namespace(ns text)` that deletes only that namespace's rows. Global teardown is forbidden (Rider §7).

### 3.3 Identity model (Rider §4.2, §5)

- `users` table keyed by opaque `id` (UUID). No provider semantics encoded.
- Dev-mode request pipeline:
  - Reads `X-User-Id` header if `NEXT_PUBLIC_DEV_MODE=true`.
  - Falls back to `DEFAULT_USER_ID` env, then to a "namespace default user" row created on first boot.
- Production swap (future): replace `lib/auth/devIdentity.ts` with Supabase Auth / OAuth; the `user_id` contract and schema do **not** change. This is the "configuration + wiring, not schema redesign" guarantee (Rider §5.2).
- All Route Handlers resolve `(namespace_id, user_id)` via a single `getRequestContext(req)` helper at the top of each handler. Bypassing it is a lint-checked anti-pattern.

### 3.4 Effective partition key

```
(namespace_id, user_id)
```

is the composite key for every user-owned row. Unique indexes and RLS both include it.

### 3.5 Scripts (package.json)

| Script | Purpose |
|---|---|
| `npm run dev` | Next.js dev server, loads `.env.local` |
| `npm run build` / `start` | Production build + run |
| `npm test` | Vitest (unit + integration) |
| `npm run test:e2e` | Playwright |
| `npm run test:reset` | Wipe the current namespace in Supabase (safe by definition) |
| `npm run db:migrate` | Apply SQL migrations in `supabase/migrations/` |
| `npm run db:seed` | Optional fixtures for dev/demo |
| `npm run ai:eval` | Run the Golden Set AI quality harness (§8.7) |

### 3.6 Docker is optional

The primary path is hosted Supabase. Local Supabase CLI is documented as optional in README. No tests may assume Docker.

---

## 4. Data Model & Persistence

### 4.1 Tables (Postgres, via Supabase migrations)

Field naming mirrors the technical reference in `storage-schema.ts` but stored snake_case per Postgres convention. All tables have `namespace_id text not null` + RLS.

**`users`** — opaque identity inside a namespace.
```
id uuid pk
namespace_id text not null
display_name text not null default ''
created_at timestamptz default now()
unique(namespace_id, id)
```

**`shows`** — canonical show + user overlay, one row per `(namespace, user, show_id)` when user has saved; transient catalog fetches do not hit this table.
```
namespace_id text not null
user_id uuid not null references users(id)
show_id text not null                    -- external catalog id, stable string
title text not null
show_type text not null                  -- 'movie'|'tv'|'person'|'unknown'
external_ids jsonb                       -- optional secondary ids
-- public metadata
overview text
genres text[] default '{}'
tagline text
homepage text
original_language text
spoken_languages text[] default '{}'
languages text[] default '{}'
poster_url text
backdrop_url text
logo_url text
network_logos text[] default '{}'
vote_average double precision
popularity double precision
vote_count integer
last_air_date timestamptz
first_air_date timestamptz
release_date timestamptz
runtime integer
budget bigint
revenue bigint
series_status text
number_of_episodes integer
number_of_seasons integer
episode_run_time integer[] default '{}'
last_episode_run_time integer
-- user overlay ("My Data")
my_tags text[] default '{}'
my_tags_update_date timestamptz
my_score double precision
my_score_update_date timestamptz
my_status text                           -- 'active'|'next'|'later'|'done'|'quit'|'wait'
my_status_update_date timestamptz
my_interest text                         -- 'excited'|'interested'
my_interest_update_date timestamptz
-- AI
ai_scoop text
ai_scoop_update_date timestamptz
-- management
details_update_date timestamptz
creation_date timestamptz default now()
is_test boolean default false
provider_data jsonb                      -- {countries: {US: {flatrate:[...], rent:[...], buy:[...]}}}
primary key (namespace_id, user_id, show_id)
```

**`cloud_settings`** — per-user synced settings (PRD §7.7, storage-schema §3.3).
```
namespace_id text not null
user_id uuid not null
id text default 'globalSettings'
user_name text
version double precision                -- epoch seconds for conflict resolution
catalog_api_key text                    -- nullable; env usually wins
ai_api_key text                         -- nullable; env usually wins
ai_model text
primary key (namespace_id, user_id, id)
```

**`app_metadata`** — per-namespace schema version for continuity (PRD §5.11).
```
namespace_id text primary key
data_model_version integer not null default 3
```

**`ui_state`** — per-user UI state (font size, autoSearch, hide status-removal confirmation, last selected filter). Kept out of `shows` so UI churn doesn't collide with collection writes.

**`ai_scoop_cache` (optional, unsaved shows only)** — see §8.5 on Scoop persistence: shows already in collection store Scoop on the `shows` row; for unsaved shows we may cache ephemerally server-side keyed by `(namespace, user, show_id, generated_at)` with a short TTL. Alternatively, unsaved-show Scoops are session-only and not persisted at all — we default to that and mark this table "open question" in §13.

### 4.2 Indexes

- `(namespace_id, user_id, my_status)` — Collection Home grouping/filter.
- `(namespace_id, user_id)` using `my_tags` GIN — tag filters.
- `(namespace_id, user_id, show_id)` — primary key already; direct lookups.
- `(namespace_id, user_id)` with `show_type` — Movies/TV toggle.
- `(namespace_id, user_id)` with `vote_average` / `first_air_date` — sort orders.
- Trigram index on `title` for client-side suggest fallback if needed.

### 4.3 Row-Level Security (RLS)

Enable RLS on every user-owned table. Policy pattern (applied to `shows`, `cloud_settings`, `ui_state`):

```sql
create policy shows_isolation on shows
  for all using (
    namespace_id = current_setting('app.namespace_id', true)
    and user_id::text = current_setting('app.user_id', true)
  );
```

`app_metadata` is namespace-scoped only:
```sql
create policy app_metadata_isolation on app_metadata
  for all using (namespace_id = current_setting('app.namespace_id', true));
```

Server-side helper `withRequestConfig()` runs `set_config('app.namespace_id', ..., true)` and `set_config('app.user_id', ..., true)` inside a transaction before any query. Anon-key client code (browser) cannot set these GUCs, so RLS stays safe even if keys leak.

### 4.4 Merge policy (storage-schema §"Merge / overwrite policy")

Implemented as a single server function `mergeShow(newCatalog, existing)`:

- **Non-my fields** use `selectFirstNonEmpty(newValue, oldValue)`:
  - Never overwrite a non-empty string/array with an empty one.
  - Never overwrite a non-null scalar with null.
- **My-fields** resolve by timestamp (PRD §5.5):
  - If both sides have update dates, keep the newer.
  - If only one has an update date, keep that side.
- `details_update_date = now()` after every merge.
- `creation_date` set only on first insert; refreshes do not touch it.

Test obligations: golden table tests for the full matrix (empty vs non-empty; older vs newer; null vs value; empty array vs populated).

### 4.5 Migrations & data continuity (PRD §5.11)

- All schema changes via numbered SQL files in `supabase/migrations/`.
- Every migration that changes `shows` must include a forward data-move step or prove the change is additive.
- `app_metadata.data_model_version` is bumped by each migration; a boot-time `assertDataModel()` refuses to run if the DB is behind the code's expected version.
- "Users never lose their collection" is an integration test: run migrations forward on a seeded DB, verify row count + `my_*` fields preserved per user.

### 4.6 External catalog mapping (storage-schema §"External catalog → Show mapping")

Implemented in `lib/catalog/mapCatalogShow.ts`:

1. Decode catalog payload → fresh `Show` object (transient, not yet persisted).
2. `showType`: `media_type` first, else infer (`name`→tv, `title`→movie, else `unknown` → reject).
3. `title`: catalog title (movie) or name (tv); missing → decode fails.
4. `genres`: map genre IDs → names (string array).
5. Dates: parse multiple accepted formats into ISO-8601.
6. Logo selection: deterministic rule (prefer English, then highest vote_average, then first). Documented in code comments as a single line.
7. Transient fields (`cast`, `crew`, `seasons`, `videos`, `images`, `recommendations`, `similar`) live on a separate `TransientShowExtras` type and are never persisted.

### 4.7 Export format (PRD §7.7)

`Export My Data` → ZIP containing:
- `shows.json` — every `shows` row for this user (ISO-8601 dates).
- `settings.json` — `cloud_settings` + `ui_state`.
- `metadata.json` — `{ exportedAt, dataModelVersion, namespaceId, userId }`.

Import/restore is flagged as optional extension (PRD §10) — not in M0–M4 plan; stub left for later with a documented target file format.

---

## 5. Backend / Server Boundary

### 5.1 Route Handler inventory (`src/app/api/...`)

All routes accept `X-User-Id` (dev mode) and resolve `(namespace_id, user_id)`.

**Catalog + show details**
- `GET /api/catalog/search?q=` — pass-through to CatalogProvider; marks in-collection items.
- `GET /api/catalog/show/:id` — details; merges with stored row if user has it; returns union.
- `GET /api/catalog/person/:id` — person profile + credits for Person Detail.

**Collection (my data)**
- `GET /api/shows` — list with filter+sort params (status group, tag, genre, decade, community-score range, media-type).
- `GET /api/shows/:id` — single (returns null if not saved + catalog detail separately).
- `POST /api/shows/:id` — upsert my-fields (`status`, `interest`, `tags`, `rating`). Applies save triggers + defaults (§9).
- `DELETE /api/shows/:id` — remove from collection; clears all my-fields + scoop per PRD §5.4.
- `POST /api/shows/:id/tags` — add/remove a single tag (reduces write amplification).
- `POST /api/shows/:id/rating` — set/clear rating with auto-save-as-Done rule.

**AI surfaces**
- `POST /api/ai/scoop/:showId` — generate or return cached Scoop; 4-hour freshness (§8.5).
- `POST /api/ai/ask` — chat turn; input `{messages, showContext?}`; output `{commentary, showList[]}` streamed when supported (§8.2).
- `POST /api/ai/concepts` — input `{showIds[], max}`; output `{concepts: string[]}` (§8.3).
- `POST /api/ai/explore-similar` — input `{showId, concepts[]}`; output `{recommendations: ResolvedShow[5]}`.
- `POST /api/ai/alchemy` — input `{showIds[], concepts[]}`; output `{recommendations: ResolvedShow[6]}`.

**Settings + export**
- `GET /api/settings` / `POST /api/settings` — cloud_settings.
- `GET /api/settings/ui` / `POST /api/settings/ui` — ui_state (font size, autoSearch, etc.).
- `GET /api/export` — streams a zip per §4.7.

**Admin / benchmark utilities**
- `POST /api/_test/reset` — namespace-scoped reset (dev only, behind `NEXT_PUBLIC_DEV_MODE`).

### 5.2 Catalog adapter interface

```ts
interface CatalogProvider {
  search(query: string): Promise<CatalogShowSummary[]>;
  getShow(id: string, type?: ShowType): Promise<CatalogShowDetail>;
  getPerson(id: string): Promise<CatalogPersonDetail>;
  resolveByTitle(title: string, externalId?: string, type?: ShowType): Promise<CatalogShowSummary | null>;
}
```

Default impl: `TmdbCatalogProvider`. Mock impl: `MockCatalogProvider` with deterministic fixtures for tests.

**`resolveByTitle`** is critical for AI rec mapping (PRD §5.8): look up by external id first; if no id or no hit, search by title and accept the first case-insensitive match. Returns null when no match — caller renders non-interactive or hands off to Search.

### 5.3 AI adapter interface

```ts
interface AIProvider {
  complete(input: AICompletionRequest): Promise<AICompletionResponse>;
  stream?(input: AICompletionRequest): AsyncIterable<AICompletionChunk>;
}
```

Prompts and output schemas live in `lib/ai/prompts/`:
- `scoopPrompt.ts`, `askPrompt.ts`, `conceptsPrompt.ts`, `exploreSimilarPrompt.ts`, `alchemyPrompt.ts`, `summarizeConversationPrompt.ts`.
- Each exports `{ system, userTemplate, outputSchema }`.
- Structured outputs validated with Zod. Parse failure → one retry with stricter formatting (per `ai_prompting_context.md §5`), then fallback to commentary + Search handoff.

### 5.4 Conversation summarization (PRD §4.6, ai_prompting_context §4)

- Client keeps the full in-session transcript in Zustand.
- Server decides when to summarize: if messages > 10, replace the older half with a single persona-consistent 1–2 sentence summary generated by `summarizeConversationPrompt`.
- Summary turn is labeled distinctly but styled in-voice so the feel of the thread is preserved.

### 5.5 Mentioned-shows parser (PRD §4.6, ai_prompting_context §3.2)

Format: `Title::externalId::mediaType;;Title2::externalId::mediaType;;...`

- Implemented as a strict split-and-validate parser.
- Every parsed entry runs through `CatalogProvider.resolveByTitle`.
- Resolved entries surface as interactive `ShowTile`s in the "Mentioned Shows" strip.
- Unresolved entries render non-interactive with a "Search for this" affordance.

### 5.6 Rate limiting, abuse, telemetry

- Basic per-user sliding-window rate limit on AI endpoints (configurable env: `AI_RATE_LIMIT_PER_MIN`). Prevents runaway prompts in dev.
- Structured logs (`pino`) on every AI call: user_id, surface, latency, tokens (when provider reports), parse success/failure, resolution rate.
- No PII in logs; user_id is opaque.

---

## 6. Frontend Architecture

### 6.1 Page-level shells (fractal pattern)

Each page lives in `src/pages/<PageName>/` and is rendered by the matching `app/.../page.tsx` thin file. Each page has a `use<PageName>Logic.ts` hook that orchestrates its features.

```
src/pages/HomePage/
  HomePage.tsx
  useHomePageLogic.ts
  constants.ts
  features/
    FiltersPanel/
    StatusGroupedList/      # Active / Excited / Interested / Other
      StatusGroupedList.tsx
      useStatusGroupedListLogic.ts
      features/
        StatusGroup/        # one section
        ShowTile/           # with in-collection + rating badges
    MediaTypeToggle/
    EmptyStates/
```

```
src/pages/DetailPage/
  DetailPage.tsx
  useDetailPageLogic.ts
  features/
    HeaderMedia/
    CoreFactsRow/
    MyStatusToolbar/        # chips; maps Interested/Excited → Later+Interest
    MyRating/
    MyTags/
    Overview/
    Scoop/                  # Give me the scoop! → Show the scoop → The Scoop
    AskAboutThisShow/
    RecommendationsStrand/
    ExploreSimilar/         # Get Concepts → select → Explore Shows
    StreamingAvailability/
    CastCrew/
    Seasons/                # TV only
    BudgetRevenue/          # movies only
```

```
src/pages/FindPage/
  FindPage.tsx
  useFindPageLogic.ts
  features/
    ModeSwitcher/           # Search | Ask | Alchemy
    SearchMode/
      features/ {SearchInput/, ResultsGrid/}
    AskMode/
      features/ {ChatWindow/, MentionedShowsStrip/, StarterPromptsBar/}
    AlchemyMode/
      features/ {StartingShows/, ConceptualizeStep/, ConceptSelection/, ResultsList/, ChainCta/}
```

```
src/pages/PersonPage/
  PersonPage.tsx
  features/ {ImageGallery/, Bio/, AnalyticsCharts/, FilmographyByYear/}
```

```
src/pages/SettingsPage/
  features/ {AppSettings/, UserSettings/, AISettings/, IntegrationsSettings/, YourData/}
```

### 6.2 Global primitives

`src/components/` holds only truly shared primitives: `Button`, `Chip`, `Dialog`, `Poster`, `Rating`, `Tag`, `Section`, `ScrollStrand`, `StreamingLogo`, `ShowTile` (if used in ≥2 places — otherwise lives in the feature that owns it).

`src/theme/` centralizes every design token. No inline styles. Tailwind config reads from tokens.

### 6.3 Client state

- **Server state**: TanStack Query; query keys start `['ns', namespaceId, 'user', userId, ...]`.
- **Session state** (explicitly cleared on leave, per PRD §5.7):
  - `useAskSession` — messages, mentioned shows, optional `seedShowId`.
  - `useAlchemySession` — starting shows, candidate concepts, selected concepts, last results.
  - `useExploreSimilarSession` (scoped per detail page) — concepts, selected, results.
- **UI state**: `useUiState` (font size, autoSearch, hideStatusRemovalConfirmation) persists via `/api/settings/ui`.

### 6.4 Navigation

- Persistent **Filters/navigation panel** (sidebar on desktop, drawer on mobile): All Shows, tag filters (one per tag, plus "No tags" if any tagless shows), data filters (genre, decade, community score ranges).
- Persistent **Find/Discover** and **Settings** entry points.
- **Media-type toggle** (All / Movies / TV) is a persistent top-bar control that composes with the active filter.
- Detail and Find are primary routes, not modals or popovers. The PRD explicitly rejects legacy panel mechanics.

### 6.5 Accessibility and responsive

- All interactive elements keyboard-reachable; focus visible; ARIA labels on chip groups, rating sliders, mode switcher.
- Font size setting maps to an accessible rem scale (XS..XXL) in the theme.
- Responsive from 375px to 1920px. Detail media carousel falls back gracefully with no backdrops/trailers (§7.5 of product PRD, detail_page_experience §5).
- Reduced-motion respected for carousel autoplay.

---

## 7. Feature Specifications

Each block lists **what it must do**, **acceptance criteria**, and **notable edge cases**. Tests listed here are the obligations — exact test names are at team's discretion.

### 7.1 Collection Home (PRD §7.1)

**Must do.**
- Show library grouped by status in this order: **Active** (prominent/larger tiles) → **Excited** (Later+Excited) → **Interested** (Later+Interested) → **Other** (collapsed: Wait, Quit, Done, uncategorized Later).
- Media-type toggle (All / Movies / TV) composes with the current filter.
- Tiles show poster, title, in-collection badge, rating badge when `myScore` present.
- Empty states: "No shows yet" (with CTA to Search/Ask); "No results found" when a filter yields none.

**Acceptance.**
- Adding a tag creates/keeps a sidebar tag filter (plus "No tags" when a tagless show exists).
- Removing last tag from a show removes the tag filter if no show uses it anymore.
- Groups render with correct counts and are collapsible.
- Server returns sorted payload; client doesn't re-sort critical order.

### 7.2 Search (PRD §7.2)

- Text search by title/keywords, poster grid results.
- In-collection badge appears on saved items (from a join against `shows`).
- Selecting a result → Detail.
- Setting "Search on Launch" in Settings causes app launch to land on `/find/search`.

### 7.3 Ask (PRD §7.3)

- Chat UI, user/assistant turns.
- 6 random **starter prompts** on welcome; refresh button rerolls. Starter pool lives in `lib/ai/starterPrompts.ts`.
- Streaming where provider supports it; progressive render ("Generating…" — detail_page_experience §3.4 applies to Scoop but same principle).
- Mentioned shows strip sourced from `showList` (§5.5). Tile tap → Detail; unresolved entries hand off to Search.
- Conversation summarization after ~10 messages (§5.4).
- **General Ask**: reached from Find.
- **Ask about a Show**: detail CTA opens `/find/ask?seedShowId=...`. On mount, seed the conversation with a persona-consistent opening turn referencing the show (no user message visible, but `seedShowContext` sent to server).
- Voice is **persona-consistent** (ai_voice_personality §4.2); spoiler-safe; opinionated.

### 7.4 Alchemy (PRD §7.4, concept_system §5)

- Step 1: pick ≥2 starting shows (library + catalog search).
- Step 2: "Conceptualize Shows" → AI returns concepts shared across all inputs.
- Step 3: user selects up to 8 concepts. Selecting/unselecting clears downstream recs.
- Step 4: "ALCHEMIZE!" → 6 recommendations, each with a 1–3 sentence reason that names which concept(s) it matches.
- Step 5: "More Alchemy!" uses current results as new starting shows (chain).
- Backtracking: changing starting shows clears concepts + results.

### 7.5 Show Detail Page (PRD §7.5, detail_page_experience §3)

Sections in this exact order (rebuild may not reorder silently):

1. Header media (backdrops/posters/logos/videos; trailer inline where available; graceful fallback).
2. Core facts row (year, runtime or seasons/episodes) + community score bar.
3. Tag chips (My Tags) + picker.
4. Overview text + Scoop toggle/stream.
5. "Ask about this show" CTA.
6. Genres + languages.
7. Recommendations strand.
8. Explore Similar (concepts → recs).
9. Streaming availability (Providers).
10. Cast, Crew strands → Person Detail.
11. Seasons (TV only).
12. Budget/Revenue (movies when available).

**Relationship controls in the toolbar** (not scroll body):
- Status chips: Active, Interested, Excited, Wait, Done, Quit.
- Interested/Excited chips set `status = Later` + `interest = Interested|Excited`.
- Reselecting an active status triggers removal confirmation (unless `hideStatusRemovalConfirmation=true`).
- Rating slider — rating unsaved show auto-saves as `Done` (§9).
- Tag picker — adding a tag to unsaved show auto-saves as `Later + Interested` (§9).

**Scoop toggle copy**:
- No scoop: "Give me the scoop!"
- Cached scoop: "Show the scoop"
- Open: titled "The Scoop"

### 7.6 Explore Similar (per-show concepts) (PRD §4.8, §7.5, concept_system §5)

- "Get Concepts" → 8 concepts (current generation count) rendered as chips.
- Select 1+ chips (selection cap aligned with Alchemy's cap of 8).
- "Explore Shows" → **5** recommendations (concept_system §6 — Explore Similar returns 5).
- Each reason explicitly references selected concept(s).

### 7.7 AI Scoop (PRD §4.9, detail_page_experience §3.4, ai_voice_personality §4.1)

- Spoiler-safe by default.
- Sections: personal take → honest stack-up vs reviews → The Scoop paragraph (centerpiece) → fit/warnings → verdict.
- Length target ~150–350 words.
- Streams when provider supports it (no blank wait).
- 4-hour freshness: after expiry, regenerates on demand.
- Persisted on `shows.ai_scoop` only when show is in collection; ephemeral otherwise.

### 7.8 Person Detail (PRD §4.10, §7.6)

- Image gallery, name, bio.
- Analytics charts: average project ratings, top genres, projects-by-year (simple bar/line charts; self-contained, no external dep beyond tiny SVG helper).
- Filmography grouped by year; tapping a credit opens that show's Detail.

### 7.9 Settings (PRD §7.7)

- **App**: font size (XS..XXL), Search on launch.
- **User**: username (synced if sync enabled).
- **AI**: AI provider API key (env wins in benchmark mode — never commit the user-entered key; synced if enabled), AI model selection.
- **Integrations**: catalog provider API key.
- **Your data**: Export My Data (§4.7). Import/restore listed as open extension.

### 7.10 Filters & views (PRD §4.5)

- Sidebar/menu structure:
  - All Shows (default quick view).
  - Tag filters: one per tag + "No tags" when applicable.
  - Data filters: genre, decade, community score ranges.
- Media-type toggle composes with any filter.
- `lastSelectedFilter` persists via ui_state so navigation feels sticky.

### 7.11 Tile indicators (PRD §5.9)

- In-collection badge when `my_status` exists.
- Rating badge when `my_score` exists.

---

## 8. AI System

### 8.1 Persona (ai_voice_personality)

Single persona across surfaces: the **fun, chatty TV/movie nerd friend**. Water-cooler gossip + critic brain + hype friend.

- **Joy-forward + warm**, even when critical.
- **Opinionated honesty** — mixed reception acknowledged; no gushing.
- **Vibe-first, spoiler-safe** unless user explicitly asks.
- **Specific** — concrete texture over genre boilerplate.
- **Short when needed, lush when earned**.
- Tone sliders as defaults: 70/30 friend/critic, 60/40 hype/measured, playful↔serious adaptive, concise↔lyrical concise by default.
- **Search has no AI voice.** Search surface is plain catalog search.

### 8.2 Surface contracts (ai_prompting_context §3, discovery_quality_bar §2)

| Surface | Output shape | Length | Structure |
|---|---|---|---|
| Scoop | Sections present (personal take, stack-up, Scoop paragraph, fit/warnings, verdict) | ~150–350 words | Streams progressively |
| Ask (no mentions) | Prose, bulleted list when recommending multiple titles | 1–3 tight paragraphs | n/a |
| Ask (with Mentions) | `{commentary, showList: "Title::externalId::mediaType;;..."}` | as Ask | Zod-validated; strict parser |
| Concepts | Bullet list only | 1–3 words each; 8 by default | Diverse axes, no plot |
| Explore Similar recs | `{recommendations: [{showId, title, reason}]}` | 5 items | Reasons name matched concepts |
| Alchemy recs | Same shape | 6 items | Reasons name matched concepts |

Guardrails:
- Redirect back into TV/movies if user strays.
- Never recommend a hallucinated title — if catalog resolution fails, surface as non-interactive or Search handoff.
- If structured parsing fails once, retry with stricter formatting; on second failure, fall back to unstructured commentary and Search handoff.

### 8.3 Concept generation rules (concept_system §4)

- Multi-show: concepts must be shared across all inputs.
- Quality: specificity over genericity ("hopeful absurdity" ✓; "good characters" ✗).
- Diversity: cover structure / tone / emotion / relationship / craft / flavor.
- Order by strength: best "aha" concepts first.
- Default count 8, selection cap 8.

### 8.4 AI recommendation resolution (PRD §5.8)

Single code path `resolveAIRecommendation(title, externalId, mediaType)`:
1. If `externalId`, look up by id directly.
2. Else search by title, accept first case-insensitive match whose `showType` matches `mediaType`.
3. Return resolved `Show` summary or `null`.

The transient "reason" is passed alongside the resolved show object and lives with session state only.

### 8.5 Scoop caching (PRD §5.7, §4.9)

- If show is in collection: Scoop persists on `shows.ai_scoop` with `ai_scoop_update_date`. Regenerate on demand when >4 hours old.
- If show is not in collection: return-only, ephemeral. Do not persist.
- Regeneration is always explicit (user presses toggle) — no silent refresh.

### 8.6 Session-only vs persisted AI data (PRD §5.7)

| AI data | Persisted | Scope |
|---|---|---|
| AI Scoop | ✓ if in collection | per show |
| Ask chat history | ✗ | per Ask session |
| Ask mentioned shows | ✗ | derived from current session |
| Alchemy results/reasons | ✗ | per Alchemy session |
| Explore Similar results | ✗ | per Detail visit |

On leaving or resetting each session, state is cleared.

### 8.7 Golden Set / AI quality gate (discovery_quality_bar)

- Maintain `tests/ai/golden/` with ~10 scenarios per surface covering: on-brand Scoop, concept diversity, Alchemy reasoning, Ask mentions parsing.
- Scoring rubric (per scenario): Voice ≥1, Taste alignment ≥1, Real-show integrity =2 required, total ≥7/10.
- `npm run ai:eval` runs the harness against the configured AI provider and prints pass/fail by dimension. Used as a gate in CI for AI-touching PRs.
- `MockCatalogProvider` + `MockAIProvider` allow golden-set-style tests to run deterministically in unit tests (no network), using recorded fixtures.

---

## 9. Business Rules Matrix (PRD §5)

Every rule below has a corresponding unit or integration test.

| Rule | Trigger | Effect | Test |
|---|---|---|---|
| **Save (status)** (§5.2) | Select any status chip | Upsert show with that status; `my_status_update_date = now()` | ✓ |
| **Save (interest)** (§5.2) | Select Interested/Excited chip | Status = Later + interest = chip value | ✓ |
| **Save (rating on unsaved)** (§5.2, §5.3) | Adjust rating on unsaved show | Status defaults to **Done**; rating stored | ✓ |
| **Save (tag on unsaved)** (§5.2, §5.3) | Add first tag on unsaved show | Status defaults to **Later + Interested** | ✓ |
| **Default save** (§5.3) | Save without explicit status | status=Later, interest=Interested | ✓ |
| **Remove from collection** (§5.4) | Reselect active status + confirm | Delete row; clear all my-fields + scoop; show warning dialog | ✓ |
| **Suppress removal confirm** (§5.4) | User checks "don't ask again" | `ui_state.hide_status_removal_confirmation=true` + `status_removal_count_key` tracked | ✓ |
| **Re-add same show** (§5.5) | Encounter a show already saved | Preserve my-fields + scoop; refresh public metadata; merge | ✓ |
| **Merge conflict** (§5.5, §5.6) | Two edits across devices | Newer timestamp wins per-field | ✓ |
| **Timestamps** (§5.6) | Any my-field edit | Update matching `*_update_date` | ✓ |
| **Scoop freshness** (§4.9, §5.7) | Open Scoop when `ai_scoop_update_date` > 4h old | Regenerate on demand | ✓ |
| **Scoop persistence** (§5.7) | Generate Scoop on unsaved show | Return ephemeral; do not persist | ✓ |
| **AI rec resolution** (§5.8) | AI returns `title + externalId + mediaType` | Resolve via catalog; first case-insensitive title match if needed; unresolved → non-interactive/search | ✓ |
| **Tile indicators** (§5.9) | `my_status` or `my_score` present | Show badges in all tile contexts | ✓ |
| **Sync conflict** (§5.10) | Two devices change same field | Most recent timestamp wins | ✓ |
| **Data continuity** (§5.11) | Migration bumps data_model_version | User library preserved through forward migration | ✓ |

---

## 10. Non-Functional Requirements

### 10.1 Performance

- Home list first meaningful paint < 1.5s on warm cache; < 3s cold (hosted Supabase in same region).
- Detail page hydrates public metadata + my-overlay in a single round trip via a server-rendered shell.
- AI streaming starts rendering within 800ms of request for supported providers.

### 10.2 Accessibility

- WCAG 2.1 AA target: contrast, focus, labels, keyboard.
- Font size setting affects all readable surfaces (including chat bubbles and Scoop body).
- `prefers-reduced-motion` respected.

### 10.3 Security

- Service role key never exposed to client. Lint rule forbids importing it from `app/` client components.
- API keys (AI, catalog) server-side by default; user-entered keys stored only in `cloud_settings` (per-user, scoped) if user opts in — never committed.
- RLS enforced on every user-owned table.
- CSRF protection on POST/DELETE routes (Next.js built-in + origin check).

### 10.4 Observability

- Structured server logs with `namespace_id`, `user_id`, `route`, `latency_ms`, `ai_tokens_in/out` where available.
- Minimal client error reporting (optional, env-gated) — must be namespace-aware.

### 10.5 Internationalization readiness

- All copy in a single `copy.ts` per feature (or a global messages module). No hardcoded strings in JSX beyond trivial labels.
- Dates rendered via locale-aware formatter; stored ISO-8601.

---

## 11. Testing Strategy

### 11.1 Unit tests (Vitest)

- `lib/catalog/mapCatalogShow.test.ts` — every mapping edge case.
- `server/shows/mergeShow.test.ts` — full merge matrix (§4.4).
- `server/shows/saveRules.test.ts` — §9 rule matrix, one test per row.
- `lib/ai/parse*` — structured output parsers; happy path, malformed, partial.
- `lib/ai/resolveAIRecommendation.test.ts` — id hit, title match, no match.
- `utils/timestamps.test.ts` — newer-wins selection.

### 11.2 Integration tests

- Route handler tests with a seeded Supabase schema and `MockCatalogProvider` / `MockAIProvider`.
- RLS smoke: request under namespace A cannot read data under namespace B.
- Data continuity: seed → migrate forward → assert row counts + my-fields.
- `test:reset` scoped to namespace.

### 11.3 E2E (Playwright)

Golden flows from PRD §9:
1. Build collection (Search → Detail → Interested/Excited).
2. Rate-to-save.
3. Tag-to-save.
4. Maintain collection (Home → update from Detail).
5. Tag-driven organization (tag filters appear).
6. Ask discovery → save a rec.
7. Explore Similar → save a rec.
8. Alchemy round → chain.
9. Talent deep-dive (Detail → Person → Detail).
10. Backup (Export My Data).

### 11.4 AI quality gate

- `npm run ai:eval` as specified in §8.7. Required on AI-touching PRs.
- Non-AI PRs bypass the gate via a label.

### 11.5 Visual tests

- Snapshot critical surfaces (Home status sections, Detail header + toolbar, Scoop streamed state, Alchemy result row).
- Visual tests are highly preferred per INSTRUCTIONS.md — enforced for pages, optional for leaves.

---

## 12. Phasing / Milestones

### M0 — Scaffolding (1 sprint)

**Exit:** `npm run dev` serves an empty app that reads env, shows namespace + user banner in dev mode, and runs an empty test suite green.

- Next.js app init, TypeScript strict, Tailwind + theme tokens.
- `.env.example`, `.gitignore`, env-check script.
- `lib/supabase` clients + `getRequestContext`.
- `lib/auth/devIdentity` (`X-User-Id` + default-user fallback).
- `CatalogProvider` + `AIProvider` interfaces with `Mock*` impls.
- Empty pages registered.
- CI runs lint + unit tests.

### M1 — Data backbone (1 sprint)

**Exit:** a user in namespace A can create/read/update/delete shows via the API; RLS verified; `test:reset` works.

- Supabase migrations for `users`, `shows`, `cloud_settings`, `ui_state`, `app_metadata`.
- RLS policies + `withRequestConfig()`.
- `server/shows/*` CRUD + save rules + merge.
- Namespace reset function.
- Unit + integration tests for §9 matrix and §4.4 merge.

### M2 — Browse, Search, Detail shell (1–2 sprints)

**Exit:** Home shows grouped collection, Search works, Detail renders with public metadata and my-overlay, status/rating/tag edits persist through the server, removal confirmation dialog works.

- Home + filters sidebar + media-type toggle.
- Search page (catalog pass-through).
- Detail sections 1–4, 6 (header, facts, tags, overview, genres).
- Toolbar status chips + rating + tags with save triggers.
- E2E: build-collection, rate-to-save, tag-to-save.

### M3 — AI surfaces (2 sprints)

**Exit:** Ask, Scoop, Explore Similar, Alchemy all work end-to-end against a real AI provider; golden-set eval passes the quality bar.

- Scoop (§7.7) streamed; 4-hour cache per show.
- Ask (§7.3) — general + "Ask about this show"; mentioned-shows parser + resolver; conversation summarization.
- Concepts + Explore Similar (§7.6) on Detail.
- Alchemy (§7.4) mode + chaining.
- `ai:eval` harness + golden set seeded.

### M4 — Discovery polish + Person Detail + Settings (1 sprint)

**Exit:** Person Detail, Settings (App/User/AI/Integrations/YourData with Export), recommendations strand, streaming availability all render.

- Detail sections 7–12 completed.
- Person page.
- Settings pages.
- Export My Data zip.

### M5 — Cross-cutting polish (1 sprint)

**Exit:** a11y audit passes, performance budgets met, visual tests in place for top surfaces, docs current.

- Accessibility audit + fixes.
- Performance passes; streaming start budget honored.
- Visual snapshot coverage for golden flows.
- README + runbook for benchmark operator.

### M6 — Optional (open questions, §13)

- Next status promoted to first-class (if decided).
- Named custom lists.
- Import/restore.
- Alchemy session saving.
- Explicit myStatus filter group in sidebar.

---

## 13. Risks, Open Questions, Out of Scope

### 13.1 Open questions (from PRD §10, plus implementation decisions we need)

1. Should **Next** become a first-class status in UI? Schema supports it; UI does not surface it yet.
2. Should users create named **custom lists** beyond tags?
3. Should generating **AI Scoop** on an unsaved show implicitly save it? We default to *no* (Scoop is ephemeral for unsaved).
4. Should clearing My Rating store an explicit **Unrated** state or nil? We default to nil + show "unrated" affordance.
5. **Import/Restore** from export zip — UI missing; target format decided but deferred to M6.
6. Saveable/shareable **Alchemy sessions** — deferred.
7. Explicit **myStatus filters** in sidebar — supported by schema; deferred to M6.
8. **Scoop for unsaved shows — server-side ephemeral cache vs pure-session?** We default to pure-session to simplify data lifecycle; revisit if UX suffers.
9. **Starter prompt catalog** — what exactly are the 6 rotating prompts drawn from? We'll ship a tuned list of ~30 and rotate 6 randomly; evolve.
10. **Conversation summarization trigger** — PRD says "~10 messages." We'll pick 10 as the exact threshold unless user testing shows otherwise.

### 13.2 Risks

| Risk | Impact | Mitigation |
|---|---|---|
| AI hallucinates titles faster than catalog can resolve | Broken recommendations | Structured output + strict resolver; one retry; fallback to Search handoff |
| Supabase RLS misconfiguration leaks data across namespaces | Data breach | Integration tests explicitly assert cross-namespace reads fail; CI gate |
| Client cache drift vs server source of truth | User sees stale My Data | TanStack Query invalidation on every mutation; never read-through client storage for correctness |
| Conversation context grows unbounded, tokens explode | Cost + latency | Summarize older half after 10 messages; hard cap on context size |
| Catalog provider outage | Most pages degrade | Graceful fallbacks on Detail (render what we have); retry with backoff |
| Prompt drift breaks voice | Brand damage | Golden set (§8.7) is the canary; voice pillars in `ai_voice_personality.md` are the spec |
| Secret leaks (service role, AI key) | Cost + data risk | `.gitignore`, env-check script, lint rule on server-only imports |

### 13.3 Out of scope (explicit)

- Offline-first mode beyond graceful network failure.
- Production OAuth (designed for, not implemented this round).
- Social/community features.
- Vendor-specific optimizations.
- UI micro-interaction/animation prescriptions.
- Detailed caching strategy beyond 4-hour Scoop TTL.

---

## 14. Success Criteria

### 14.1 Functional (Product PRD)

- All 10 key user journeys (PRD §9) pass E2E.
- All business rules in §9 of this plan pass unit/integration tests.
- All AI surfaces meet the quality bar (voice, taste, specificity, real-show integrity) per `discovery_quality_bar.md`.
- User's version always wins on render (PRD cross-cutting rule §1).
- Every recommendation maps to a selectable real show (PRD cross-cutting rule §2).

### 14.2 Infrastructure (Rider PRD §9)

- `.env.example` present; repo configurable without code edits.
- Two namespaces run side-by-side without collisions.
- Every user-owned record scoped to `user_id`.
- Destructive test runs scoped to the namespace; no global teardown.
- Dev identity can be swapped for real OAuth with configuration + auth wiring only — **schema unchanged**.

### 14.3 Engineering

- Fractal architecture honored — no page has logic outside its tree.
- Humble components — TSX files contain markup + binding only.
- No magic numbers / inline styles.
- Lint-clean build.
- Unit tests for critical logic adjacent to source.
- Visual tests on golden surfaces.

---

## Appendix A — Requirements traceability sketch

This plan maps each major PRD section to a plan section so the evaluator can check coverage quickly.

| PRD source | Plan section(s) |
|---|---|
| product_prd §1–§3 Summary/Goals/Non-goals | §1, §13.3 |
| product_prd §4 Core Concepts & Objects | §4, §7 |
| product_prd §5 Data Behaviors & Rules | §4.4, §9 |
| product_prd §6 App Structure & Navigation | §6.1, §6.4 |
| product_prd §7.1 Collection Home | §7.1 |
| product_prd §7.2 Search | §7.2 |
| product_prd §7.3 Ask | §7.3, §8.2 |
| product_prd §7.4 Alchemy | §7.4, §8.2 |
| product_prd §7.5 Show Detail | §7.5 |
| product_prd §7.6 Person Detail | §7.8 |
| product_prd §7.7 Settings & Your Data | §7.9, §4.7 |
| product_prd §8 Cross-cutting Rules | §2.3, §3, §10.3 |
| product_prd §9 Key User Journeys | §11.3 |
| product_prd §10 Open Questions | §13.1 |
| infra_rider §2 Baseline | §2.1 |
| infra_rider §3 Repo Deliverables | §3.1, §3.5, §4.5 |
| infra_rider §4 Identity & Isolation | §3.2, §3.3, §3.4, §4.3 |
| infra_rider §5 Auth Policy | §3.3 |
| infra_rider §6 Data Ownership | §2.3, §6.3 |
| infra_rider §7 Destructive Testing | §3.2, §3.5, §4.1 |
| infra_rider §8 Cloud Agent Compatibility | §3.6 |
| infra_rider §9 Success Criteria | §14.2 |
| ai_prompting_context | §5.3, §8.2, §8.4 |
| ai_voice_personality | §8.1, §8.2 |
| concept_system | §7.4, §7.6, §8.3 |
| detail_page_experience | §6.1, §7.5 |
| discovery_quality_bar | §8.7, §11.4 |
| storage-schema(.md/.ts) | §4.1–§4.7 |

---

**End of Plan.**
