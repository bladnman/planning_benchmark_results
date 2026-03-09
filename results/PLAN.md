# TV + Movie Companion App — Implementation Plan

## Tech Stack (Mandated by Infra Rider)

- **Framework:** Next.js (latest stable), App Router
- **Persistence:** Supabase (hosted preferred; Docker optional and documented)
- **External Catalog:** TMDB API (or compatible)
- **AI:** Configurable provider via `AI_API_KEY` + `AI_MODEL` env vars
- **Language:** TypeScript throughout

---

## Repository Layout

```
/
├── app/                        # Next.js App Router pages
│   ├── (home)/                 # Collection Home (root)
│   ├── find/
│   │   ├── search/
│   │   ├── ask/
│   │   └── alchemy/
│   ├── show/[id]/              # Show Detail
│   ├── person/[id]/            # Person Detail
│   └── settings/
├── components/
│   ├── collection/             # Home, StatusSection, ShowTile
│   ├── show/                   # Header, StatusChips, RatingBar, TagPicker, Scoop, ExploreSimilar, etc.
│   ├── find/                   # SearchPanel, AskChat, AlchemyFlow
│   ├── person/                 # PersonHeader, Filmography, AnalyticsCharts
│   ├── layout/                 # NavBar, FilterSidebar, MediaTypeToggle
│   └── ui/                     # Shared primitives (Button, Chip, Tile, etc.)
├── lib/
│   ├── supabase/               # browser.ts, server.ts clients
│   ├── catalog/                # TMDB client, show mapper, merge logic
│   ├── ai/                     # AI client, prompts per surface, stream helpers
│   └── types/                  # Canonical TypeScript types (Show, CloudSettings, etc.)
├── server/
│   ├── actions/                # Server Actions: saveShow, removeShow, updateMyData, etc.
│   └── api/                    # Route handlers: /api/ai/*, /api/catalog/*, /api/export
├── supabase/
│   └── migrations/             # SQL migration files
├── scripts/
│   ├── dev.ts                  # Start app (npm run dev)
│   ├── test.ts                 # Run tests (npm test)
│   └── reset-namespace.ts      # Reset test data (npm run test:reset)
├── .env.example
└── .gitignore
```

---

## Identity & Isolation Model

Every request carries a `(namespace_id, user_id)` pair. This is the fundamental partition for all persisted data.

```
NAMESPACE_ID=<stable-build-id>       # set per build, never changes
DEV_USER_ID=<default-dev-user-uuid>  # injected via env in dev/test
```

- In dev/test: `user_id` resolved from `DEV_USER_ID` env var, or overridden per-request via `X-User-Id` header (only in non-production mode).
- All DB queries always include `WHERE namespace_id = $1 AND user_id = $2`.
- Destructive test resets are scoped: `DELETE FROM shows WHERE namespace_id = $1 AND is_test = true`.
- Designed so swapping to real OAuth later requires only auth wiring, not a schema redesign.

---

## Environment Configuration

### `.env.example`

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=          # Your Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=     # Supabase anon/public key (safe for browser)
SUPABASE_SERVICE_ROLE_KEY=         # Supabase service role key (server-only, never expose to browser)

# Build identity (required)
NAMESPACE_ID=                      # Stable unique ID for this build/run
DEV_USER_ID=                       # Default user UUID for dev/test identity injection

# External catalog (TMDB or compatible)
CATALOG_API_KEY=                   # API key for external show catalog
CATALOG_BASE_URL=https://api.themoviedb.org/3

# AI provider
AI_API_KEY=                        # API key for AI completions
AI_MODEL=                          # Model identifier (e.g. gpt-4o, claude-3-5-sonnet)
AI_BASE_URL=                       # Optional: override AI provider base URL
```

All secrets excluded from `.gitignore`. Only `.env.example` committed.

---

## Database Schema (Supabase Migrations)

### Migration 001: Initial Schema (`supabase/migrations/001_initial_schema.sql`)

#### `shows` table

Primary key: `(id, namespace_id, user_id)`

```sql
CREATE TABLE shows (
  -- Identity
  id                    TEXT NOT NULL,
  namespace_id          TEXT NOT NULL,
  user_id               TEXT NOT NULL,
  title                 TEXT NOT NULL,
  show_type             TEXT NOT NULL,       -- 'movie' | 'tv' | 'person' | 'unknown'
  external_ids          JSONB,

  -- Catalog meta
  overview              TEXT,
  genres                TEXT[]    DEFAULT '{}',
  tagline               TEXT,
  homepage              TEXT,
  original_language     TEXT,
  spoken_languages      TEXT[]    DEFAULT '{}',
  languages             TEXT[]    DEFAULT '{}',

  -- Images
  poster_url            TEXT,
  backdrop_url          TEXT,
  logo_url              TEXT,
  network_logos         TEXT[]    DEFAULT '{}',

  -- Ratings / popularity
  vote_average          FLOAT,
  vote_count            INT,
  popularity            FLOAT,

  -- Dates (ISO-8601 stored as TIMESTAMPTZ)
  first_air_date        TIMESTAMPTZ,
  last_air_date         TIMESTAMPTZ,
  release_date          TIMESTAMPTZ,

  -- Movie-specific
  runtime               INT,
  budget                BIGINT,
  revenue               BIGINT,

  -- TV-specific
  series_status         TEXT,
  number_of_episodes    INT,
  number_of_seasons     INT,
  episode_run_time      INT[]     DEFAULT '{}',

  -- User data ("My Data")
  my_tags               TEXT[]    DEFAULT '{}',
  my_tags_update_date   TIMESTAMPTZ,
  my_score              FLOAT,
  my_score_update_date  TIMESTAMPTZ,
  my_status             TEXT,               -- 'active' | 'next' | 'later' | 'done' | 'quit' | 'wait'
  my_status_update_date TIMESTAMPTZ,
  my_interest           TEXT,               -- 'excited' | 'interested'
  my_interest_update_date TIMESTAMPTZ,

  -- AI data
  ai_scoop              TEXT,
  ai_scoop_update_date  TIMESTAMPTZ,

  -- Management
  details_update_date   TIMESTAMPTZ,
  creation_date         TIMESTAMPTZ DEFAULT NOW(),
  is_test               BOOLEAN     DEFAULT FALSE,
  provider_data         JSONB,

  PRIMARY KEY (id, namespace_id, user_id)
);
```

#### `cloud_settings` table

```sql
CREATE TABLE cloud_settings (
  namespace_id     TEXT NOT NULL,
  user_id          TEXT NOT NULL,
  user_name        TEXT,
  version          FLOAT,                   -- epoch seconds for conflict resolution
  catalog_api_key  TEXT,
  ai_api_key       TEXT,
  ai_model         TEXT,
  PRIMARY KEY (namespace_id, user_id)
);
```

#### `app_metadata` table

```sql
CREATE TABLE app_metadata (
  namespace_id        TEXT NOT NULL,
  data_model_version  INT  DEFAULT 3,
  PRIMARY KEY (namespace_id)
);
```

### Migration 002: Indexes (`supabase/migrations/002_indexes.sql`)

```sql
CREATE INDEX idx_shows_ns_user         ON shows (namespace_id, user_id);
CREATE INDEX idx_shows_my_status       ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_my_tags         ON shows USING GIN (my_tags);
CREATE INDEX idx_shows_is_test         ON shows (namespace_id, is_test);
```

---

## Data Layer

### Canonical Types (`lib/types/`)

Mirror `storage-schema.ts` exactly:
- `Show`, `ShowType`, `MyStatusType`, `MyInterestType`
- `ProviderData`, `CloudSettings`, `AppMetadata`
- `FilterConfiguration`, `FilterType`, `LocalSettings`

### External Catalog Client (`lib/catalog/client.ts`)

| Function | Description |
|---|---|
| `searchShows(query, mediaType?)` | Title/keyword search, returns `Show[]` (transient) |
| `getShowDetails(id, mediaType)` | Full show with transient credits, seasons, videos, similar, providers |
| `getPersonDetails(id)` | Person profile with filmography |
| `getWatchProviders(id, mediaType, region)` | Provider IDs by country |

### Show Mapper (`lib/catalog/mapper.ts`)

Maps raw catalog API response → canonical `Show` type:
- Title: prefer `title` (movies) or `name` (TV); fail if neither
- `showType`: from explicit media_type field; fallback: `name` present → `tv`, `title` present → `movie`, else `unknown` (reject)
- Image URLs: constructed from base URL + path; best English logo selected deterministically
- Genre IDs → genre name strings
- Transient fields (credits, seasons, videos, recommendations, similar) attached to non-persisted properties

### Merge Logic (`lib/catalog/merge.ts`)

When refreshed catalog data is merged into an existing stored show:

- **Non-user fields** (`title`, `overview`, `genres`, `poster_url`, etc.): `selectFirstNonEmpty(newValue, existingValue)` — never overwrite a populated field with null/empty.
- **User fields** (`my_tags`, `my_score`, `my_status`, `my_interest`): compare `*_update_date` — keep the newer side. If only one side has a date, keep that side.
- `details_update_date` → set to NOW() after every merge.
- `creation_date` → set only on first insert; never overwritten.

### Server Actions (`server/actions/`)

| Action | Behavior |
|---|---|
| `saveShow(show, myData?)` | Upsert with merge; apply default status/interest rules |
| `removeShow(showId, confirmed)` | Delete row; clear all My Data; requires `confirmed = true` |
| `updateMyStatus(showId, status, interest?)` | Update status + timestamp; handle re-select → removal flow |
| `updateMyRating(showId, score)` | Auto-saves as `Done` if show not yet in collection |
| `updateMyTags(showId, tags)` | Auto-saves as `Later + Interested` if not in collection |
| `updateAiScoop(showId, scoop)` | Persists only when show is in collection |
| `getCollection(filter?)` | Fetch user's shows, respecting filter + media type |
| `exportUserData()` | Returns JSON blob → zipped for download |

---

## Saving & Business Rules

Enforced in server actions; client enforces the same rules optimistically for responsiveness.

| Trigger | Default Status | Default Interest |
|---|---|---|
| Set status explicitly | As chosen | As chosen |
| Rate an unsaved show | `Done` | — |
| Tag an unsaved show | `Later` | `Interested` |
| Interested chip | `Later` | `Interested` |
| Excited chip | `Later` | `Excited` |

**Removal flow:**
1. User re-selects their active status chip.
2. Confirmation dialog shown (unless `hideStatusRemovalConfirmation` is set).
3. On confirm: show deleted from DB; all My Data cleared.
4. After enough confirmations, "stop asking" option shown.

**Re-adding a removed show:** behaves as a fresh save (no prior data remains).

**Timestamps:** every `my_*` field has a companion `*_update_date` field updated on every write.

---

## App Structure & Navigation

```
┌─────────────────────────────────────────────┐
│  NavBar (persistent)                         │
│  [Collection]  [Find/Discover]  [Settings]   │
├───────────────┬─────────────────────────────┤
│ Filter        │  Main Content Area           │
│ Sidebar       │  ┌──────────────────────┐   │
│               │  │ All Shows            │   │
│ • All Shows   │  │ [Movies] [TV] [All]  │   │
│ • Tag filters │  │                      │   │
│ • No Tags     │  │ ── Active ──         │   │
│ • Genre       │  │ ── Excited ──        │   │
│ • Decade      │  │ ── Interested ──     │   │
│ • Score       │  │ ── [Others] ──       │   │
│               │  └──────────────────────┘   │
└───────────────┴─────────────────────────────┘
```

Find/Discover hub uses a clear mode switcher between Search, Ask, and Alchemy.

---

## Feature Implementation: Collection Home

**File:** `app/(home)/page.tsx`

- Fetch collection from Supabase, filtered by current `FilterConfiguration` + media type toggle.
- Status sections rendered in order: **Active** (prominent/larger tiles) → **Excited** → **Interested** → collapsed group (Wait, Done, Quit, unclassified Later).
- `ShowTile` component: poster image, title, in-collection badge (when `my_status` set), rating badge (when `my_score` set).
- Empty states: no collection → "Start by searching or asking"; filtered → "No results found."
- Last selected filter persisted in `ui_state` (local storage, recoverable).

---

## Feature Implementation: Search

**Files:** `app/find/search/page.tsx`, `server/api/catalog/search/route.ts`

- Live text search, no pre-loading; queries external catalog on input.
- Results in a poster grid.
- In-collection items visually marked by checking against user's collection.
- Selecting a result navigates to Show Detail.
- "Search on Launch" setting: if `autoSearch = true`, Find tab opens to Search automatically.

---

## Feature Implementation: Show Detail

**File:** `app/show/[id]/page.tsx`

Section order (per detail_page_experience.md):

1. **Header media carousel** — backdrops, posters, logos; inline trailer player when available; graceful fallback to poster-only layout.
2. **Core facts row** — year, runtime (movies) or seasons/episodes (TV), community score bar.
3. **My Tags chips** — tag display + tag picker modal.
4. **Overview + Scoop toggle** — overview text; toggle button label changes state: "Give me the scoop!" → "Show the scoop" (cached) → "The Scoop" (open + streaming).
5. **"Ask about this show" CTA** — navigates to Ask with show context seeded.
6. **Genres + languages.**
7. **Traditional recommendations strand** — horizontal scroll of similar/recommended shows.
8. **Explore Similar** — Get Concepts → chip selection → Explore Shows (AI recs).
9. **Streaming availability ("Stream It")** — providers by region.
10. **Cast strand → Crew strand** — horizontal; tapping opens Person Detail.
11. **Seasons accordion** (TV only).
12. **Budget / Revenue** (movies only, when data present).

**Status toolbar** (persistent, not in scroll body):
- Chips: Active, Interested, Excited, Done, Wait, Quit.
- Interested/Excited set `myStatus = Later` + respective `myInterest`.
- Re-selecting active chip triggers removal confirmation.

**My Rating bar:** adjustable 0–10; rating an unsaved show auto-saves as `Done`.

---

## Feature Implementation: AI Scoop

**Files:** `server/api/ai/scoop/route.ts`, `components/show/Scoop.tsx`

- POST `/api/ai/scoop` — accepts `showId`, returns Server-Sent Events stream.
- Server checks `ai_scoop_update_date`; serves cached text if age < 4 hours.
- Prompt produces a structured "mini blog post of taste":
  - Personal take (make a stand)
  - Honest stack-up vs reviews
  - **The Scoop** centerpiece paragraph (emotional, vivid, 60–120 words)
  - Fit/warnings ("perfect if… might not land if…")
  - "Worth it?" gut check
  - Target: 150–350 words total
- Streams progressively; UI shows "Generating…" during stream.
- Persists final text to DB only if show is in user's collection.
- Spoiler-safe by default; voice: gossipy, warm, opinionated.

---

## Feature Implementation: Ask Chat

**Files:** `app/find/ask/page.tsx`, `server/api/ai/ask/route.ts`

**Response format (structured):**
```
{
  commentary: string,          // user-facing response (no external IDs)
  showList: "Title::externalId::mediaType;;Title2::externalId::mediaType;;"
}
```

- `showList` parsed → catalog lookups → "Mentioned Shows" horizontal strip of real Show tiles.
- If catalog lookup fails for a title: shown non-interactive or Search handoff.
- If structured parse fails: retry once with stricter instructions; fall back to commentary-only + Search handoff.

**Context passed to AI:**
- User library summary: title, status, interest, tags, rating for each saved show.
- Last N conversation turns; older turns summarized to 1–2 sentences in the same persona voice (not sterile "system summary" tone). Auto-summarize after ~10 messages.

**Welcome screen:** 6 randomly selected starter prompts (from the product's 80-prompt pool); refresh button picks 6 new ones.

**"Ask about this show" path:** navigating from Show Detail seeds the first system message with show context (title, overview, genres, community score). Exact prefill behavior: inject as a hidden context message before the user's first visible turn.

**Voice:** conversational friend, not essay; confident picks; bulleted lists for multi-recs; 1–3 tight paragraphs + list.

---

## Feature Implementation: Alchemy

**Files:** `app/find/alchemy/page.tsx`, `server/api/ai/concepts/route.ts`, `server/api/ai/recs/alchemy/route.ts`

**Step flow:**

```
Step 1: Select ≥2 input shows
        (search library + global catalog)
        ↓
Step 2: "Conceptualize Shows"
        → POST /api/ai/concepts (multi-show mode)
        → concepts represent shared qualities across ALL inputs
        ↓
Step 3: Select concept chips (max 8)
        Selecting/unselecting clears downstream results
        ↓
Step 4: "ALCHEMIZE!"
        → POST /api/ai/recs/alchemy
        → returns 6 recs with reasons citing selected concepts
        ↓
Step 5: Review results; optionally "More Alchemy!"
        (results become new input shows → return to Step 1)
```

Backtracking: changing input shows clears concepts and results. Each step visually distinct (card/section layout).

---

## Feature Implementation: Explore Similar

**File:** `components/show/ExploreSimilar.tsx`

Embedded in Show Detail (section 8):

1. "Get Concepts" button → POST `/api/ai/concepts` (single-show mode, ~8 concepts).
2. Concept chips appear; user selects ≥1. Copy hints: "pick the ingredients you want more of."
3. "Explore Shows" button → POST `/api/ai/recs/explore` → 5 recs with per-concept reasons.
4. Each rec: resolve via catalog lookup by external ID → real Show tile. Non-resolvable shown non-interactive with Search handoff option.

---

## AI Prompts (`lib/ai/prompts/`)

### Shared rules (all surfaces)

- Stay within TV/movies domain; redirect if user strays.
- Spoiler-safe by default unless user explicitly requests.
- Opinionated and honest; acknowledge mixed reception.
- Specific vibe/structure/craft reasoning over genre summaries.

### `scoop.ts`

Structures output into labeled sections. Injects: show title, overview, genres, runtime/seasons, community score, tagline.

### `ask.ts`

Injects: user library summary + recent conversation turns + summarized older turns. Requests `{ commentary, showList }` JSON shape. Instructs: pick confidently, use bullets for multi-recs, stay concise.

### `concepts.ts`

Single-show mode: generates ~8 evocative 1–3-word concepts for that show.  
Multi-show mode: generates concepts representing shared qualities across ALL input shows.  
Output: bullet list only, no explanation, no generic entries ("good characters" invalid).

### `recs.ts`

Injects: selected concepts + user library. Requests N shows (5 for Explore Similar, 6 for Alchemy). Each rec must include: title, external catalog ID, media type, and a 1–3 sentence reason citing which concepts it matches.

### `summarize.ts`

Summarizes older conversation turns into 1–2 sentences, written in the same warm/casual persona voice as Ask.

---

## Feature Implementation: Person Detail

**File:** `app/person/[id]/page.tsx`

- Header: image gallery, name, biography.
- Analytics charts (lightweight, rendered client-side):
  - Average project rating by year (bar or line chart)
  - Top genres by credit count (horizontal bar)
  - Projects per year (histogram)
- Filmography: credits grouped by year, descending. Each credit is a tappable tile → Show Detail.

---

## Feature Implementation: Settings

**File:** `app/settings/page.tsx`

| Section | Fields |
|---|---|
| App | Font size (XS/S/M/L/XL/XXL), Search on Launch toggle |
| User | Username (synced via `cloud_settings`) |
| AI | API key, model selection (env var takes precedence in benchmark mode) |
| Integrations | Catalog API key |
| Your Data | Export My Data button → `.zip` of JSON |

**Data Export (`server/api/export/route.ts`):**
- Fetch all `shows` for `(namespace_id, user_id)`.
- Serialize to JSON with all My Data fields; dates in ISO-8601.
- Zip and return as `Content-Type: application/zip`.

**Credential handling:** AI and catalog API keys entered in Settings UI are stored in `cloud_settings` (server-side). In benchmark mode, env vars (`AI_API_KEY`, `CATALOG_API_KEY`) override any stored value. Keys are never committed to the repo.

---

## Concept Quality Constraints (from discovery_quality_bar.md)

For all concept generation:

| Requirement | Rule |
|---|---|
| Count | 8 concepts per request by default |
| Format | 1–3 words, evocative, no explanation |
| Specificity | "hopeful absurdity" valid; "good characters" invalid |
| Diversity | Cover different axes: structure, vibe, emotion, craft |
| Order | Best "aha" concepts first |
| Multi-show | Must be shared across all input shows |

For recommendations:

| Surface | Count | Reason requirement |
|---|---|---|
| Explore Similar | 5 recs | Name which concept(s) each rec matches |
| Alchemy | 6 recs | Name which concept(s) each rec matches |
| Real-show integrity | Non-negotiable | Every rec must resolve to a real catalog item via valid external ID |

---

## Data Continuity & Migration

- `data_model_version` tracked in `app_metadata` per namespace.
- Migrations are additive only (new nullable columns + defaults).
- On app start, check `data_model_version`; run any pending forward migrations automatically.
- User library never lost across updates; collection, ratings, tags, statuses, interest, and AI Scoop all preserved.

---

## Infra Scripts

### `npm run dev`
Standard `next dev`.

### `npm test`
Test runner (e.g., Vitest or Jest). Tests use a dedicated `NAMESPACE_ID` (e.g., `test-<uuid>`) and `DEV_USER_ID`. Tests are self-contained and leave `is_test = true` on any rows they create.

### `npm run test:reset`
Deletes all rows in `shows` where `namespace_id = $NAMESPACE_ID`. Does not require global DB teardown. Script reads `NAMESPACE_ID` from env.

---

## Cross-Cutting Rules Summary

1. **User's version wins everywhere** — My Data always takes precedence over refreshed public data.
2. **Discovery is always actionable** — every AI recommendation must resolve to a real, selectable Show.
3. **Taste-aware AI** — Ask, Alchemy, and Explore Similar inject user library + My Data as context.
4. **Spoiler-safe by default** — no plot specifics unless user explicitly asks.
5. **Implicit behaviors feel natural** — auto-save rules applied consistently; no surprising data loss.
6. **Your data is yours** — export is first-class (zip + JSON, ISO-8601 dates).
7. **Identity is explicit** — every user-owned record scoped to `(namespace_id, user_id)`; ready for OAuth without schema redesign.
8. **Backend is source of truth** — client cache is disposable; clearing it never loses user data.
9. **Runs are isolated** — two different `namespace_id` values never read/write each other's data.

---

## Open Questions (Not Blocking, Per PRD)

- Should `Next` become a first-class UI status chip?
- Should named custom lists (beyond free-form tags) be added?
- Should generating AI Scoop on an unsaved show implicitly save it?
- Should clearing My Rating store an explicit `Unrated` vs `null`?
- Should Import/Restore from export zip be added to Settings?
- Should Alchemy sessions be saveable/shareable as reusable "blends"?
- Should sidebar show explicit `myStatus` filter entries?
