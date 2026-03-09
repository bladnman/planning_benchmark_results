# Implementation Plan: TV & Movie Companion App

## Overview

A personal TV + movie companion for collecting, organizing, rating, and discovering entertainment. Users build a personal library with statuses, interest levels, tags, and ratings. The app uses that taste profile to power discovery via Search, AI Chat (Ask), Alchemy (concept blending), and Explore Similar (per-show concept discovery).

**Tech Stack (mandated by infra rider):**
- **Framework:** Next.js (latest stable, App Router)
- **Persistence:** Supabase (hosted PostgreSQL)
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **AI Provider:** Configurable (OpenAI-compatible, key injected via env)
- **Catalog Provider:** Configurable (TMDB-compatible, key injected via env)

---

## Phase 0: Project Bootstrap & Infrastructure

### 0.1 Repo Initialization
- `npx create-next-app@latest` with TypeScript, Tailwind, App Router, ESLint
- Install core dependencies: `@supabase/supabase-js`, `@supabase/ssr`, `openai`, `zod`
- Install dev dependencies: `vitest`, `@testing-library/react`, `playwright` (e2e)

### 0.2 Environment Variable Interface
Create `.env.example` with all required variables:
```
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=  # server-only; never exposed to client

# External catalog (e.g. TMDB)
CATALOG_API_KEY=
CATALOG_BASE_URL=https://api.themoviedb.org/3

# AI provider
AI_API_KEY=
AI_BASE_URL=https://api.openai.com/v1
AI_DEFAULT_MODEL=gpt-4o

# Identity/isolation
NAMESPACE_ID=dev-default
DEFAULT_USER_ID=dev-user-001   # dev/benchmark mode only

# App behavior
NODE_ENV=development
NEXT_PUBLIC_APP_ENV=development
```

`.gitignore` entries:
```
.env
.env.local
.env.*.local
```

### 0.3 Identity & Isolation Model
- `NAMESPACE_ID`: a stable string assigned per build at deploy time. All persisted rows include this column.
- `user_id`: opaque UUID/string per user. All user-owned rows include this column.
- Effective partition key for all user data: `(namespace_id, user_id)`
- **Dev/benchmark auth injection:** In non-production mode, server routes accept `DEFAULT_USER_ID` from env or an optional `X-User-Id` request header. This must be disabled/gated behind `NODE_ENV !== 'production'`.
- Auth wiring is isolated to a single `lib/auth.ts` module so swapping to real OAuth (Supabase Auth, Google) later requires only that file and configuration changes — no schema changes.

### 0.4 Package Scripts
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:e2e": "playwright test",
    "test:reset": "ts-node scripts/reset-namespace.ts",
    "db:migrate": "supabase db push",
    "db:seed": "ts-node scripts/seed.ts"
  }
}
```

---

## Phase 1: Database Schema & Migrations

### 1.1 Supabase Migration Files
All migrations live in `supabase/migrations/`. A fresh database can be created deterministically by running all migrations in order.

**Migration 001 — Core tables:**

```sql
-- shows table: user-saved shows with all My Data fields
CREATE TABLE shows (
  id               TEXT NOT NULL,
  namespace_id     TEXT NOT NULL,
  user_id          TEXT NOT NULL,

  -- identity
  title            TEXT NOT NULL,
  show_type        TEXT NOT NULL CHECK (show_type IN ('movie','tv','person','unknown')),
  external_ids     JSONB,

  -- catalog metadata
  overview         TEXT,
  genres           TEXT[]    DEFAULT '{}',
  tagline          TEXT,
  homepage         TEXT,
  original_language TEXT,
  spoken_languages TEXT[]    DEFAULT '{}',
  languages        TEXT[]    DEFAULT '{}',

  -- images
  poster_url       TEXT,
  backdrop_url     TEXT,
  logo_url         TEXT,
  network_logos    TEXT[]    DEFAULT '{}',

  -- ratings / popularity
  vote_average     NUMERIC,
  vote_count       INTEGER,
  popularity       NUMERIC,

  -- dates (ISO-8601 strings)
  release_date     TEXT,
  first_air_date   TEXT,
  last_air_date    TEXT,

  -- movie fields
  runtime          INTEGER,
  budget           BIGINT,
  revenue          BIGINT,

  -- tv fields
  series_status    TEXT,
  number_of_episodes INTEGER,
  number_of_seasons  INTEGER,
  episode_run_time   INTEGER[] DEFAULT '{}',

  -- user data (my*)
  my_tags              TEXT[]    DEFAULT '{}',
  my_tags_update_date  TIMESTAMPTZ,
  my_score             NUMERIC,
  my_score_update_date TIMESTAMPTZ,
  my_status            TEXT CHECK (my_status IN ('active','next','later','done','quit','wait')),
  my_status_update_date TIMESTAMPTZ,
  my_interest          TEXT CHECK (my_interest IN ('excited','interested')),
  my_interest_update_date TIMESTAMPTZ,

  -- ai data
  ai_scoop             TEXT,
  ai_scoop_update_date TIMESTAMPTZ,

  -- management
  details_update_date  TIMESTAMPTZ,
  creation_date        TIMESTAMPTZ DEFAULT NOW(),
  is_test              BOOLEAN DEFAULT FALSE,

  -- provider data
  provider_data        JSONB,

  PRIMARY KEY (namespace_id, user_id, id)
);

CREATE INDEX idx_shows_namespace_user ON shows (namespace_id, user_id);
CREATE INDEX idx_shows_my_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_is_test ON shows (namespace_id, is_test);

-- cloud settings (synced per user)
CREATE TABLE cloud_settings (
  namespace_id  TEXT NOT NULL,
  user_id       TEXT NOT NULL,
  user_name     TEXT NOT NULL DEFAULT '',
  version       NUMERIC NOT NULL DEFAULT 0,  -- epoch seconds for conflict resolution
  catalog_api_key TEXT,
  ai_api_key    TEXT,
  ai_model      TEXT NOT NULL DEFAULT 'gpt-4o',
  PRIMARY KEY (namespace_id, user_id)
);

-- app metadata (per namespace, for schema migration tracking)
CREATE TABLE app_metadata (
  namespace_id       TEXT PRIMARY KEY,
  data_model_version INTEGER NOT NULL DEFAULT 3
);
```

**Migration 002 — Indexes for filter performance:**
```sql
CREATE INDEX idx_shows_genres ON shows USING GIN (genres);
CREATE INDEX idx_shows_my_tags ON shows USING GIN (my_tags);
CREATE INDEX idx_shows_show_type ON shows (namespace_id, user_id, show_type);
```

### 1.2 Row-Level Security (RLS) Policy Approach
- Enable RLS on all tables.
- In benchmark/dev mode with injected user IDs, use a permissive policy gated by `namespace_id` match from a server-side session context. 
- Policy structure is designed to accept a real Supabase Auth `auth.uid()` replacement when OAuth is added — no schema redesign required.

### 1.3 Reset Script
`scripts/reset-namespace.ts`: deletes all rows in `shows` and `cloud_settings` where `namespace_id = $NAMESPACE_ID AND is_test = true` (or the full namespace for a full reset). Supports destructive test cleanup without global teardown.

---

## Phase 2: Core Library Modules

### 2.1 Directory Structure
```
src/
  app/                    # Next.js App Router pages
    (home)/               # Collection Home layout
    find/                 # Find/Discover hub
      search/
      ask/
      alchemy/
    show/[id]/            # Show Detail
    person/[id]/          # Person Detail
    settings/             # Settings
    api/                  # API routes (server-side)
      shows/
      catalog/
      ai/
  components/             # Shared UI components
    show/                 # ShowTile, ShowDetail sections
    filters/              # Sidebar, FilterChip
    ai/                   # AskChat, AlchemyFlow, ConceptChips
    ui/                   # Button, Chip, Modal, RatingSlider, etc.
  lib/
    auth.ts               # Identity resolution (dev injection + future OAuth)
    supabase/
      client.ts           # Browser client (anon key)
      server.ts           # Server client (service role, server-only)
    catalog/
      client.ts           # External catalog API abstraction
      mappers.ts          # Catalog payload → Show shape
      merge.ts            # Merge/overwrite rules
    ai/
      client.ts           # AI provider abstraction
      prompts/            # Surface-specific prompt builders
        scoop.ts
        ask.ts
        concepts.ts
        recommendations.ts
    db/
      shows.ts            # CRUD operations for shows table
      settings.ts         # CRUD for cloud_settings
    types.ts              # Shared TypeScript types (mirrors storage-schema.ts)
    utils.ts              # Date helpers, formatters
  hooks/                  # React hooks for client state
  scripts/                # CLI scripts (reset, seed, migrate)
```

### 2.2 Type Definitions (`lib/types.ts`)
Mirror the TypeScript types from `storage-schema.ts` exactly:
- `ShowType`, `MyStatusType`, `MyInterestType`
- `Show`, `ProviderData`, `CloudSettings`, `AppMetadata`
- `FilterConfiguration`, `FilterType`, `LocalSettings`, `UserDefaultsUIState`
- `StorageSnapshot` (for export/backup)

### 2.3 Auth Module (`lib/auth.ts`)
```typescript
// Returns { userId, namespaceId } for the current request
// Dev mode: reads from X-User-Id header or DEFAULT_USER_ID env
// Production: reads from Supabase Auth session
export async function getIdentity(req?: Request): Promise<Identity>
```
This is the single seam for future OAuth integration.

### 2.4 Supabase Clients
- `lib/supabase/client.ts`: browser client using `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `lib/supabase/server.ts`: server client using `SUPABASE_SERVICE_ROLE_KEY` (never imported in client code)

### 2.5 Show CRUD (`lib/db/shows.ts`)
All operations are scoped to `(namespace_id, user_id)`:
- `getShows(identity, filters?)` — list with optional filter predicates
- `getShow(identity, showId)` — single show
- `upsertShow(identity, show)` — create or merge using field merge rules
- `deleteShow(identity, showId)` — remove from collection, clears all My Data
- `updateMyData(identity, showId, patch)` — partial update to my* fields with timestamp
- `getTagLibrary(identity)` — distinct tags across all saved shows

### 2.6 Merge Logic (`lib/catalog/merge.ts`)
Implements the merge/overwrite policy from `storage-schema.md`:
- Non-my fields: `selectFirstNonEmpty(newValue, oldValue)` — never overwrite non-empty with empty
- My fields: resolve by timestamp — keep newer, or keep the side that has a date if only one does
- Updates `details_update_date` to `now()` after merge
- Preserves `creation_date` from original

---

## Phase 3: API Routes (Next.js Server)

All API routes live under `src/app/api/`. They call `getIdentity()` first, then interact with Supabase via the server client. The anon key is never used server-side for elevated operations.

### 3.1 Shows API (`/api/shows`)
- `GET /api/shows` — list user's collection, supports `?filter=`, `?type=`, `?status=`, `?tag=`
- `GET /api/shows/[id]` — single saved show
- `POST /api/shows` — save/upsert a show (accepts Show shape)
- `PATCH /api/shows/[id]` — update My Data fields (status, interest, tags, score, aiScoop)
- `DELETE /api/shows/[id]` — remove from collection

### 3.2 Catalog API (`/api/catalog`)
- `GET /api/catalog/search?q=&type=` — proxy to external catalog search
- `GET /api/catalog/[id]?type=` — fetch full catalog details (credits, seasons, images, etc.)
- `GET /api/catalog/person/[id]` — fetch person details + filmography
- Response: mapped Show shape (transient fields included, not persisted)

**Catalog mapper** (`lib/catalog/mappers.ts`):
- Maps external catalog payload fields to Show shape
- Handles show type inference (movie vs tv)
- Normalizes dates, genres (names not IDs), images to full URLs
- Chooses "best" logo deterministically (prefer English, highest vote)
- Transient fields (cast, crew, seasons, videos, recommendations) are attached but not persisted

### 3.3 AI API (`/api/ai`)
All AI calls are server-side to protect the API key.

- `POST /api/ai/scoop` — generate AI Scoop for a show
  - Body: `{ showId, showTitle, overview, ... }`
  - Returns: streaming text response
  - After generation: if show is in collection, persist `aiScoop` + `aiScoopUpdateDate`
  - Freshness check: if cached scoop < 4 hours old, return cached

- `POST /api/ai/ask` — chat message
  - Body: `{ messages, userLibrarySummary }`
  - Returns: `{ commentary, showList }` (structured output with mentioned shows)
  - Summarizes older turns (after ~10 messages) preserving persona

- `POST /api/ai/concepts` — generate concepts for show(s)
  - Body: `{ shows: Show[], mode: 'single' | 'multi' }`
  - Returns: `string[]` — 1–3 word evocative concepts
  - Multi-show: concepts must be shared across all inputs

- `POST /api/ai/recommendations` — concept-based recommendations
  - Body: `{ selectedConcepts, sourceShows, userLibrarySummary }`
  - Returns: `{ title, externalId, mediaType, reason }[]`
  - Count: 5 for Explore Similar, 6 for Alchemy

### 3.4 Settings API (`/api/settings`)
- `GET /api/settings` — fetch CloudSettings for user
- `PUT /api/settings` — update CloudSettings (conflict resolution via `version` timestamp)

### 3.5 Export API (`/api/export`)
- `GET /api/export` — returns a `.zip` containing:
  - `shows.json` — all saved shows in collection (ISO-8601 dates)
  - `settings.json` — CloudSettings

---

## Phase 4: UI Implementation

### 4.1 Global Layout & Navigation
**Layout (`app/layout.tsx`):**
- Left sidebar: filter panel (All Shows, tag filters, data filters)
- Main content area (router outlet)
- Persistent top nav with Find/Discover and Settings links
- Media-type toggle (All / Movies / TV) in sidebar header

**Filter Panel:**
- "All Shows" default
- Tag filters: one per tag in library; show "No tags" filter if untagged shows exist
- Data filters: genre, decade, community score range
- Filters drive the collection view via URL params or context

### 4.2 Collection Home (`app/(home)/page.tsx`)
- Fetches user's shows filtered by active filter and media-type toggle
- Groups by status in order: Active (prominent), Excited (Later+Excited), Interested (Later+Interested), collapsed group (Wait, Quit, Done, unclassified Later)
- `ShowTile` component: poster, title, in-collection badge, user rating badge
- Empty states: no collection → prompt to Search/Ask; filter yields none → "No results found"

**Show Tile (`components/show/ShowTile.tsx`):**
- Poster image with fallback
- In-collection indicator overlay when `myStatus` exists
- Rating indicator overlay when `myScore` exists
- Click → navigate to Show Detail

### 4.3 Find / Discover Hub (`app/find/`)
Mode switcher (tabs/segments): Search | Ask | Alchemy

**Search (`app/find/search/page.tsx`):**
- Text input, live queries to `/api/catalog/search`
- Poster grid results
- In-collection items marked with badge
- Click → Show Detail
- `autoSearch` setting: if true, auto-focus search on Find open

**Ask (`app/find/ask/page.tsx`):**
- Chat UI: user/assistant message bubbles
- Welcome view: 6 random starter prompts + refresh button
- Sends messages to `/api/ai/ask`, renders `commentary` text
- "Mentioned shows" horizontal strip from `showList` response field
- Clicking a mentioned show: resolve via `/api/catalog/[id]` → Show Detail (or fall back to Search)
- Session context: retain turns in local React state; after ~10 turns, include summarized history in next request
- Session ends on page leave / explicit reset

**Alchemy (`app/find/alchemy/page.tsx`):**

Flow as distinct UI steps:
1. **Select Shows** — search catalog + library, pick ≥2 starting shows (AddShowCard grid)
2. **Conceptualize Shows** button → calls `/api/ai/concepts` with selected shows
3. **Select Concepts** — concept chips, max 8 selectable; changing shows clears downstream
4. **ALCHEMIZE! button** → calls `/api/ai/recommendations` with selected concepts + source shows
5. **Results** — 6 show cards with AI reason; "More Alchemy!" button chains next round using results as new inputs

### 4.4 Show Detail (`app/show/[id]/page.tsx`)
Fetches: catalog details (transient) + saved show data (if in collection).

Sections in narrative order (per `detail_page_experience.md`):
1. **Header media carousel** — backdrop images, logos, trailer (inline video); graceful fallback to poster
2. **Core facts row** — year, runtime/seasons, community score bar
3. **My Tags** — tag chips + tag picker; adding first tag auto-saves as Later+Interested
4. **Overview + Scoop toggle**
   - Overview text
   - Scoop toggle button: "Give me the scoop!" / "Show the scoop" / "The Scoop" (when open)
   - Scoop streams in progressively via SSE/streaming fetch; shows "Generating…" spinner
   - Cached scoop: check `aiScoopUpdateDate`; regenerate if > 4 hours old
   - Only persist if show is in collection
5. **"Ask about this show" CTA** — opens Ask with show context seeded
6. **Genres + languages**
7. **Recommendations strand** — horizontal scroll of similar/recommended shows (from catalog)
8. **Explore Similar** — Get Concepts → concept chip selection → Explore Shows → 5 results with reasons
9. **Streaming availability** — provider logos by region (flatrate/rent/buy)
10. **Cast & Crew** — horizontal strands → Person Detail
11. **Seasons** (TV only) — season list with episode counts
12. **Budget vs Revenue** (movies when available)

**Toolbar (sticky/floating):**
- Status/Interest chips: Active | Interested | Excited | Done | Wait | Quit
- Interested → sets `myStatus=later, myInterest=interested`; Excited → `myStatus=later, myInterest=excited`
- Reselecting active status → confirmation dialog → clears all My Data and removes from collection
- `hideStatusRemovalConfirmation` setting: suppress dialog after N confirmations
- My Rating slider: 0–10; rating an unsaved show auto-saves as Done; clearing rating sets to null

**Auto-save rules (enforced in Detail route handlers):**
- Any status chip click → saves to collection
- Rating adjustment on unsaved show → save with `myStatus=done`
- First tag on unsaved show → save with `myStatus=later, myInterest=interested`
- Default when saving without explicit status: `myStatus=later, myInterest=interested`

### 4.5 Person Detail (`app/person/[id]/page.tsx`)
- Fetches from `/api/catalog/person/[id]`
- Image gallery, name, bio
- Analytics charts: average project ratings, top genres, projects-by-year (lightweight bar/line charts)
- Filmography grouped by year; clicking a credit → Show Detail

### 4.6 Settings (`app/settings/page.tsx`)
Sections:
- **Display:** font size (XS/S/M/L/XL/XXL), search-on-launch toggle
- **User:** username input (synced via CloudSettings)
- **AI:** AI API key input, model selector (synced; dev mode may prefill from env, never committed)
- **Integrations:** catalog API key input (synced)
- **Your Data:**
  - "Export My Data" button → fetches `/api/export` → triggers `.zip` download
  - Import/Restore: noted as not yet implemented, placeholder in UI

---

## Phase 5: AI Integration Details

### 5.1 Prompt Architecture (`lib/ai/prompts/`)

**Shared system context** injected into all prompts:
- User's library summary (titles, statuses, tags, ratings) — compressed representation
- Persona instructions (from `ai_voice_personality.md`)

**Scoop prompt (`prompts/scoop.ts`):**
- Input: show title, overview, year, genres, community score
- System: persona instructions, structured output format (personal take, honest stack-up, The Scoop paragraph, fit/warnings, "Worth it?" verdict)
- ~150–350 words target, streams progressively

**Ask prompt (`prompts/ask.ts`):**
- Input: conversation history (recent turns + summary of older turns), user library, current show context (if "Ask about this show")
- Output format: `{ commentary, showList }` where `showList` = `"Title::externalId::mediaType;;"` format
- Summarization: after ~10 turns, include 1–2 sentence summary in system context, preserving persona voice

**Concepts prompt (`prompts/concepts.ts`):**
- Input: show(s) title/overview/genres
- Output: bullet list, 1–3 word evocative concepts
- Multi-show: concepts must be shared across all inputs
- Quality constraints: specific over generic, diverse axes, ordered by strength

**Recommendations prompt (`prompts/recommendations.ts`):**
- Input: selected concepts, source shows, user library summary
- Output: JSON array `[{ title, externalId, mediaType, reason }]`
- Count: 5 (Explore Similar) or 6 (Alchemy)
- Reasons must cite selected concepts explicitly

### 5.2 AI Response → Catalog Resolution (`lib/ai/resolver.ts`)
- For each AI recommendation: look up catalog by `externalId` → verify title matches case-insensitively
- If resolved: return full Show object (transient), attach AI `reason` as transient field
- If not found: return non-interactive placeholder with option to hand off to Search
- Structured output parse failure: retry once with stricter prompt, then fall back to commentary-only + Search handoff

### 5.3 AI Scoop Freshness
- On Scoop request: check `aiScoopUpdateDate`; if within 4 hours, return cached
- After generation: PATCH show with new `aiScoop` + `aiScoopUpdateDate` (only if in collection)
- Unsaved show: generate and return scoop without persisting; offer to save

---

## Phase 6: Data Behaviors Implementation

### 6.1 Collection Membership
- A show is "in collection" if it has a row in `shows` table with non-null `myStatus`
- Display rule: wherever a show appears, if it's in collection, overlay the user's My Data

### 6.2 Status/Interest State Machine
Managed in `lib/status-machine.ts`:
```
Interested chip → { myStatus: 'later', myInterest: 'interested' }
Excited chip    → { myStatus: 'later', myInterest: 'excited' }
Active chip     → { myStatus: 'active', myInterest: null }
Done chip       → { myStatus: 'done',   myInterest: null }
Wait chip       → { myStatus: 'wait',   myInterest: null }
Quit chip       → { myStatus: 'quit',   myInterest: null }
Reselect active → show confirmation → delete show row → clear all My Data
```
Interest is only relevant when `myStatus = 'later'`. If status changes away from Later, interest becomes irrelevant (retained in DB for if/when status returns to Later).

### 6.3 Timestamps
Every `PATCH` to a My Data field also writes the corresponding `*_update_date` to `now()`. These are used for:
- Sorting (recently updated first)
- Merge conflict resolution (newer wins on sync)
- AI Scoop freshness check

### 6.4 Re-adding the Same Show
If a show's `id` already exists in the user's collection (via `upsertShow`):
- Preserve existing My Data using timestamp merge rules
- Refresh public catalog metadata using `selectFirstNonEmpty` rules
- Update `details_update_date` to now

### 6.5 AI Data Persistence
| Surface | Persisted | Storage |
|---|---|---|
| AI Scoop | Yes (if in collection) | `shows.ai_scoop` + `ai_scoop_update_date` |
| Alchemy results | No | React state, session only |
| Ask chat history | No | React state, session only |
| Mentioned shows strip | No | Derived from current chat state |

---

## Phase 7: Filters & Collection Views

### 7.1 Filter Configuration
Client stores `lastSelectedFilter` in `localStorage` (not Supabase) — this is UI state, not user data.

Filter types:
- `all` — no filter predicate
- `myTag` — `WHERE my_tags @> ARRAY[$tag]`
- `genre` — `WHERE genres @> ARRAY[$genre]`
- `decade` — `WHERE EXTRACT(year FROM release_date) BETWEEN $start AND $end`
- `communityScore` — `WHERE vote_average BETWEEN $min AND $max`
- `myStatus` — (available in data model, not currently a first-class UI filter but present for future use)

"No tags" filter: `WHERE array_length(my_tags, 1) IS NULL OR my_tags = '{}'`

Media-type toggle applied on top of any filter: `AND show_type = 'movie'` or `AND show_type = 'tv'`

### 7.2 Tag Library
Derived by querying `SELECT DISTINCT unnest(my_tags) FROM shows WHERE namespace_id=? AND user_id=?`.
Tag filters appear in sidebar dynamically based on library contents.

---

## Phase 8: Data Export

### 8.1 Export Format
`GET /api/export` builds a `.zip` containing:
- `shows.json` — array of all Show objects with all My Data; dates as ISO-8601
- `settings.json` — CloudSettings

Implemented server-side using a zip library (e.g., `jszip` or `archiver`).

### 8.2 Data Continuity / Migrations
- `app_metadata.data_model_version` tracks current schema version (starts at 3)
- On app startup: server checks version and runs any pending migration logic
- Migration scripts in `supabase/migrations/` are additive; no destructive changes without version bump
- Migrator (`lib/db/migrate.ts`) runs on first request after deploy if version is behind

---

## Phase 9: Testing Infrastructure

### 9.1 Unit Tests (Vitest)
- `lib/catalog/merge.ts` — merge logic for all field types
- `lib/status-machine.ts` — all status transitions and auto-save rules
- `lib/ai/resolver.ts` — catalog resolution and fallback behavior
- `lib/auth.ts` — identity injection in dev mode

### 9.2 Integration Tests (Vitest + Supabase)
- Use `namespace_id = 'test-' + testRunId` for isolation
- Test CRUD operations via `lib/db/shows.ts` against a real Supabase test instance
- `npm run test:reset` clears `is_test = true` rows for the test namespace after each run

### 9.3 E2E Tests (Playwright)
- Key user journeys (Section 9 of PRD):
  - Build collection (search → save via status chip)
  - Rate-to-save (open show → adjust rating → auto-saved as Done)
  - Tag-to-save (open show → add tag → auto-saved as Later+Interested)
  - Ask discovery session
  - Alchemy full flow
  - Export data

---

## Phase 10: Cloud Agent Compatibility

Per `infra_rider_prd.md`:
- Docker is NOT required. Supabase hosted instance is primary.
- All configuration via environment variables — no code editing required
- `npm run dev` starts the app; environment variables wire it to the correct Supabase instance and namespace
- Test isolation via `NAMESPACE_ID` — no global teardown needed
- `npm run test:reset` clears only the test namespace's `is_test` data

---

## Implementation Order (Sequenced)

1. **Phase 0** — Bootstrap, env, identity model, scripts
2. **Phase 1** — Database schema, migrations, reset script
3. **Phase 2** — Core library modules: types, auth, Supabase clients, Show CRUD, merge logic
4. **Phase 3.1 + 3.2** — Shows API + Catalog API (no AI yet)
5. **Phase 4.2** — Collection Home + ShowTile (enough to verify DB + catalog round-trip)
6. **Phase 4.4 (partial)** — Show Detail: header, core facts, status/rating/tags toolbar, overview (no AI sections yet)
7. **Phase 4.1** — Global layout, filter sidebar, navigation
8. **Phase 4.3 (Search only)** — Find hub with Search mode
9. **Phase 3.3** — AI API routes
10. **Phase 5** — AI prompts, resolver
11. **Phase 4.3 (Ask + Alchemy)** — Ask chat UI + Alchemy flow
12. **Phase 4.4 (AI sections)** — Scoop, Explore Similar on Show Detail
13. **Phase 4.5** — Person Detail
14. **Phase 4.6** — Settings
15. **Phase 3.5 + Phase 8** — Export API
16. **Phase 6** — Verify all data behavior rules (auto-save, merge, timestamps)
17. **Phase 7** — Filter panel completeness (tags, genres, decades, score ranges)
18. **Phase 9** — Tests (unit, integration, e2e)
19. **Phase 10** — Cloud agent compatibility verification

---

## Key Constraints & Cross-Cutting Concerns

| Constraint | Implementation |
|---|---|
| User's version takes precedence | Timestamp merge rules in `merge.ts`; My Data wins over catalog refresh |
| Every recommendation maps to a real show | `resolver.ts` looks up catalog by external ID + title match |
| Taste-aware AI | User library summary injected into all AI prompts |
| Spoiler-safe by default | System prompt instruction; never reveal plot specifics unless user asks |
| Backend is source of truth | All CRUD through API routes; localStorage only for UI state |
| `(namespace_id, user_id)` partition | All DB queries scoped to both; composite primary key on shows table |
| No Docker required | Hosted Supabase; no local services |
| OAuth-ready without schema redesign | `lib/auth.ts` is the only seam; `user_id` is an opaque string throughout |
| Secrets never committed | `.gitignore` covers `.env*`; service role key is server-only |
| Data continuity across upgrades | Versioned migrations; `app_metadata.data_model_version` gating |
