# Implementation Plan: TV/Movie Companion App

## Overview

A personal TV/movie companion app built with Next.js 15 (App Router), TypeScript, Tailwind CSS, and Supabase. The app lets a single user track, discover, and discuss films and TV shows with AI assistance.

---

## 1. Project Setup & Configuration

### Initialize Project

```bash
npx create-next-app@latest . --typescript --tailwind --app --src-dir --import-alias "@/*"
```

### Dependencies

```json
{
  "dependencies": {
    "@supabase/supabase-js": "^2",
    "@tanstack/react-query": "^5",
    "zustand": "^4",
    "zod": "^3",
    "openai": "^4",
    "@radix-ui/react-dialog": "^1",
    "@radix-ui/react-dropdown-menu": "^1",
    "@radix-ui/react-select": "^1",
    "@radix-ui/react-slider": "^1",
    "@radix-ui/react-tabs": "^1",
    "@radix-ui/react-toast": "^1",
    "@radix-ui/react-tooltip": "^1"
  },
  "devDependencies": {
    "vitest": "^1",
    "@testing-library/react": "^14",
    "@testing-library/user-event": "^14",
    "@playwright/test": "^1",
    "@vitejs/plugin-react": "^4"
  }
}
```

### Environment Variables

`.env.example`:
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
TMDB_API_KEY=
OPENAI_API_KEY=
OPENAI_BASE_URL=https://api.openai.com/v1
OPENAI_MODEL=gpt-4o
DEV_DEFAULT_USER_ID=dev-user-1
DEV_NAMESPACE_ID=dev
```

### npm Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "test": "vitest",
    "test:e2e": "playwright test",
    "test:reset": "ts-node scripts/reset-test-data.ts",
    "db:migrate": "supabase db push",
    "db:generate-types": "supabase gen types typescript --local > src/types/supabase.ts"
  }
}
```

---

## 2. Database Schema (Supabase)

### Migration: `supabase/migrations/001_initial_schema.sql`

```sql
-- Shows table: composite PK (namespace_id, user_id, id)
CREATE TABLE shows (
  namespace_id      TEXT NOT NULL,
  user_id           TEXT NOT NULL,
  id                TEXT NOT NULL,
  media_type        TEXT NOT NULL CHECK (media_type IN ('movie', 'tv')),

  -- Catalog fields (from TMDB, selectFirstNonEmpty merge)
  title             TEXT,
  overview          TEXT,
  poster_path       TEXT,
  backdrop_path     TEXT,
  release_date      TEXT,
  first_air_date    TEXT,
  genre_ids         INTEGER[],
  genres            JSONB,
  vote_average      NUMERIC,
  vote_count        INTEGER,
  popularity        NUMERIC,
  original_language TEXT,
  original_title    TEXT,
  adult             BOOLEAN DEFAULT false,
  budget            BIGINT,
  revenue           BIGINT,
  runtime           INTEGER,
  number_of_seasons INTEGER,
  number_of_episodes INTEGER,
  status            TEXT,
  tagline           TEXT,
  homepage          TEXT,
  production_companies JSONB,
  networks          JSONB,
  seasons           JSONB,

  -- User "my*" fields (timestamp-based merge wins)
  my_status         TEXT CHECK (my_status IN ('excited', 'interested', 'watching', 'done', 'dropped', 'paused', NULL)),
  my_status_date    TIMESTAMPTZ,
  my_rating         INTEGER CHECK (my_rating BETWEEN 1 AND 10 OR my_rating IS NULL),
  my_rating_date    TIMESTAMPTZ,
  my_tags           TEXT[],
  my_tags_date      TIMESTAMPTZ,
  my_notes          TEXT,
  my_notes_date     TIMESTAMPTZ,
  my_watch_date     TEXT,
  my_watch_date_date TIMESTAMPTZ,

  -- AI fields
  ai_scoop          TEXT,
  ai_scoop_update_date TIMESTAMPTZ,

  -- Record metadata
  created_at        TIMESTAMPTZ DEFAULT NOW(),
  updated_at        TIMESTAMPTZ DEFAULT NOW(),

  PRIMARY KEY (namespace_id, user_id, id)
);

-- Index for common queries
CREATE INDEX idx_shows_user ON shows (namespace_id, user_id);
CREATE INDEX idx_shows_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_media_type ON shows (namespace_id, user_id, media_type);

-- User settings table
CREATE TABLE user_settings (
  namespace_id      TEXT NOT NULL,
  user_id           TEXT NOT NULL,
  version           BIGINT NOT NULL DEFAULT EXTRACT(EPOCH FROM NOW()),
  tmdb_api_key      TEXT,
  openai_api_key    TEXT,
  openai_base_url   TEXT,
  openai_model      TEXT,
  default_media_type TEXT DEFAULT 'movie',
  theme             TEXT DEFAULT 'dark',
  created_at        TIMESTAMPTZ DEFAULT NOW(),
  updated_at        TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (namespace_id, user_id)
);

-- App metadata (app-level, not user-specific)
CREATE TABLE app_metadata (
  key               TEXT PRIMARY KEY,
  value             JSONB,
  updated_at        TIMESTAMPTZ DEFAULT NOW()
);

-- UI state (per user, ephemeral preferences)
CREATE TABLE ui_state (
  namespace_id      TEXT NOT NULL,
  user_id           TEXT NOT NULL,
  key               TEXT NOT NULL,
  value             JSONB,
  updated_at        TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (namespace_id, user_id, key)
);

-- Updated_at trigger
CREATE OR REPLACE FUNCTION set_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER shows_updated_at BEFORE UPDATE ON shows
  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
CREATE TRIGGER user_settings_updated_at BEFORE UPDATE ON user_settings
  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
```

### Test Reset Script: `scripts/reset-test-data.ts`

Deletes all rows where `namespace_id = process.env.TEST_NAMESPACE_ID ?? 'test'`. Used by `test:reset` npm script.

---

## 3. Directory Structure

Following the fractal architecture from `INSTRUCTIONS.md`: features live inside their page directories; shared primitives live in top-level `components/`, `hooks/`, `utils/`.

```
src/
├── app/                          # Next.js App Router
│   ├── layout.tsx                # Root layout (providers, theme)
│   ├── page.tsx                  # Home → redirects to /home
│   ├── home/
│   │   └── page.tsx
│   ├── find/
│   │   └── page.tsx
│   ├── show/
│   │   └── [id]/
│   │       └── page.tsx
│   ├── person/
│   │   └── [id]/
│   │       └── page.tsx
│   ├── settings/
│   │   └── page.tsx
│   └── api/
│       ├── shows/
│       │   ├── route.ts          # GET collection
│       │   └── [id]/
│       │       └── route.ts      # PUT / DELETE individual
│       ├── catalog/
│       │   ├── search/
│       │   │   └── route.ts
│       │   ├── [id]/
│       │   │   └── route.ts
│       │   └── person/
│       │       └── [id]/
│       │           └── route.ts
│       ├── ai/
│       │   ├── scoop/
│       │   │   └── route.ts      # streaming
│       │   ├── ask/
│       │   │   └── route.ts      # streaming
│       │   ├── concepts/
│       │   │   └── route.ts
│       │   └── recs/
│       │       └── route.ts
│       ├── settings/
│       │   └── route.ts
│       ├── ui-state/
│       │   └── route.ts
│       └── export/
│           └── route.ts
├── components/                   # Shared primitives
│   ├── ui/                       # Radix-based atoms
│   │   ├── Button.tsx
│   │   ├── Badge.tsx
│   │   ├── Card.tsx
│   │   ├── Chip.tsx
│   │   ├── Dialog.tsx
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   ├── Skeleton.tsx
│   │   ├── Slider.tsx
│   │   ├── Spinner.tsx
│   │   └── Toast.tsx
│   ├── ShowCard.tsx
│   ├── ShowGrid.tsx
│   ├── MediaTypeBadge.tsx
│   ├── PosterImage.tsx
│   └── StarRating.tsx
├── features/                     # Cross-page feature modules
│   ├── home/
│   │   ├── FilterSidebar.tsx
│   │   ├── FilterSidebar.test.tsx
│   │   ├── MediaTypeToggle.tsx
│   │   ├── StatusSection.tsx
│   │   ├── useHomeFilters.ts
│   │   └── useHomeFilters.test.ts
│   ├── find/
│   │   ├── ModeSwitcher.tsx
│   │   ├── search/
│   │   │   ├── SearchFeature.tsx
│   │   │   └── useSearch.ts
│   │   ├── ask/
│   │   │   ├── AskFeature.tsx
│   │   │   ├── ChatHistory.tsx
│   │   │   ├── MentionedShows.tsx
│   │   │   └── useAsk.ts
│   │   └── alchemy/
│   │       ├── AlchemyFeature.tsx
│   │       ├── ShowSelector.tsx
│   │       ├── ConceptSelector.tsx
│   │       ├── AlchemyResults.tsx
│   │       └── useAlchemy.ts
│   ├── show-detail/
│   │   ├── HeaderMedia.tsx
│   │   ├── CoreFacts.tsx
│   │   ├── DetailToolbar.tsx
│   │   │   # sticky toolbar: StatusChipGroup + RatingControl
│   │   ├── StatusChipGroup.tsx
│   │   ├── StatusChipGroup.test.tsx
│   │   ├── RatingControl.tsx
│   │   ├── TagsSection.tsx
│   │   ├── OverviewSection.tsx
│   │   ├── ScoopSection.tsx
│   │   ├── ScoopSection.test.tsx
│   │   ├── AskCTA.tsx
│   │   ├── RecommendationsStrand.tsx
│   │   ├── ExploreSimilar/
│   │   │   ├── ExploreSimilar.tsx
│   │   │   ├── ConceptPanel.tsx
│   │   │   └── SimilarResults.tsx
│   │   ├── ProvidersSection.tsx
│   │   ├── CastCrewSection.tsx
│   │   ├── SeasonsSection.tsx    # TV only
│   │   └── BudgetRevenueSection.tsx # movie only
│   ├── person-detail/
│   │   ├── PersonHeader.tsx
│   │   ├── PersonAnalytics.tsx
│   │   └── Filmography.tsx
│   └── settings/
│       ├── AppearanceSettings.tsx
│       ├── AccountSettings.tsx
│       ├── AISettings.tsx
│       ├── CatalogSettings.tsx
│       └── DataSettings.tsx
├── hooks/                        # Shared hooks
│   ├── useCollection.ts          # React Query: user's show collection
│   ├── useShow.ts                # Single show (merged catalog + user data)
│   ├── useUpdateShow.ts          # Optimistic mutation
│   ├── useSettings.ts
│   └── useUIState.ts
├── lib/                          # External client wrappers
│   ├── supabase/
│   │   ├── client.ts             # Browser client
│   │   └── server.ts             # Server-side (service role)
│   ├── tmdb/
│   │   ├── tmdbClient.ts
│   │   └── tmdbMapper.ts
│   └── ai/
│       ├── aiClient.ts           # OpenAI-compatible wrapper
│       └── prompts/
│           ├── scoop.ts
│           ├── ask.ts
│           ├── concepts.ts
│           └── recs.ts
├── middleware.ts                 # Identity injection (X-User-Id / DEV_DEFAULT_USER_ID)
├── types/
│   ├── show.ts                   # Show, ShowStatus, MediaType, etc.
│   ├── catalog.ts                # TMDB raw types
│   ├── ai.ts                     # AI response shapes
│   └── supabase.ts               # Generated by supabase gen types
├── utils/
│   ├── showMerge.ts              # selectFirstNonEmpty + timestamp merge
│   ├── showMerge.test.ts
│   ├── showDefaults.ts           # implicit save defaults
│   ├── showDefaults.test.ts
│   ├── aiParser.ts               # parse structured AI responses
│   └── aiParser.test.ts
└── config/
    ├── constants.ts              # Status labels, tag presets, etc.
    └── queryKeys.ts              # React Query key factories
```

---

## 4. Core Types

Based on `storage-schema.ts` and `storage-schema.md`:

```typescript
// src/types/show.ts

export type MediaType = 'movie' | 'tv';

export type ShowStatus =
  | 'excited'
  | 'interested'
  | 'watching'
  | 'done'
  | 'dropped'
  | 'paused';

// Status groups for Home page display
export const STATUS_GROUPS = {
  active:       ['watching', 'paused'] as ShowStatus[],
  excited:      ['excited'] as ShowStatus[],
  interested:   ['interested'] as ShowStatus[],
  others:       ['done', 'dropped'] as ShowStatus[],
} as const;

export interface Show {
  // Identity
  namespace_id: string;
  user_id: string;
  id: string;
  media_type: MediaType;

  // Catalog fields (TMDB-sourced, selectFirstNonEmpty merge)
  title?: string;
  overview?: string;
  poster_path?: string;
  backdrop_path?: string;
  release_date?: string;
  first_air_date?: string;
  genre_ids?: number[];
  genres?: { id: number; name: string }[];
  vote_average?: number;
  vote_count?: number;
  popularity?: number;
  original_language?: string;
  original_title?: string;
  adult?: boolean;
  budget?: number;
  revenue?: number;
  runtime?: number;
  number_of_seasons?: number;
  number_of_episodes?: number;
  status?: string;
  tagline?: string;
  homepage?: string;
  production_companies?: ProductionCompany[];
  networks?: Network[];
  seasons?: Season[];

  // User fields (timestamp-based merge)
  my_status?: ShowStatus;
  my_status_date?: string;
  my_rating?: number;         // 1–10
  my_rating_date?: string;
  my_tags?: string[];
  my_tags_date?: string;
  my_notes?: string;
  my_notes_date?: string;
  my_watch_date?: string;
  my_watch_date_date?: string;

  // AI fields
  ai_scoop?: string;
  ai_scoop_update_date?: string;

  // Record metadata
  created_at?: string;
  updated_at?: string;
}
```

---

## 5. Data Layer

### Merge Logic: `src/utils/showMerge.ts`

The merge strategy handles two scenarios:
1. **Catalog fields**: `selectFirstNonEmpty` — keep existing non-null/non-empty value; only overwrite with incoming if existing is null/empty.
2. **User (`my*`) fields**: compare `*_date` timestamps; the record with the newer timestamp wins.

```typescript
// selectFirstNonEmpty: prefer existing non-empty value
function selectFirstNonEmpty<T>(existing: T | undefined, incoming: T | undefined): T | undefined {
  if (existing !== null && existing !== undefined && existing !== '') return existing;
  return incoming;
}

// mergeShows: combine a stored record with freshly-fetched catalog data
export function mergeShows(stored: Show, incoming: Partial<Show>): Show {
  const catalogFields: (keyof Show)[] = [
    'title', 'overview', 'poster_path', 'backdrop_path',
    'release_date', 'first_air_date', 'genre_ids', 'genres',
    'vote_average', 'vote_count', 'popularity', 'runtime',
    // ... all catalog fields
  ];

  const userFields: Array<{ field: keyof Show; dateField: keyof Show }> = [
    { field: 'my_status',     dateField: 'my_status_date' },
    { field: 'my_rating',     dateField: 'my_rating_date' },
    { field: 'my_tags',       dateField: 'my_tags_date' },
    { field: 'my_notes',      dateField: 'my_notes_date' },
    { field: 'my_watch_date', dateField: 'my_watch_date_date' },
  ];

  const merged = { ...stored };

  // Merge catalog fields
  for (const field of catalogFields) {
    merged[field] = selectFirstNonEmpty(stored[field], incoming[field]) as never;
  }

  // Merge user fields by timestamp
  for (const { field, dateField } of userFields) {
    const storedDate = stored[dateField] ? new Date(stored[dateField] as string).getTime() : 0;
    const incomingDate = incoming[dateField] ? new Date(incoming[dateField] as string).getTime() : 0;
    if (incomingDate > storedDate) {
      merged[field] = incoming[field] as never;
      merged[dateField] = incoming[dateField] as never;
    }
  }

  return merged;
}
```

### Implicit Save Defaults: `src/utils/showDefaults.ts`

When user actions implicitly save a show that has no explicit status yet:

| Action | Implicit result |
|---|---|
| Set rating | `my_status = 'done'` |
| Add tag | `my_status = 'interested'` (if no status) + tag saved |
| Add notes | save notes without changing status |

```typescript
export function applyImplicitDefaults(show: Partial<Show>, action: 'rate' | 'tag' | 'note'): Partial<Show> {
  const now = new Date().toISOString();
  const patch: Partial<Show> = { ...show };

  if (action === 'rate' && !patch.my_status) {
    patch.my_status = 'done';
    patch.my_status_date = now;
  }
  if (action === 'tag' && !patch.my_status) {
    patch.my_status = 'interested';
    patch.my_status_date = now;
  }

  return patch;
}
```

### React Query Configuration

```typescript
// src/config/queryKeys.ts
export const queryKeys = {
  collection: (userId: string) => ['collection', userId] as const,
  show: (id: string) => ['show', id] as const,
  catalog: (id: string) => ['catalog', id] as const,
  catalogSearch: (query: string, type: MediaType) => ['catalog', 'search', query, type] as const,
  person: (id: string) => ['person', id] as const,
  settings: (userId: string) => ['settings', userId] as const,
};

// Stale times
const COLLECTION_STALE = 30 * 1000;   // 30 seconds
const CATALOG_STALE = 10 * 60 * 1000; // 10 minutes
const AI_SCOOP_TTL = 4 * 60 * 60 * 1000; // 4 hours
```

Source of truth is always the server (Supabase). Browser cache is performance-only; optimistic updates are used for instant UI feedback on mutations.

---

## 6. Auth & Identity Strategy

### Development (Phase 1)

`src/middleware.ts` resolves user identity from:
1. `X-User-Id` request header (for testing multiple users)
2. `DEV_DEFAULT_USER_ID` environment variable
3. Falls back to `'anonymous'`

The resolved `userId` and `namespaceId` are forwarded to API routes via request headers. All API routes read identity from these headers — never from cookies or query params.

```typescript
// src/middleware.ts
export function middleware(request: NextRequest) {
  const userId = request.headers.get('x-user-id')
    ?? process.env.DEV_DEFAULT_USER_ID
    ?? 'anonymous';
  const namespaceId = process.env.DEV_NAMESPACE_ID ?? 'dev';

  const headers = new Headers(request.headers);
  headers.set('x-resolved-user-id', userId);
  headers.set('x-resolved-namespace-id', namespaceId);

  return NextResponse.next({ request: { headers } });
}
```

### OAuth Migration Path

When OAuth is added later, only `resolveUserId()` in middleware changes — reads from JWT/session instead of header. **Zero schema changes required.** All downstream API routes and DB queries remain identical.

### Service Role Key

All DB writes go through API routes using `SUPABASE_SERVICE_ROLE_KEY`. The browser never has direct DB write access.

---

## 7. API Routes

### Shows Collection

**`GET /api/shows`** — Returns full collection for resolved user.
Query params: `media_type`, `status`, `tag` (filter), `sort`.

**`PUT /api/shows/[id]`** — Upsert a show record. Body: `Partial<Show>`. Applies `mergeShows` with existing record before write.

**`DELETE /api/shows/[id]`** — Remove show from collection (delete row). Returns `{ deleted: true }`.

### TMDB Catalog Proxy

**`GET /api/catalog/search`** — Proxies TMDB multi-search. Query: `q`, `type` (movie|tv|all).
**`GET /api/catalog/[id]`** — Full show details from TMDB. Maps via `tmdbMapper.ts`.
**`GET /api/catalog/person/[id]`** — Person details + combined_credits from TMDB.

All TMDB responses are mapped to internal `Show` type via `tmdbMapper.ts`. Transient fields (not persisted) are marked in the mapper.

### AI Routes

**`POST /api/ai/scoop`** — Streaming. Body: `{ showId, title, mediaType }`.
- Checks `ai_scoop_update_date`; returns cached if < 4 hours old.
- Only persists scoop if the show exists in the user's collection.
- Streams response via `ReadableStream` with `text/event-stream`.

**`POST /api/ai/ask`** — Streaming. Body: `{ question, showContext?, conversationHistory }`.
- Uses show context when provided (from ShowDetail AskCTA).
- Streams markdown response.

**`POST /api/ai/concepts`** — Non-streaming. Body: `{ showIds: string[], mediaType }`.
- Returns concept objects: `{ id, label, description, showIds[] }`.
- Used by Alchemy (ShowSelector → ConceptSelector) and ExploreSimilar.

**`POST /api/ai/recs`** — Non-streaming. Body: `{ conceptId, conceptLabel, sourceShowIds[], count }`.
- Returns recommended show IDs + rationale.
- Results are fetched from TMDB catalog to hydrate full show objects.

### Settings & Utility

**`GET/PUT /api/settings`** — User settings CRUD.
**`GET/PUT /api/ui-state`** — Per-key UI state (sidebar open/closed, etc.).
**`GET /api/export`** — Returns ZIP containing `shows.json` + `settings.json` for data portability.

---

## 8. External Integrations

### TMDB Client: `src/lib/tmdb/tmdbClient.ts`

Thin wrapper around TMDB REST API v3. Reads `TMDB_API_KEY` from env. Methods:
- `searchMulti(query, page)` → raw search results
- `getMovie(id)` → full movie details
- `getTVShow(id)` → full TV details
- `getPerson(id)` → person + combined_credits
- `getWatchProviders(id, type)` → streaming providers

### TMDB Mapper: `src/lib/tmdb/tmdbMapper.ts`

Maps TMDB raw objects to internal `Show` type. Marks certain fields as "transient" (not persisted to DB but included in API responses for rendering): `cast`, `crew`, `watch_providers`.

### AI Client: `src/lib/ai/aiClient.ts`

OpenAI-compatible client configured via env:
- `OPENAI_API_KEY`
- `OPENAI_BASE_URL` (allows swapping to any OpenAI-compatible provider)
- `OPENAI_MODEL`

Exposes:
- `streamCompletion(systemPrompt, userPrompt, options)` → `ReadableStream`
- `complete(systemPrompt, userPrompt, options)` → `string`

### AI Prompts: `src/lib/ai/prompts/`

Each prompt file exports a function that takes context and returns `{ system, user }`:
- `scoop.ts` — generating show scoop (personality defined in `ai_voice_personality.md`)
- `ask.ts` — conversational Q&A about shows
- `concepts.ts` — concept extraction from show set (rules from `concept_system.md`)
- `recs.ts` — show recommendations from concept

The AI persona (from `ai_voice_personality.md`) is embedded in all system prompts: curious, warm, specific, never generic.

---

## 9. Pages & Features

### Home Page (`/home`)

Layout: left `FilterSidebar` + main content area.

**FilterSidebar**: Filters by genre, decade, tags, media type. State managed by `useHomeFilters` hook (Zustand store + URL sync via `nuqs` or `useSearchParams`).

**Main content**: `MediaTypeToggle` (All / Movies / TV) at top. Below: status sections in order:
1. **Active** (watching + paused) — horizontal scroll strip
2. **Excited** — horizontal scroll strip
3. **Interested** — grid
4. **Others** (done + dropped) — collapsed grid with "Show more"

Each section is a `StatusSection` component rendering a `ShowGrid` or scroll strip. Shows with no status (untracked) are not shown on Home.

**ShowCard**: Poster + title + status chip + rating. Clicking opens ShowDetail.

### Find Page (`/find`)

**ModeSwitcher**: Tabs for Search / Ask / Alchemy.

**Search**: Text input → debounced `GET /api/catalog/search` → grid of results. Results show "In collection" badge if in user's collection. Click → ShowDetail.

**Ask**: Chat interface. User types a question; response streams from `/api/ai/ask`. Responses may include `[[SHOW:id:title]]` tokens that render as inline show pills linking to ShowDetail. Chat history maintained in component state.

**Alchemy**:
1. `ShowSelector`: User picks 2–5 shows from their collection (or searches catalog)
2. `ConceptSelector`: Calls `/api/ai/concepts` with selected shows → renders concept pills. User picks 1–3 concepts.
3. `AlchemyResults`: Calls `/api/ai/recs` with selected concepts → renders recommended shows grid.

### Show Detail Page (`/show/[id]`)

Data fetching: `GET /api/catalog/[id]` for full catalog data, merged with user's collection record if present.

**Layout** (vertical scroll):
1. **HeaderMedia** — backdrop image, poster, title, year, runtime/seasons, genres
2. **DetailToolbar** — **sticky**, not inside scroll. Contains:
   - `StatusChipGroup`: status chips (Excited / Interested / Watching / Done / Dropped / Paused). Selecting one saves immediately. Deselecting removes status.
   - `RatingControl`: 1–10 slider/stars. Setting rating triggers implicit save if no status (→ Done).
3. **CoreFacts** — language, release date, budget/revenue (movie), network (TV)
4. **TagsSection** — tag chips (editable). Adding tag triggers implicit save if no status (→ Interested).
5. **OverviewSection** — show overview text
6. **ScoopSection** — AI-generated scoop. Streams on load if in collection. Shows loading state, then rendered markdown. "Refresh" button (re-fetches ignoring TTL).
7. **AskCTA** — "Ask about this show" button → navigates to Find/Ask with show context pre-loaded
8. **RecommendationsStrand** — horizontal scroll of "More like this" (from TMDB similar endpoint)
9. **ExploreSimilar** — concept-driven exploration:
   - `ConceptPanel`: shows concepts for this show (from `/api/ai/concepts`)
   - `SimilarResults`: shows from `/api/ai/recs` for selected concept
10. **ProvidersSection** — streaming/rent/buy providers (from TMDB watch providers)
11. **CastCrewSection** — top cast + director/creator
12. **SeasonsSection** (TV only) — season list with episode counts
13. **BudgetRevenueSection** (movie only) — budget, revenue, profit

### Person Detail Page (`/person/[id]`)

- **PersonHeader**: photo, name, bio, known-for department
- **PersonAnalytics**: stats derived from filmography (total films, genres, decades, avg rating of their works)
- **Filmography**: grouped by role (Actor / Director / Writer). Each entry links to ShowDetail.

### Settings Page (`/settings`)

Tabs:
- **Appearance**: theme (dark/light/system), default media type
- **Account**: user ID display (dev), future OAuth section
- **AI**: OpenAI API key, base URL, model selector (with test button)
- **Catalog**: TMDB API key (with test button)
- **Data**: Export button (downloads ZIP), import (future), danger zone (clear all data)

---

## 10. AI Surface Details

### Scoop (Show Detail)

- Triggered on show detail page load if show is in collection
- 4-hour TTL: check `ai_scoop_update_date`; skip fetch if fresh
- Only persisted if show is in user's collection (not for catalog-only views)
- Streaming via Server-Sent Events
- Content: personality-driven show summary with specific facts, tone from `ai_voice_personality.md`
- Quality bar: specific > generic, no filler phrases (from `discovery_quality_bar.md`)

### Ask (Find Page + ShowDetail CTA)

- Conversational — maintains history within session
- Show context injected when opened from ShowDetail
- Streaming response; supports inline show references `[[SHOW:id:title]]`
- Can answer questions about themes, similar shows, cast, context

### Concepts (Alchemy + ExploreSimilar)

From `concept_system.md`:
- Concepts are thematic lenses: mood, tone, setting, narrative device, cultural context
- NOT genre labels or character names
- Each concept has: `id`, `label` (2–4 words), `description` (1 sentence), `sourceShowIds`
- Alchemy: generate concepts from 2–5 user-selected shows
- ExploreSimilar: generate concepts from single show

### Recommendations (Alchemy Results + ExploreSimilar)

- Input: concept + source show IDs
- Output: TMDB show IDs + rationale strings
- Client hydrates IDs via `/api/catalog/[id]` to get full Show objects
- Results rendered as ShowGrid with rationale tooltips

---

## 11. Component Standards

From `INSTRUCTIONS.md`:

- **Humble components**: components are layout/render only; logic lives in hooks
- **Co-location**: feature hooks, types, and tests live next to their feature component
- **No prop drilling past 2 levels**: use React Query or Zustand for shared state
- **Optimistic updates**: all mutations update cache immediately, revert on error
- **Skeleton loading**: every async section has a skeleton placeholder
- **Error boundaries**: page-level error boundaries catch render failures

---

## 12. Testing Plan

### Unit Tests (Vitest)

| File | What's tested |
|---|---|
| `showMerge.test.ts` | selectFirstNonEmpty, timestamp merge, conflict resolution |
| `showDefaults.test.ts` | implicit save defaults for rate/tag/note actions |
| `aiParser.test.ts` | parsing `[[SHOW:id:title]]` tokens, concept JSON parsing |
| `tmdbMapper.test.ts` | TMDB movie → Show, TMDB TV → Show, transient field exclusion |

### Component Tests (@testing-library/react)

| Component | What's tested |
|---|---|
| `StatusChipGroup` | selecting status saves, deselecting removes, exclusive selection |
| `ScoopSection` | shows loading state, renders streamed content, refresh triggers new fetch |
| `useHomeFilters` | filter state updates, URL sync, reset |
| `RatingControl` | sets rating, triggers implicit Done status |

### E2E Tests (Playwright)

| Scenario | Steps |
|---|---|
| rate-to-save | Open catalog show → set rating → verify in collection with status=done |
| tag-to-save | Open catalog show → add tag → verify in collection with status=interested |
| alchemy-flow | Select 2+ shows → get concepts → select concept → get recommendations |
| status-removal | Set status → remove status → verify show removed from Home sections |
| export | Settings → Export → verify ZIP downloaded with shows.json |

---

## 13. Implementation Phases

### Phase 1: Infrastructure
- [ ] Initialize Next.js 15 project with TypeScript + Tailwind
- [ ] Supabase project setup + migration `001_initial_schema.sql`
- [ ] Middleware for identity injection (`x-resolved-user-id`)
- [ ] `supabase/server.ts` and `supabase/client.ts` wrappers
- [ ] `.env.example`, `test:reset` script
- [ ] Vitest + Playwright configuration

### Phase 2: Data Types + Merge Logic
- [ ] All types in `src/types/`
- [ ] `showMerge.ts` + tests (TDD)
- [ ] `showDefaults.ts` + tests
- [ ] `tmdbClient.ts` + `tmdbMapper.ts` + tests
- [ ] React Query setup with `queryKeys.ts`

### Phase 3: Collection API + Home Page
- [ ] `GET /api/shows`, `PUT /api/shows/[id]`, `DELETE /api/shows/[id]`
- [ ] `GET /api/catalog/search`, `GET /api/catalog/[id]`
- [ ] `useCollection` hook
- [ ] Home page: FilterSidebar, StatusSection, ShowGrid, ShowCard
- [ ] `useHomeFilters` hook + tests

### Phase 4: Show Detail Page
- [ ] `GET /api/catalog/[id]` (full details including cast, seasons, providers)
- [ ] ShowDetail page scaffold
- [ ] HeaderMedia, CoreFacts, DetailToolbar (sticky)
- [ ] StatusChipGroup + RatingControl with optimistic updates + tests
- [ ] TagsSection, OverviewSection
- [ ] RecommendationsStrand (TMDB similar)
- [ ] ProvidersSection, CastCrewSection, SeasonsSection, BudgetRevenueSection

### Phase 5: AI Surfaces
- [ ] `aiClient.ts` with streaming support
- [ ] All prompt files (scoop, ask, concepts, recs)
- [ ] `POST /api/ai/scoop` (streaming + TTL + persistence guard)
- [ ] ScoopSection with streaming render + tests
- [ ] `POST /api/ai/ask` (streaming)
- [ ] Ask feature in Find page (ChatHistory, MentionedShows)
- [ ] `POST /api/ai/concepts` + `POST /api/ai/recs`
- [ ] Alchemy feature (ShowSelector → ConceptSelector → AlchemyResults)
- [ ] ExploreSimilar in ShowDetail (ConceptPanel + SimilarResults)

### Phase 6: Person Detail + Settings + Export
- [ ] `GET /api/catalog/person/[id]`
- [ ] PersonDetail page (PersonHeader, PersonAnalytics, Filmography)
- [ ] Settings page (all tabs)
- [ ] `GET/PUT /api/settings`
- [ ] `GET /api/export` (ZIP download)
- [ ] DataSettings component

### Phase 7: Polish
- [ ] Empty states for all sections (no shows, no results, no concepts)
- [ ] Loading skeletons for all async sections
- [ ] Error boundary at page level
- [ ] E2E test suite passing
- [ ] ESLint + TypeScript strict mode clean
- [ ] Lighthouse accessibility pass

---

## 14. Key Design Decisions

1. **No direct browser DB access**: All reads/writes go through API routes using service role key. Browser only has anon key for potential realtime subscriptions.

2. **Identity isolation via namespace_id**: All rows include `namespace_id`. Changing `DEV_NAMESPACE_ID` isolates test data without affecting production data. OAuth migration = swap `resolveUserId()` only.

3. **Merge-not-overwrite strategy**: Catalog fields use `selectFirstNonEmpty`; user fields use timestamp comparison. This ensures that stale catalog fetches don't wipe user data, and that syncing from multiple devices is conflict-safe.

4. **AI scoop persistence guard**: Scoop is only saved to DB if the show is in the user's collection. Generating scoop for a catalog-browsing session doesn't pollute the collection.

5. **Sticky DetailToolbar**: Status and rating controls are outside the scroll container on ShowDetail. They remain accessible while reading overview, cast, scoop, etc.

6. **Streaming via ReadableStream / SSE**: AI routes stream text to the browser. Client uses `fetch` with `response.body.getReader()` for progressive rendering.

7. **Concept system as discovery primitive**: Concepts (not genres) are the discovery layer. They're generated by AI from a show set, not looked up from a taxonomy. This enables nuanced recommendations and the Alchemy feature.
