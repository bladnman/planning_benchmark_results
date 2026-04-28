# Implementation Plan: Personal TV + Movie Companion App

## Document Summary

This plan is based on complete reading of:
- `docs/prd/product_prd.md` — Product requirements (what the app does)
- `docs/prd/infra_rider_prd.md` — Infrastructure requirements (how it must run)
- `docs/prd/supporting_docs/ai_prompting_context.md` — AI behavioral contracts
- `docs/prd/supporting_docs/ai_voice_personality.md` — AI persona + voice spec
- `docs/prd/supporting_docs/concept_system.md` — Concept system for discovery
- `docs/prd/supporting_docs/detail_page_experience.md` — Detail page UX spec
- `docs/prd/supporting_docs/discovery_quality_bar.md` — Discovery acceptance criteria
- `docs/prd/supporting_docs/technical_docs/storage-schema.md` — Persistent data schema
- `docs/prd/supporting_docs/technical_docs/storage-schema.ts` — Schema type definitions

---

## Technology Stack (Mandated)

| Layer | Choice | Rationale |
|---|---|---|
| App runtime | **Next.js (latest stable)** | Mandated by infra rider |
| Persistence | **Supabase (hosted)** | Mandated by infra rider; cloud-agent compatible |
| Language | **TypeScript** | Type safety; schema file already in TS |
| Auth (bench) | Dev identity injection | No real OAuth required for benchmark |
| Catalog | **TMDB API** | Industry-standard, provides external IDs for AI resolution |
| AI provider | Configurable via env | Key + model injected via environment |

Docker is explicitly **not** required. All tools must work with a hosted Supabase instance.

---

## Architectural Decisions

### 1. Next.js App Router
Use the App Router (`/app` directory) with Server Components for catalog-heavy pages and Client Components for interactive UI (status chips, rating, AI chat). API routes live in `/app/api/`.

### 2. Identity & Namespace Model
Every request carries two identifiers:
- `namespace_id` — stable per-build/run; injected via `NAMESPACE_ID` env var (UUID default)
- `user_id` — stable per user; injected via dev header `X-User-Id` or env var `DEV_USER_ID` in development; production replaces with real OAuth without schema changes

All Supabase queries include a `WHERE namespace_id = $1 AND user_id = $2` predicate. This is the `(namespace_id, user_id)` partition from the infra rider.

### 3. Backend-as-Source-of-Truth
All user-owned data lives in Supabase. Client may hold an in-memory cache (React Query / SWR) but clearing it never loses data. The app uses optimistic updates with rollback on failure.

### 4. AI Provider Abstraction
A thin `AIClient` module wraps the configured provider (key + model from env/settings). Swapping provider means swapping the client implementation, not the feature logic.

### 5. Catalog Integration
TMDB is the external catalog. A `CatalogClient` module wraps TMDB calls. The `Show` merge logic is isolated in a `mergeCatalogShow(storedShow, freshCatalogShow)` function that enforces `selectFirstNonEmpty` for catalog fields and timestamp-based resolution for `my*` fields.

---

## Repository Structure

```
/
├── app/
│   ├── layout.tsx                  # Root layout, providers
│   ├── page.tsx                    # Collection Home (/)
│   ├── find/
│   │   ├── page.tsx                # Find/Discover hub (Search default)
│   │   ├── ask/page.tsx            # Ask (chat)
│   │   └── alchemy/page.tsx        # Alchemy
│   ├── show/[id]/page.tsx          # Show Detail
│   ├── person/[id]/page.tsx        # Person Detail
│   ├── settings/page.tsx           # Settings
│   └── api/
│       ├── shows/                  # Collection CRUD
│       ├── catalog/                # TMDB proxy endpoints
│       ├── ai/
│       │   ├── scoop/              # AI Scoop generation
│       │   ├── ask/                # Ask chat (streaming)
│       │   ├── concepts/           # Concept generation
│       │   └── recommendations/    # Concept-based recs
│       └── settings/               # User/cloud settings
├── lib/
│   ├── supabase/
│   │   ├── client.ts               # Browser client (anon key)
│   │   ├── server.ts               # Server client (service key, server-only)
│   │   └── middleware.ts           # Auth middleware + namespace injection
│   ├── catalog/
│   │   ├── tmdb.ts                 # TMDB API client
│   │   ├── mapper.ts               # Catalog response → Show
│   │   └── merge.ts                # mergeCatalogShow() + merge rules
│   ├── ai/
│   │   ├── client.ts               # AI provider wrapper
│   │   ├── prompts/                # Prompt templates per surface
│   │   │   ├── scoop.ts
│   │   │   ├── ask.ts
│   │   │   ├── concepts.ts
│   │   │   └── recommendations.ts
│   │   └── parsers.ts              # showList string parser, structured output
│   ├── store/
│   │   ├── collection.ts           # Collection state + hooks
│   │   └── settings.ts             # Settings state + hooks
│   └── utils/
│       ├── identity.ts             # namespace_id + user_id resolution
│       ├── timestamps.ts           # ISO-8601 helpers
│       └── export.ts               # ZIP export builder
├── components/
│   ├── layout/
│   │   ├── AppShell.tsx            # Sidebar + content area
│   │   ├── FilterPanel.tsx         # Left sidebar filters
│   │   └── PrimaryNav.tsx          # Top-level nav
│   ├── collection/
│   │   ├── CollectionHome.tsx
│   │   ├── StatusGroup.tsx         # Active / Excited / Interested / Other sections
│   │   ├── ShowTile.tsx            # Poster + badges
│   │   └── MediaTypeToggle.tsx
│   ├── detail/
│   │   ├── DetailPage.tsx
│   │   ├── HeaderCarousel.tsx
│   │   ├── StatusChips.tsx         # Toolbar chips (Active/Interested/Excited/Done/Wait/Quit)
│   │   ├── RatingBar.tsx
│   │   ├── TagPicker.tsx
│   │   ├── ScoopPanel.tsx          # Scoop toggle + stream render
│   │   ├── RecommendationsStrand.tsx
│   │   ├── ExploreSimilar.tsx      # Get Concepts → select → Explore Shows
│   │   ├── CastCrewStrand.tsx
│   │   ├── SeasonsPanel.tsx
│   │   └── BudgetRevenue.tsx
│   ├── find/
│   │   ├── FindHub.tsx             # Mode switcher wrapper
│   │   ├── SearchMode.tsx
│   │   ├── AskMode.tsx             # Chat UI
│   │   ├── AlchemyMode.tsx         # Multi-step alchemy flow
│   │   └── MentionedShowsStrip.tsx # Horizontal mentioned shows strip
│   ├── person/
│   │   └── PersonDetail.tsx
│   └── shared/
│       ├── ShowGrid.tsx
│       ├── ConceptChips.tsx
│       └── StatusRemovalModal.tsx
├── supabase/
│   ├── migrations/                 # SQL migration files
│   └── seed.ts                     # Optional dev seed
├── scripts/
│   ├── reset-namespace.ts          # npm run test:reset
│   └── export-data.ts
├── .env.example
├── .gitignore
└── package.json
```

---

## Phase 1: Project Bootstrapping

### 1.1 Initialize Next.js Project
```
npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir=false
```
- Tailwind CSS for styling (utility-first, no design system lock-in)
- ESLint + Prettier configured

### 1.2 Environment Variables
**`.env.example`** (all required vars with comments):
```
# Supabase
NEXT_PUBLIC_SUPABASE_URL=        # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=   # Public anon key (browser-safe)
SUPABASE_SERVICE_ROLE_KEY=       # Server-only elevated key

# Identity (benchmark mode)
NAMESPACE_ID=                    # Stable UUID for this build/run
DEV_USER_ID=                     # Fixed user UUID in dev/test

# Catalog
TMDB_API_KEY=                    # TMDB v3 API key (server-only)

# AI
AI_API_KEY=                      # AI provider key (server-only)
AI_MODEL=                        # Model name/key e.g. "claude-opus-4-7"

# App
NODE_ENV=development
```

**`.gitignore`** additions:
```
.env
.env.local
.env.*.local
```

### 1.3 npm Scripts
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "test": "jest --passWithNoTests",
    "test:reset": "tsx scripts/reset-namespace.ts"
  }
}
```

### 1.4 Supabase Client Setup
- **`lib/supabase/client.ts`**: browser client using `NEXT_PUBLIC_SUPABASE_URL` + anon key
- **`lib/supabase/server.ts`**: server-only client using service role key (imported only in Server Components + API routes via `server-only` package)

---

## Phase 2: Database Schema & Migrations

### 2.1 Migration Files

**Migration 001: core tables**

```sql
-- Namespace isolation extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Shows (user collection)
CREATE TABLE shows (
  id              TEXT NOT NULL,
  namespace_id    TEXT NOT NULL,
  user_id         TEXT NOT NULL,

  -- Identity
  title           TEXT NOT NULL,
  show_type       TEXT NOT NULL CHECK (show_type IN ('movie', 'tv', 'person', 'unknown')),
  external_ids    JSONB,

  -- Catalog meta
  overview        TEXT,
  genres          TEXT[] NOT NULL DEFAULT '{}',
  tagline         TEXT,
  homepage        TEXT,
  original_language TEXT,
  spoken_languages TEXT[] NOT NULL DEFAULT '{}',
  languages       TEXT[] NOT NULL DEFAULT '{}',

  -- Images
  poster_url      TEXT,
  backdrop_url    TEXT,
  logo_url        TEXT,
  network_logos   TEXT[] NOT NULL DEFAULT '{}',

  -- Ratings
  vote_average    DOUBLE PRECISION,
  vote_count      INTEGER,
  popularity      DOUBLE PRECISION,

  -- Dates (ISO-8601)
  last_air_date   TEXT,
  first_air_date  TEXT,
  release_date    TEXT,

  -- Movie-specific
  runtime         INTEGER,
  budget          BIGINT,
  revenue         BIGINT,

  -- TV-specific
  series_status        TEXT,
  number_of_episodes   INTEGER,
  number_of_seasons    INTEGER,
  episode_run_time     INTEGER[] NOT NULL DEFAULT '{}',

  -- My Data (user overlay)
  my_tags             TEXT[] NOT NULL DEFAULT '{}',
  my_tags_update_date TEXT,
  my_score            DOUBLE PRECISION,
  my_score_update_date TEXT,
  my_status           TEXT CHECK (my_status IN ('active','next','later','done','quit','wait')),
  my_status_update_date TEXT,
  my_interest         TEXT CHECK (my_interest IN ('excited','interested')),
  my_interest_update_date TEXT,

  -- AI data
  ai_scoop            TEXT,
  ai_scoop_update_date TEXT,

  -- Management
  details_update_date TEXT,
  creation_date       TEXT NOT NULL DEFAULT (to_char(NOW() AT TIME ZONE 'UTC', 'YYYY-MM-DD"T"HH24:MI:SS"Z"')),
  is_test             BOOLEAN NOT NULL DEFAULT FALSE,

  -- Providers (opaque blob)
  provider_data       JSONB,

  PRIMARY KEY (id, namespace_id, user_id)
);

CREATE INDEX shows_namespace_user ON shows (namespace_id, user_id);
CREATE INDEX shows_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX shows_tags ON shows USING GIN (my_tags);
```

**Migration 002: settings & metadata**

```sql
-- Cloud settings (per user, syncable)
CREATE TABLE cloud_settings (
  id              TEXT NOT NULL DEFAULT 'globalSettings',
  namespace_id    TEXT NOT NULL,
  user_id         TEXT NOT NULL,
  user_name       TEXT NOT NULL DEFAULT '',
  version         DOUBLE PRECISION NOT NULL DEFAULT 0,
  catalog_api_key TEXT,
  ai_api_key      TEXT,
  ai_model        TEXT NOT NULL DEFAULT '',
  PRIMARY KEY (id, namespace_id, user_id)
);

-- App metadata (per namespace+user, for migration tracking)
CREATE TABLE app_metadata (
  namespace_id        TEXT NOT NULL,
  user_id             TEXT NOT NULL,
  data_model_version  INTEGER NOT NULL DEFAULT 3,
  PRIMARY KEY (namespace_id, user_id)
);
```

**Migration 003: Row Level Security**

```sql
ALTER TABLE shows ENABLE ROW LEVEL SECURITY;
ALTER TABLE cloud_settings ENABLE ROW LEVEL SECURITY;
ALTER TABLE app_metadata ENABLE ROW LEVEL SECURITY;

-- Service role bypasses RLS (used in server-side API routes)
-- Anon key users get no direct access — all reads/writes go through API routes
-- (or optionally add user-auth based RLS if Supabase Auth is wired later)
```

> RLS policy strategy: In benchmark mode, all DB access goes through server-side API routes using the service role key. The anon key is only used for health checks. This means the RLS policies start permissive for the service role and can be tightened when real OAuth is added — no schema redesign required.

### 2.2 Local Settings (non-Supabase)
Local settings (`autoSearch`, `fontSize`) and UI state (`hideStatusRemovalConfirmation`, `lastSelectedFilter`) are stored in browser `localStorage` — these are ephemeral UI preferences, not user-owned data, so they are exempt from the server-as-source-of-truth rule.

### 2.3 test:reset Script
`scripts/reset-namespace.ts`:
```typescript
// Deletes all rows WHERE namespace_id = process.env.NAMESPACE_ID
// Uses service role key. Never touches other namespaces.
```
Called via `npm run test:reset`. Safe to run repeatedly; does not require global teardown.

---

## Phase 3: Identity & Dev Auth Injection

### 3.1 `lib/utils/identity.ts`
```typescript
// Reads NAMESPACE_ID from process.env (server) or falls back to a default
// Reads DEV_USER_ID from process.env or X-User-Id request header (dev only)
// Returns { namespaceId: string, userId: string }
```

### 3.2 Middleware (`lib/supabase/middleware.ts`)
- In `development`/`test`: accept `X-User-Id` header or use `DEV_USER_ID` env var
- In `production`: validate real auth token (placeholder; does not break schema)
- Attach `{ namespaceId, userId }` to request context for all API routes

This is the only place that resolves identity. Feature code never reads raw headers.

### 3.3 Production Migration Path
When real OAuth is added:
1. Wire Supabase Auth or external provider
2. Map `auth.user.id` → `user_id` in middleware
3. Remove `X-User-Id` header acceptance for non-development environments
4. No schema changes needed — `user_id` column is already an opaque string

---

## Phase 4: External Catalog Integration

### 4.1 TMDB API Client (`lib/catalog/tmdb.ts`)
Server-only module (never imported in browser code).

**Endpoints used:**
- `GET /search/multi?query=` — multi-type search
- `GET /movie/{id}?append_to_response=credits,videos,recommendations,similar,images,watch/providers`
- `GET /tv/{id}?append_to_response=credits,videos,recommendations,similar,images,watch/providers,seasons`
- `GET /person/{id}?append_to_response=combined_credits,images`
- `GET /configuration` — image base URL

**Key decisions:**
- Use `append_to_response` to minimize round trips for Detail page
- Image base URL cached in memory (config changes rarely)
- Logo selection: choose first English logo; fallback to first logo of any language

### 4.2 Show Mapper (`lib/catalog/mapper.ts`)
Converts TMDB response → `Show` TypeScript interface:
- Title: `movie.title` or `tv.name`; fail if neither exists
- `showType`: infer from `media_type` field or presence of `name`/`title`
- Genres: map `genre_ids` or `genres` array to display names using TMDB genre map
- Images: construct full URLs from `posterPath`, `backdropPath`, best logo
- Transient fields (`cast`, `crew`, `seasons`, `videos`, `recommendations`, `similar`, `images`): decoded and attached as transient properties for UI, **not stored in DB**

### 4.3 Merge Logic (`lib/catalog/merge.ts`)
```typescript
function mergeCatalogShow(stored: Show, fresh: Show): Show {
  // Non-my fields: selectFirstNonEmpty(fresh, stored)
  // my* fields: compare update timestamps, keep newer
  // detailsUpdateDate: set to now
  // creationDate: always preserve stored value
}
```

This function is pure and independently testable. It's called whenever catalog data is fetched for a show already in the user's collection.

---

## Phase 5: Collection API Routes

### 5.1 Show CRUD
```
GET    /api/shows                  # List user's collection (with filters)
GET    /api/shows/[id]             # Get single show (or 404)
PUT    /api/shows/[id]             # Upsert show (creates or merges)
DELETE /api/shows/[id]             # Remove from collection (clears all my* data)
PATCH  /api/shows/[id]/status      # Update myStatus + myInterest
PATCH  /api/shows/[id]/rating      # Update myScore
PATCH  /api/shows/[id]/tags        # Update myTags
```

**Upsert behavior:**
1. Fetch fresh catalog data for the show (optional, caller can pass `refresh: false`)
2. If show exists in DB: `mergeCatalogShow(stored, fresh)` then update
3. If show does not exist: insert with `creationDate = now`

### 5.2 Auto-Save Rules (enforced server-side)
When any `PATCH` endpoint receives a field update for a show **not** in the collection:
- Rating update → auto-save with `myStatus = 'done'`
- Tag update → auto-save with `myStatus = 'later'`, `myInterest = 'interested'`
- Status/Interest update → auto-save with provided values

### 5.3 Default Values
When saving without explicit status:
- `myStatus = 'later'`, `myInterest = 'interested'`
- Exception: first save via rating → `myStatus = 'done'`

### 5.4 Timestamps
All `PATCH` routes set the corresponding `*_update_date` field to `new Date().toISOString()`.

---

## Phase 6: App Shell & Navigation

### 6.1 Root Layout
`app/layout.tsx`:
- Providers wrapper (React Query, settings context)
- `<AppShell>` — renders sidebar + main content
- Font size class applied from local settings

### 6.2 Filter Panel (Sidebar)
Sections:
1. **All Shows** (default)
2. **My Tags** — one entry per distinct tag in collection; "No tags" if any tagless shows
3. **Data Filters**:
   - Genre (one per genre in collection)
   - Decade
   - Community Score ranges
4. **Media Type Toggle** — All / Movies / TV (applies on top of any filter)

Filter state is persisted to `localStorage` as `lastSelectedFilter` (JSON-encoded `FilterConfiguration`).

### 6.3 Primary Navigation
Persistent entries:
- **Library** (home `/`)
- **Find/Discover** (`/find`)
- **Settings** (`/settings`)

---

## Phase 7: Collection Home

### 7.1 Status Grouping
Shows are grouped and displayed in this order:
1. **Active** — larger tiles, prominent section
2. **Excited** — `myStatus = 'later'` AND `myInterest = 'excited'`
3. **Interested** — `myStatus = 'later'` AND `myInterest = 'interested'`
4. **Other** — collapsed group containing: Wait, Quit, Done, any unclassified Later

Filter parameter reduces the show pool before grouping. Grouping happens client-side from the API response.

### 7.2 Show Tile
- Poster image (with lazy loading)
- Title
- **In-collection badge** when `myStatus` is set
- **User rating badge** when `myScore` is set
- Tap → Show Detail

### 7.3 Empty States
- No shows in collection → "Start building your library" with CTA to Search/Ask
- Filter yields no results → "No results found"

---

## Phase 8: Show Detail Page

Follows the narrative hierarchy from `detail_page_experience.md` exactly.

### 8.1 Section Order
1. **Header Media Carousel** — backdrops/posters/logos/trailers; graceful fallback to poster only
2. **Core Facts Row** — year, runtime (movies) or seasons/episodes (TV), community score bar
3. **Status/Interest Toolbar** (sticky or near top, not in scroll body)
   - Chips: Active, Interested, Excited, Done, Wait, Quit
   - Interested/Excited set `myStatus = 'later'` + appropriate `myInterest`
   - Reselecting active chip → `StatusRemovalModal` confirmation → clears all My Data on confirm
4. **My Rating Bar** — 0–10 slider; rating unsaved show auto-saves as Done
5. **My Tags** — tag chips + picker; adding tag to unsaved show auto-saves as Later + Interested
6. **Overview** — plain text; full-text with expand for long overviews
7. **AI Scoop Panel** — toggle-driven; see Section 8.2
8. **"Ask About This Show" CTA** — navigates to `/find/ask?showId=[id]`
9. **Genres + Languages**
10. **Traditional Recommendations Strand** — horizontal scroll of `recommendations`/`similar`
11. **Explore Similar** — concept-driven discovery; see Section 8.3
12. **Streaming Availability** — provider cards by region
13. **Cast Strand** + **Crew Strand** — tap → Person Detail
14. **Seasons Panel** (TV only) — season list with episode counts
15. **Budget vs Revenue** (movies where budget/revenue > 0)

### 8.2 AI Scoop Panel (`ScoopPanel.tsx`)
States:
- `no_scoop`: shows "Give me the scoop!" button
- `cached_scoop`: shows "Show the scoop" button (scoop exists but hidden)
- `open`: shows "The Scoop" header + streamed content

Logic:
1. On "Give me the scoop!": call `/api/ai/scoop?showId=[id]`
2. Stream response using `ReadableStream` / `useChat`-style hook
3. Show "Generating…" while streaming
4. On complete: if show is in collection, persist scoop text + `aiScoopUpdateDate` to DB
5. Freshness: if `aiScoopUpdateDate` is older than 4 hours → allow regeneration; otherwise serve cached
6. Unsaved show: scoop generated but not persisted (ephemeral until user saves)

### 8.3 Explore Similar (`ExploreSimilar.tsx`)
State machine:
```
idle → loading_concepts → concepts_ready → recs_loading → recs_ready
                                        ↑ (deselect concept clears recs)
```
1. "Get Concepts" → `POST /api/ai/concepts` with `{ showId, showType: 'single' }`
2. Concepts rendered as selectable chips; user picks 1+
3. "Explore Shows" → `POST /api/ai/recommendations` with `{ concepts: [...], sourceShows: [id] }`
4. Returns 5 show objects with reasons; resolved to real catalog items
5. Each result is a tappable ShowTile + inline "reason" text

---

## Phase 9: Find/Discover Hub

### 9.1 Mode Switcher
`/find` renders `<FindHub>` with three tabs: **Search | Ask | Alchemy**
Default tab: Search (or stored preference).

### 9.2 Search Mode
- Text input → debounced call to `/api/catalog/search?q=[query]`
- Results: poster grid with in-collection indicator (badge overlay)
- Tap → Show Detail
- Auto-open on launch if `autoSearch = true` (focus input immediately)

### 9.3 Ask Mode
Chat UI flow:
1. Welcome view: 6 randomly selected starter prompts (from a 80-prompt pool); "Refresh" shuffles
2. User types → `POST /api/ai/ask` (streaming)
3. Response streams as assistant turn; `MentionedShowsStrip` updates as shows are parsed
4. Session context: last ~10 turns kept verbatim; older turns summarized into 1–2 sentences by AI
5. "Reset" clears session; "Ask About [Show]" seeds context with show data

**Structured Output (Mentioned Shows):**
API returns:
```json
{
  "commentary": "...(user-facing text)...",
  "showList": "Title::externalId::mediaType;;Title2::externalId::mediaType"
}
```
Parser splits on `;;`, then splits each entry on `::`. Invalid format → retry once with stricter instruction → fall back to commentary only + Search handoff.

**Ask About a Show:**
Launched from `/find/ask?showId=[id]`:
- Fetches show data server-side
- Seeds system context with show name, genres, overview
- First message implied: "Tell me about [Show Title]"

### 9.4 Alchemy Mode
Multi-step UI with clear step progression:

**Step 1: Select Shows (2+ required)**
- Search field to find shows (library + global catalog)
- Selected shows shown as removable chips
- "Conceptualize Shows" button enabled when ≥2 shows selected
- Changing selected shows clears concepts and results downstream

**Step 2: Concepts**
- `POST /api/ai/concepts` with `{ showIds: [...], showType: 'multi' }`
- Returns larger pool of concepts (8+ options)
- User selects up to 8 concepts
- Changing selection clears recommendations

**Step 3: Alchemize**
- "ALCHEMIZE!" button → `POST /api/ai/recommendations` with `{ concepts, sourceShows, mode: 'alchemy' }`
- Returns 6 recommended shows with reasons referencing selected concepts
- Results shown as tiles with reason text

**Step 4: Chain (More Alchemy!)**
- "More Alchemy!" button uses results as new input shows
- Clears old concepts/results, pre-populates Step 1 with result shows

---

## Phase 10: AI API Routes

### 10.1 `/api/ai/scoop`
- Input: `{ showId, title, overview, genres, releaseDate, showType }`
- Output: streaming text (Server-Sent Events or chunked transfer)
- Prompt: personality-driven "mini blog post of taste" (~150–350 words)
  - Sections: personal take, honest stack-up, The Scoop paragraph (centerpiece), fit/warnings, "Worth it?" verdict
- Spoiler-safe by default
- Tone: gossipy, vivid, trusted friend

### 10.2 `/api/ai/ask`
- Input: `{ messages: ConversationTurn[], userLibrary: LibrarySummary, showContext?: Show }`
- Output: streaming JSON with `{ commentary: string, showList: string }`
- Context management: client sends last 10 turns; if turn count > 10, client requests a summary first
- Summarization endpoint: `/api/ai/ask/summarize` → condenses older turns into 1–2 sentences in same persona

### 10.3 `/api/ai/concepts`
- Input: `{ shows: Show[], mode: 'single' | 'multi' }`
- Output: `{ concepts: string[] }` — bullet list of 1–3 word evocative concepts
- Single-show: 8 concepts; Multi-show: 8+ options (user still caps selection at 8 in UI)
- Quality constraints: no generic placeholders, ordered by strength, varied across axes

### 10.4 `/api/ai/recommendations`
- Input: `{ concepts: string[], sourceShows: Show[], mode: 'explore' | 'alchemy', userLibrary?: LibrarySummary }`
- Output: `{ recommendations: RecommendedShow[] }` where each item has: `title`, `externalId`, `mediaType`, `reason`
- Counts: 5 for explore, 6 for alchemy
- After AI response: resolve each recommendation via `GET /api/catalog/resolve?externalId=&title=`
- Unresolvable items: shown non-interactive with Search handoff link

### 10.5 AI Prompt Architecture
Each surface has a dedicated prompt template in `lib/ai/prompts/`. All share:
- A shared persona header ("fun, chatty TV/movie nerd friend")
- Spoiler-safe rule
- Opinionated honesty rule
- Domain restriction (TV/movies only)
- Surface-specific instructions appended

The persona header is never duplicated inline — imported from a single `lib/ai/prompts/base.ts`.

---

## Phase 11: Person Detail Page

Data source: `GET /api/catalog/person/[id]` (TMDB person endpoint with `combined_credits,images`).

Sections:
1. **Profile image + name + bio** — image gallery (multi-image carousel if multiple photos)
2. **Analytics Charts** (client-side, lightweight charting library):
   - Average rating of their projects (from community scores)
   - Top genres (bar chart)
   - Projects by year (line/bar chart)
3. **Filmography** — credits grouped by year (descending), each item is a tappable `ShowTile`

Person data is **not persisted** — always fetched fresh. Tapping a credit opens Show Detail.

---

## Phase 12: Settings Page

### 12.1 App Settings (localStorage)
- **Font size**: XS / S / M / L / XL / XXL (stored as `fontSize` in localStorage)
- **Search on launch**: toggle (stored as `autoSearch` in localStorage)

### 12.2 User Settings (cloud_settings table)
- **Username**: text input; saved on blur; synced via `/api/settings`
- **AI API Key**: text input (masked); note: key entered here stored in cloud_settings; key from env var takes precedence in dev mode; never committed to repo
- **AI Model**: select/text input for model name
- **Catalog API Key**: text input (masked)

### 12.3 Export My Data
- "Export My Data" button → calls `/api/export`
- Server builds a ZIP containing:
  - `shows.json` — all shows in collection with all My Data fields, dates encoded ISO-8601
  - `settings.json` — cloud settings (excluding secrets)
- ZIP served as file download with `Content-Disposition: attachment; filename="my-data-[date].zip"`
- Library: `jszip` or `archiver` on the server side

### 12.4 Import/Restore
Noted as "desired but not currently implemented" in the PRD. Settings page shows a placeholder "Import My Data (coming soon)" disabled button.

---

## Phase 13: Data Continuity & Migrations

### 13.1 Migration Tracking
`app_metadata` table has `data_model_version` (default 3). On app startup (middleware or root layout Server Component):
1. Fetch current `data_model_version` for `(namespace_id, user_id)`
2. If lower than current code version, run incremental migration functions
3. Migration functions are idempotent and additive (no destructive schema changes)

### 13.2 Migration Strategy
- Forward-only migrations in `supabase/migrations/`
- Named by timestamp: `001_core_tables.sql`, `002_settings.sql`, etc.
- Supabase CLI manages migration application
- Schema changes that could break old data are always additive + backfill, never drop

---

## Phase 14: Cross-Cutting Concerns

### 14.1 User Overlay Display Rule
Every place a show appears (list, search result, AI recommendation, person filmography):
1. Check if show exists in user's collection (by `id`)
2. If yes: display with `myStatus`, `myTags`, `myScore` badges
3. If no: display as standard catalog item

Implementation: collection is loaded into memory (React Query cache) on first load. Tile components receive the show's stored data (or null) as a prop.

### 14.2 Status Removal Confirmation
`StatusRemovalModal`:
- First N times (tracked in `statusRemovalCountKey` localStorage): show full confirmation warning
- After user clicks "Don't ask again" (`hideStatusRemovalConfirmation = true`): skip modal, execute removal directly
- Destructive: clears all My Data server-side in the DELETE endpoint

### 14.3 AI Recommendations → Real Shows Resolution
After AI returns recommendations:
```
1. For each rec with externalId → GET /api/catalog/resolve?externalId=[id]
2. Catalog returns show data if found (title match case-insensitive)
3. If found → render as real tappable ShowTile with reason text overlay
4. If not found → render as non-interactive text + "Search for [title]" link
```

### 14.4 Error Handling
- Catalog API failures: show skeleton state → error message with retry
- AI failures: show error inline in chat/panel; retry button
- Structured output parse failure: retry with stricter prompt once; fallback to unstructured
- Network errors: client-side retry with exponential backoff (React Query defaults)

---

## Phase 15: Testing Infrastructure

### 15.1 Test Setup
- **Jest** + **React Testing Library** for unit/component tests
- **Supertest** for API route integration tests
- Test environment uses `NAMESPACE_ID=test-[uuid]` and `DEV_USER_ID=test-user-001`

### 15.2 Test Isolation
- All test data is created with `is_test = true` and scoped to test namespace
- `npm run test:reset` calls `scripts/reset-namespace.ts`:
  ```typescript
  // DELETE FROM shows WHERE namespace_id = process.env.NAMESPACE_ID
  // DELETE FROM cloud_settings WHERE namespace_id = process.env.NAMESPACE_ID
  // DELETE FROM app_metadata WHERE namespace_id = process.env.NAMESPACE_ID
  ```
- Tests never touch `namespace_id = 'production'` or any other build's namespace

### 15.3 Key Test Scenarios
- Auto-save rules: rating unsaved show → verify `myStatus = 'done'` in DB
- Tag unsaved show → verify `myStatus = 'later'`, `myInterest = 'interested'`
- Removal → verify all My Data cleared
- Merge logic: catalog refresh preserves newer user edits
- AI structured output parser: valid input, invalid format, partial parse

---

## Implementation Order (Suggested Phases)

| Phase | Deliverable | Depends On |
|---|---|---|
| 1 | Project bootstrap, .env.example, Supabase client setup | — |
| 2 | DB migrations (shows, settings, metadata tables) | Phase 1 |
| 3 | Identity injection middleware, dev auth | Phase 2 |
| 4 | TMDB catalog client + mapper + merge logic | Phase 3 |
| 5 | Collection API routes (CRUD, auto-save rules) | Phases 3, 4 |
| 6 | App shell, navigation, filter panel | Phase 1 |
| 7 | Collection Home (status groups, tiles, filters) | Phases 5, 6 |
| 8 | Show Detail page (all sections, save behaviors) | Phases 5, 6 |
| 9 | Find/Discover hub: Search mode | Phases 4, 6 |
| 10 | AI routes: Scoop, Ask, Concepts, Recommendations | Phase 3 |
| 11 | Find/Discover hub: Ask mode + Alchemy mode | Phases 9, 10 |
| 12 | Explore Similar on Detail page | Phases 8, 10 |
| 13 | Person Detail page | Phases 6, 9 |
| 14 | Settings page + Export | Phase 5 |
| 15 | Test infrastructure + reset script | Phase 2 |
| 16 | Migration tracking + data continuity | Phase 2 |

---

## Key Constraints & Risk Areas

### Critical Business Rules (easy to miss)
1. **Interested/Excited are interest levels, not statuses** — they set `myStatus = 'later'` plus the interest field. The UI shows them as primary chips but the data model has two fields.
2. **Removing last status clears all My Data** — not just the status. The server must clear all `my*` fields atomically.
3. **User overlay display rule is universal** — applies even in AI recommendation results, person filmography, and alchemy results.
4. **AI Scoop only persists if show is in collection** — must check collection membership at persist time, not at generation time.
5. **Alchemy concepts must be shared across ALL input shows** — the multi-show concept prompt must explicitly enforce this.

### Infrastructure Risks
1. **TMDB rate limits** — server-side caching of catalog responses (at minimum, cache `details_update_date` and skip re-fetch if recent)
2. **AI streaming in Next.js** — use `Response` with `ReadableStream` and `TransformStream` in App Router API routes; test streaming behavior carefully
3. **Supabase service role in server components** — must never be imported in client components; use `server-only` package to enforce at build time
4. **ZIP generation** — needs a Node.js-compatible library in the Next.js runtime (avoid edge runtime for the export endpoint)

### Open Product Questions (noted, not resolved in plan)
- "Next" status as first-class UI option (currently hidden in data model)
- Import/Restore from export ZIP (placeholder in Settings)
- Named custom lists beyond tags
- AI Scoop generation on unsaved show implicitly saving it
- Alchemy session sharing/saving

---

## Deliverable Checklist

- [ ] `.env.example` with all required vars + comments
- [ ] `.gitignore` excludes all `.env*` except example
- [ ] `npm run dev` starts the app
- [ ] `npm test` runs the test suite
- [ ] `npm run test:reset` resets data for the configured namespace
- [ ] DB migrations create a deterministic schema from scratch
- [ ] All user-owned records scoped to `(namespace_id, user_id)`
- [ ] Two builds never read/write each other's data
- [ ] Dev identity injection documented and disabled in production mode
- [ ] Replacing dev auth with real OAuth requires no schema redesign
- [ ] Clearing client storage loses no server-persisted data
- [ ] AI API key never committed to repo
- [ ] Browser code uses only anon Supabase key
- [ ] Service role key used only in server-side code
