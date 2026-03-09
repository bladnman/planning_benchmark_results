# Implementation Plan: TV/Movie Companion App

## Overview

Build a personal TV + movie companion app that lets users collect, organize, rate, and discover entertainment. The app uses a user's taste profile (status/interest/tags/ratings) to power multiple discovery paths: Search, AI Chat (Ask), Alchemy (concept blending), and Explore Similar.

**Stack:** Next.js 15 (App Router, TypeScript) · Supabase (hosted) · TMDB API · Tailwind CSS

---

## Phase 1 — Project Bootstrap & Infrastructure

### 1.1 Repository Setup
- Initialize Next.js 15 with App Router and TypeScript
- Configure Tailwind CSS
- `.env.example` with all required variables:
  - `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY` (browser-safe)
  - `SUPABASE_SERVICE_ROLE_KEY` (server-only)
  - `TMDB_API_KEY`
  - `AI_API_KEY`, `AI_MODEL`
  - `NAMESPACE_ID`, `DEFAULT_USER_ID`
- `.gitignore` excludes `.env*` (except `.env.example`)
- Strict `tsconfig.json`

### 1.2 Supabase Client Setup
- `/lib/supabase/client.ts` — browser client (anon key only)
- `/lib/supabase/server.ts` — server client (service role, server-only)
- `/lib/supabase/middleware.ts` — Next.js middleware for auth headers

### 1.3 Identity & Namespace Injection (Benchmark-Friendly)
- `/lib/identity.ts` — `getNamespaceId()` and `getUserId()` read from env vars
- Server routes accept `X-User-Id` header in `NODE_ENV !== 'production'` (documented, gated)
- Schema designed so switching to real OAuth later requires only config + auth wiring, no schema redesign

### 1.4 Dev Scripts
- `npm run dev` — start Next.js dev server
- `npm test` — run tests (Vitest)
- `npm run test:reset` — delete all rows for current namespace

---

## Phase 2 — Database Schema & Migrations

### 2.1 Migration Files (`/migrations/`)

**`001_create_shows.sql`** — Primary table for user collection
```sql
CREATE TABLE shows (
  id                      TEXT NOT NULL,
  namespace_id            TEXT NOT NULL,
  user_id                 TEXT NOT NULL,
  title                   TEXT NOT NULL,
  show_type               TEXT NOT NULL CHECK (show_type IN ('movie','tv','person','unknown')),
  external_ids            JSONB,
  overview                TEXT,
  genres                  TEXT[]    DEFAULT '{}',
  tagline                 TEXT,
  homepage                TEXT,
  original_language       TEXT,
  spoken_languages        TEXT[]    DEFAULT '{}',
  languages               TEXT[]    DEFAULT '{}',
  poster_url              TEXT,
  backdrop_url            TEXT,
  logo_url                TEXT,
  network_logos           TEXT[]    DEFAULT '{}',
  vote_average            NUMERIC,
  vote_count              INTEGER,
  popularity              NUMERIC,
  last_air_date           TIMESTAMPTZ,
  first_air_date          TIMESTAMPTZ,
  release_date            TIMESTAMPTZ,
  runtime                 INTEGER,
  budget                  BIGINT,
  revenue                 BIGINT,
  series_status           TEXT,
  number_of_episodes      INTEGER,
  number_of_seasons       INTEGER,
  episode_run_time        INTEGER[] DEFAULT '{}',
  my_tags                 TEXT[]    DEFAULT '{}',
  my_tags_update_date     TIMESTAMPTZ,
  my_score                NUMERIC,
  my_score_update_date    TIMESTAMPTZ,
  my_status               TEXT CHECK (my_status IN ('active','next','later','done','quit','wait')),
  my_status_update_date   TIMESTAMPTZ,
  my_interest             TEXT CHECK (my_interest IN ('excited','interested')),
  my_interest_update_date TIMESTAMPTZ,
  ai_scoop                TEXT,
  ai_scoop_update_date    TIMESTAMPTZ,
  details_update_date     TIMESTAMPTZ,
  creation_date           TIMESTAMPTZ DEFAULT NOW(),
  is_test                 BOOLEAN DEFAULT FALSE,
  provider_data           JSONB,
  PRIMARY KEY (id, namespace_id, user_id)
);
CREATE INDEX idx_shows_namespace_user   ON shows(namespace_id, user_id);
CREATE INDEX idx_shows_my_status        ON shows(namespace_id, user_id, my_status);
CREATE INDEX idx_shows_is_test          ON shows(namespace_id, is_test);
```

**`002_create_user_settings.sql`**
```sql
CREATE TABLE user_settings (
  namespace_id    TEXT    NOT NULL,
  user_id         TEXT    NOT NULL,
  user_name       TEXT    NOT NULL DEFAULT '',
  version         NUMERIC NOT NULL DEFAULT 0,
  catalog_api_key TEXT,
  ai_api_key      TEXT,
  ai_model        TEXT    NOT NULL DEFAULT 'gpt-4o',
  PRIMARY KEY (namespace_id, user_id)
);
```

**`003_create_app_metadata.sql`**
```sql
CREATE TABLE app_metadata (
  namespace_id       TEXT    NOT NULL PRIMARY KEY,
  data_model_version INTEGER NOT NULL DEFAULT 3
);
```

### 2.2 Row-Level Security
- Enable RLS on `shows` and `user_settings`
- Policy: reads/writes only where `namespace_id` and `user_id` match session config
- Service role bypasses RLS for test reset + migrations

### 2.3 Data Continuity
- `app_metadata.data_model_version` tracked per namespace
- `/lib/db/migrate.ts` — migration runner, checked on app startup
- Migrations are always additive; existing user data never lost on upgrade

---

## Phase 3 — External Catalog Integration (TMDB)

### 3.1 TMDB Client (`/lib/tmdb/`)
- `client.ts` — base fetch wrapper, API key from `TMDB_API_KEY` env
- `search.ts` — `searchShows(query, page?)` → TMDB multi-search filtered to movie + tv
- `detail.ts` — `getShowDetail(id, type)` with `append_to_response=credits,videos,recommendations,similar,images,watch/providers` (+ `seasons` for TV)
- `person.ts` — `getPersonDetail(id)` → person info + combined credits
- `mapper.ts` — TMDB response → internal `Show` type per storage-schema rules

### 3.2 Show Mapper Rules (from storage-schema.md)
- `title` = `title` (movie) or `name` (TV); reject if neither
- `showType` = `media_type` or infer from field presence
- `id` = `tmdb-{tmdbId}` (prefixed for namespace clarity)
- `externalIds` stores `imdb_id` and raw TMDB id
- Transient fields (cast, crew, seasons, videos, recs) → attached to response but NOT persisted

### 3.3 Merge Policy
- **Catalog fields:** `selectFirstNonEmpty(newValue, oldValue)` — never overwrite non-empty with empty/null
- **My* fields:** compare `*UpdateDate` timestamps; keep newer side
- `detailsUpdateDate` = NOW() after every merge
- `creationDate` = set only on first creation; catalog refreshes don't change it

---

## Phase 4 — Core Data Layer

### 4.1 Show Repository (`/lib/db/shows.ts`)
All functions scoped to `(namespaceId, userId)`:

| Function | Purpose |
|---|---|
| `getCollection()` | All saved shows for user |
| `getShow(showId)` | Single saved show or null |
| `upsertShow(show)` | Insert or merge-update using merge policy |
| `removeShow(showId)` | DELETE row (clears all My Data) |
| `updateMyStatus(showId, status)` | Sets status + `myStatusUpdateDate` |
| `updateMyInterest(showId, interest)` | Sets interest + `myInterestUpdateDate` |
| `updateMyTags(showId, tags)` | Replaces tags array + `myTagsUpdateDate` |
| `updateMyScore(showId, score)` | Sets score + `myScoreUpdateDate` |
| `updateAiScoop(showId, scoop)` | Sets scoop + `aiScoopUpdateDate` |
| `getCollectionForAI()` | Compact format (title, status, interest, tags, score) for AI context |

### 4.2 Settings Repository (`/lib/db/settings.ts`)
- `getSettings()` → `CloudSettings` or defaults
- `updateSettings(partial)` — sets `version = Date.now() / 1000` (epoch seconds)

### 4.3 Test Reset (`/lib/db/reset.ts`)
- `resetNamespace(namespaceId)` — `DELETE FROM shows WHERE namespace_id = $1`
- Called by `npm run test:reset`; never touches other namespaces

---

## Phase 5 — Next.js App Structure

### 5.1 Route Layout
```
app/
  layout.tsx                      root layout, providers
  page.tsx                        redirect to /home
  (main)/
    layout.tsx                    sidebar + content area
    home/page.tsx                 Collection Home
    find/
      page.tsx                    Find hub (defaults to Search tab)
      search/page.tsx
      ask/page.tsx
      alchemy/page.tsx
    shows/[id]/page.tsx           Show Detail
    person/[id]/page.tsx          Person Detail
    settings/page.tsx
  api/
    shows/
      route.ts                    GET (collection), POST (upsert)
      [id]/route.ts               GET, PATCH (my data fields), DELETE
    search/route.ts               TMDB search proxy + collection enrichment
    detail/[id]/route.ts          TMDB detail proxy
    person/[id]/route.ts          TMDB person proxy
    ai/
      ask/route.ts                AI chat (structured output)
      scoop/route.ts              AI scoop (streaming)
      concepts/route.ts           Concept generation
      alchemy/route.ts            Alchemy recommendations
    settings/route.ts             GET/PATCH user settings
    export/route.ts               Data export zip
    test/reset/route.ts           Dev-only namespace reset
```

### 5.2 Rendering Strategy
- Page-level data fetching: Server Components (Supabase server client)
- Interactive UI (status chips, rating slider, chat): Client Components with `"use client"`
- AI endpoints: Route Handlers (streaming where applicable)

---

## Phase 6 — Collection Home (PRD §7.1)

### 6.1 Filter Sidebar (`/components/filters/FilterPanel.tsx`)
- **All Shows** (default)
- **Tag filters** — one per unique tag across collection; "No tags" if any tagless shows exist
- **Data filters** — genre, decade, community score range
- **Media-type toggle** — All / Movies / TV (applies on top of any filter)
- Active filter persisted to `localStorage` (`lastSelectedFilter`)

### 6.2 Collection Display (`/components/collection/`)
- `CollectionHome.tsx` — applies active filter + media type toggle to full collection
- `StatusSection.tsx` — one collapsible section per group
- **Group order:** Active (prominent) → Excited (Later+Excited) → Interested (Later+Interested) → Other collapsed group (Wait, Quit, Done, unclassified Later)
- `ShowTile.tsx` — poster, title, `InCollectionBadge` (when `myStatus != null`), `RatingBadge` (when `myScore != null`)
- **Empty states:** no collection → prompt to Search/Ask; filtered empty → "No results found"

---

## Phase 7 — Search (PRD §7.2)

### 7.1 Search UI (`/components/search/`)
- `SearchBar.tsx` — text input, 300ms debounce
- `SearchResults.tsx` — poster grid; in-collection items show status badge
- `SearchPage.tsx` — auto-focuses if `autoSearch` setting is enabled
- Selecting a show → navigate to `/shows/{id}?type={movie|tv}`

### 7.2 API (`/api/search/route.ts`)
- Proxy TMDB multi-search, filter to movie + tv types
- Enrich results: for each result, check if show exists in collection and attach `myStatus`/`myScore`

---

## Phase 8 — Show Detail (PRD §7.5)

### 8.1 Data Fetching
Server component fetches TMDB detail + stored show in parallel; merges using merge policy; passes to client shell.

### 8.2 Section Components (in spec-defined narrative order)

| # | Component | Notes |
|---|---|---|
| 1 | `DetailHeader.tsx` | Backdrop/poster/logo carousel; inline trailer; graceful fallback to poster-only |
| 2 | `CoreFacts.tsx` | Year, runtime or seasons/episodes, community score bar |
| 3 | `StatusToolbar.tsx` | Status chips: Active / Interested / Excited / Wait / Done / Quit; toolbar (not scroll body) |
| 4 | `MyRating.tsx` | Rating slider 0–10; unsaved → auto-save as Done |
| 5 | `MyTags.tsx` | Tag display + picker; unsaved → auto-save as Later+Interested |
| 6 | `Overview.tsx` | Overview text |
| 7 | `AiScoop.tsx` | Toggle: "Give me the scoop!" / "Show the scoop" / "The Scoop"; streams progressively |
| 8 | `AskAboutShow.tsx` | CTA → navigates to Ask with show context seeded |
| 9 | `GenresLanguages.tsx` | Genre chips + language list |
| 10 | `RecommendationsStrand.tsx` | TMDB similar/recommended horizontal scroll |
| 11 | `ExploreSimilar.tsx` | Get Concepts → chips → Explore Shows (5 recs) |
| 12 | `StreamingProviders.tsx` | Watch providers by region |
| 13 | `CastCrew.tsx` | Horizontal strands → Person Detail |
| 14 | `SeasonsSection.tsx` | TV only |
| 15 | `BudgetRevenue.tsx` | Movies only, when data available |

### 8.3 Auto-Save Logic (`/lib/autoSave.ts`)

| Trigger | If unsaved → default to |
|---|---|
| Set status | That status |
| Set interest (Interested/Excited) | `status=Later` + chosen interest |
| Rate show | `status=Done` |
| Add tag | `status=Later`, `interest=Interested` |
| (General default) | `status=Later`, `interest=Interested` |

### 8.4 Removal Confirmation
- Modal on reselecting active status
- "Don't show again" checkbox → `hideStatusRemovalConfirmation` in localStorage
- Tracks `statusRemovalCountKey`; show warning until user opts out

---

## Phase 9 — Ask / AI Chat (PRD §7.3)

### 9.1 Chat UI (`/components/ask/`)
- `AskPage.tsx` — chat container, mode switcher (Search / Ask / Alchemy)
- `ChatMessages.tsx` — user + assistant message bubbles
- `MentionedShowsStrip.tsx` — horizontal row of AI-mentioned shows; tap → Show Detail or Search handoff
- `StarterPrompts.tsx` — 6 random prompts from pool of 80; refresh button

### 9.2 Ask Variants
- **General Ask** — standard from Find hub
- **Ask About a Show** — launched from Detail page; seeds system context with show title, overview, and user's My Data for that show

### 9.3 API Route (`/api/ai/ask/route.ts`)
- Input: `{ messages[], userLibrary (compact), seedShow? }`
- Conversation summarization: after ~10 turns, summarize oldest turns into 1–2 sentences preserving persona voice
- **Structured output format:**
  ```
  { commentary: string, showList: "Title::tmdbId::mediaType;;..." }
  ```
- `showList` parser: split on `;;`, then split each on `::` — must match exactly
- Resolve each title+tmdbId → TMDB catalog lookup → return enriched Show objects
- **Fallback:** retry once with stricter format prompt on parse failure; else return plain commentary + Search handoff flag

### 9.4 AI System Prompt Requirements
- Persona: warm TV/movie nerd friend; spoiler-safe; opinionated; vibe-first
- Tone: 70% friend / 30% critic, 60% hype / 40% measured
- Stay within TV/movies domain; redirect if asked to leave
- Library context injected: status, interest, tags, score per saved show

---

## Phase 10 — Alchemy (PRD §7.4)

### 10.1 Alchemy Flow UI (`/components/alchemy/AlchemyPage.tsx`)

| Step | Component | Behavior |
|---|---|---|
| 1 | `ShowSelector.tsx` | Search + select ≥2 shows (library + catalog) |
| 2 | `ConceptualizeTrigger.tsx` | "Conceptualize Shows" → calls concepts API |
| 3 | `ConceptChips.tsx` | Selectable chips, max 8 selected; changing shows clears concepts + results |
| 4 | `AlchemyTrigger.tsx` | "ALCHEMIZE!" → calls alchemy API |
| 5 | `AlchemyResults.tsx` | 6 recs with reasons + "More Alchemy!" chain button |

### 10.2 API Routes
- `POST /api/ai/concepts` — `{ shows[] }` → returns 8+ concept bullets shared across all input shows; 1–3 words each; specific/evocative, no generics
- `POST /api/ai/alchemy` — `{ shows[], selectedConcepts[] }` → returns 6 recs with reasons explicitly citing selected concepts; each resolved to real TMDB item
- Results session-only — never persisted to Supabase

---

## Phase 11 — Explore Similar (PRD §4.8)

### 11.1 Integration (within `ExploreSimilar.tsx` on Detail page)
1. "Get Concepts" button → `GET /api/ai/concepts?showId=` → 8 concept chips
2. User selects 1+ concepts (same cap as Alchemy for consistency)
3. "Explore Shows" button → `POST /api/ai/explore` → 5 recs with reasons
4. Each rec resolved to real TMDB item; unresolvable shown as non-interactive or Search handoff

---

## Phase 12 — Person Detail (PRD §7.6)

### 12.1 Person Page (`/app/person/[id]/page.tsx`)
- Fetch TMDB person detail + combined credits server-side
- `PersonHeader.tsx` — image gallery, name, bio
- `PersonAnalytics.tsx` — charts: average project ratings by year, top genres, projects-per-year (use lightweight chart library)
- `Filmography.tsx` — credits grouped by year; selecting a credit → Show Detail

---

## Phase 13 — Settings & Export (PRD §7.7)

### 13.1 Settings Page (`/app/settings/page.tsx`)
- **App:** font size (XS–XXL) — localStorage; search on launch — localStorage
- **User:** username — synced via `user_settings.user_name`
- **AI:** AI provider API key + model selection — `user_settings` (never committed)
- **Integrations:** content catalog API key — `user_settings`

### 13.2 Data Export (`/api/export/route.ts`)
- `GET /api/export` — query all shows + settings for `(namespaceId, userId)`
- Build JSON: `{ shows[], settings, exportDate (ISO-8601) }`
- Zip the JSON file
- Return `Content-Type: application/zip`, filename: `my-shows-{YYYY-MM-DD}.zip`

---

## Phase 14 — AI Scoop (PRD §4.9)

### 14.1 API Route (`/api/ai/scoop/route.ts`)
- Input: `{ showId, show (catalog data), userMyData? }`
- Check `aiScoopUpdateDate` — if < 4 hours old and show is in collection, return cached scoop
- Stream response: personal take → honest stack-up → The Scoop paragraph (emotional centerpiece) → fit/warnings → verdict (~150–350 words total)
- On completion: if show is in user's collection → `updateAiScoop(showId, fullText)`; if not in collection → ephemeral only

### 14.2 Scoop Client State Machine
- `idle` → "Give me the scoop!" button
- `cached` → "Show the scoop" button
- `loading` → "Generating…" with progressive text render
- `showing` → "The Scoop" header with full text

---

## Phase 15 — Cross-Cutting Concerns

### 15.1 Display Rule: User Data Always Wins
`/lib/merge.ts` — `mergeShowWithStored(catalogShow, storedShow)` applied everywhere a show is displayed:
- Catalog fields: `selectFirstNonEmpty(newValue, oldValue)`
- My* fields: timestamp-wins (newer update date keeps its value)

### 15.2 Show Tile Indicators
- `InCollectionBadge` — displayed when `myStatus != null`
- `RatingBadge` — displayed when `myScore != null`

### 15.3 All Timestamps
Every write to a `my*` field also sets the corresponding `*UpdateDate` to `new Date().toISOString()`

### 15.4 Re-adding the Same Show
`upsertShow` applies merge policy — preserves latest status/tags/rating/scoop; refreshes catalog metadata without overwriting user data

### 15.5 AI Recommendations → Real Shows
All AI surfaces (Ask, Alchemy, Explore Similar) must:
1. Return `tmdbId` for each recommended title
2. Resolve via TMDB API — accept if title matches case-insensitively
3. If resolved: show as selectable Show tile with AI reason as transient text
4. If not resolved: show as non-interactive or hand off to Search

---

## Phase 16 — Testing Infrastructure

### 16.1 Unit Tests
- Merge logic: `selectFirstNonEmpty`, timestamp-wins scenarios
- Auto-save rules: each trigger → expected default status/interest
- TMDB mapper: movie, TV, edge cases (missing title, unknown media_type)
- AI structured output parser: valid format, malformed input, fallback behavior

### 16.2 Integration Tests
- Collection CRUD via API routes using test namespace (`is_test = true`)
- Status update → verify `myStatusUpdateDate` written
- Tag add to unsaved show → verify auto-save as Later+Interested
- Rating unsaved show → verify auto-save as Done
- Remove show → verify all My Data cleared

### 16.3 Test Isolation
- All test data uses `is_test = true` and a dedicated `NAMESPACE_ID`
- `npm run test:reset` → `DELETE FROM shows WHERE namespace_id = $1` (no global teardown)
- Two different namespace IDs must produce zero data overlap (enforced by primary key design)

---

## Critical Files Summary

| File | Purpose |
|---|---|
| `.env.example` | All env vars with comments |
| `migrations/001_create_shows.sql` | Full shows schema |
| `migrations/002_create_user_settings.sql` | Settings schema |
| `migrations/003_create_app_metadata.sql` | Metadata + migration tracking |
| `lib/identity.ts` | Namespace + user ID injection |
| `lib/supabase/client.ts` | Browser client (anon key) |
| `lib/supabase/server.ts` | Server client (service role) |
| `lib/tmdb/mapper.ts` | TMDB → Show mapper with merge rules |
| `lib/db/shows.ts` | Show repository (all CRUD) |
| `lib/merge.ts` | `selectFirstNonEmpty` + timestamp-wins merge |
| `lib/autoSave.ts` | Auto-save trigger rules |
| `app/api/ai/ask/route.ts` | Ask chat with structured output + mention parsing |
| `app/api/ai/scoop/route.ts` | Scoop streaming + 4-hour cache logic |
| `app/api/ai/concepts/route.ts` | Concept generation (single + multi-show) |
| `app/api/ai/alchemy/route.ts` | Alchemy 6-rec recommendations |
| `app/api/export/route.ts` | Zip export of user data |
| `app/api/test/reset/route.ts` | Namespace reset (dev-only, gated) |
| `components/collection/StatusToolbar.tsx` | Status chips with auto-save + removal confirmation |
| `components/ask/MentionedShowsStrip.tsx` | Horizontal mentioned shows row |
| `components/alchemy/AlchemyPage.tsx` | Full alchemy multi-step flow |

---

## Verification Plan

1. **Infrastructure:** Fill `.env.example` values → `npm run dev` → app loads
2. **Database:** Apply `migrations/` to fresh Supabase → tables created; re-apply → idempotent (no errors)
3. **Namespace isolation:** Create shows under namespace A and B; query each → zero bleed
4. **Collection CRUD:** Add show, verify all fields + timestamps stored; remove show, verify row deleted
5. **Auto-save triggers:** Rate unsaved → saved as Done; tag unsaved → saved as Later+Interested
6. **Search:** Query → poster grid; in-collection items show badge
7. **Show Detail:** All 15 sections render; status chips update collection; Scoop streams and persists (when in collection)
8. **Ask:** Chat maintains context; mentioned shows strip resolves to real show tiles; conversation summarizes after 10 turns
9. **Alchemy:** Select shows → get concepts → select concepts → Alchemize → 6 recs → chain another round
10. **Explore Similar:** Get Concepts → select → Explore Shows → 5 recs
11. **Person Detail:** Filmography grouped by year; each credit → Show Detail
12. **Export:** Settings → Export My Data → `.zip` downloads containing valid JSON
13. **Test reset:** `npm run test:reset` clears namespace data; other namespaces unaffected
