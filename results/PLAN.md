# Implementation Plan — TV/Movie Companion App

## 1. Architecture Overview

### 1.1 Tech Stack

| Layer | Choice | Notes |
|---|---|---|
| Framework | Next.js (latest stable, App Router) | Server components + API routes |
| Persistence | Supabase (hosted or local) | Postgres + Row Level Security |
| External catalog | TMDB API | Movie/TV metadata, images, providers |
| AI provider | OpenAI-compatible API | Scoop, Ask, Concepts, Alchemy |
| Styling | Tailwind CSS + CSS variables | Theme tokens, no inline styles |
| State management | React context + server state via SWR or TanStack Query | Cache Supabase reads client-side |
| Auth (benchmark) | Dev identity injection via `X-User-Id` header / dev selector | Swappable for real OAuth later |

### 1.2 Fractal Directory Structure

```
src/
├── config/                    # Global constants, env var access
│   ├── constants.ts
│   ├── env.ts                 # Typed env var wrapper
│   └── tmdb.ts                # TMDB config (base URLs, image sizes)
├── theme/                     # Design tokens (colors, spacing, typography)
│   ├── tokens.ts
│   └── globals.css
├── components/                # Shared UI primitives
│   ├── ShowTile/
│   │   ├── ShowTile.tsx
│   │   └── hooks/
│   │       └── useShowTile.ts
│   ├── StatusChips/
│   │   ├── StatusChips.tsx
│   │   └── hooks/
│   │       └── useStatusChips.ts
│   ├── RatingBar/
│   │   ├── RatingBar.tsx
│   │   └── hooks/
│   │       └── useRatingBar.ts
│   ├── TagPicker/
│   │   ├── TagPicker.tsx
│   │   └── hooks/
│   │       └── useTagPicker.ts
│   ├── MediaTypeToggle/
│   │   └── MediaTypeToggle.tsx
│   ├── ConceptChip/
│   │   └── ConceptChip.tsx
│   ├── ShowStrand/             # Horizontal scrollable row of ShowTiles
│   │   └── ShowStrand.tsx
│   └── LoadingStates/
│       └── LoadingStates.tsx
├── hooks/                     # Global hooks
│   ├── useIdentity.ts         # Returns current (namespace_id, user_id)
│   ├── useCollection.ts       # CRUD for user's show collection
│   ├── useShow.ts             # Single show fetch + merge
│   ├── useSettings.ts         # Cloud + local settings
│   └── useAI.ts               # Shared AI request hook
├── utils/                     # Global pure functions
│   ├── showMerge.ts           # Merge rules (selectFirstNonEmpty, timestamp wins)
│   ├── tmdbMapper.ts          # TMDB JSON → Show model mapping
│   ├── dateUtils.ts
│   ├── exportData.ts          # Export zip generation
│   └── aiParser.ts            # Parse AI structured output (showList, concepts)
├── lib/                       # Server/service layer
│   ├── supabase/
│   │   ├── client.ts          # Browser client (anon key)
│   │   ├── server.ts          # Server client (service role, server-only)
│   │   └── middleware.ts      # Identity injection middleware
│   ├── tmdb/
│   │   ├── tmdbClient.ts      # TMDB API wrapper
│   │   ├── tmdbTypes.ts       # TMDB response types
│   │   └── tmdbSearch.ts      # Search + detail + credits + providers
│   └── ai/
│       ├── aiClient.ts        # OpenAI-compatible client
│       ├── prompts/
│       │   ├── scoopPrompt.ts
│       │   ├── askPrompt.ts
│       │   ├── conceptPrompt.ts
│       │   ├── alchemyPrompt.ts
│       │   └── summarizePrompt.ts
│       └── aiTypes.ts
├── app/                       # Next.js App Router
│   ├── layout.tsx             # Root layout: sidebar + main content
│   ├── api/                   # API routes (server-side)
│   │   ├── shows/
│   │   │   ├── route.ts       # GET collection, POST save, DELETE remove
│   │   │   └── [id]/
│   │   │       └── route.ts   # GET/PATCH single show
│   │   ├── search/
│   │   │   └── route.ts       # Proxy to TMDB search
│   │   ├── tmdb/
│   │   │   ├── details/[id]/route.ts
│   │   │   ├── credits/[id]/route.ts
│   │   │   ├── providers/[id]/route.ts
│   │   │   ├── recommendations/[id]/route.ts
│   │   │   └── person/[id]/route.ts
│   │   ├── ai/
│   │   │   ├── scoop/route.ts
│   │   │   ├── ask/route.ts
│   │   │   ├── concepts/route.ts
│   │   │   └── alchemy/route.ts
│   │   ├── settings/
│   │   │   └── route.ts
│   │   └── export/
│   │       └── route.ts
│   └── (main)/                # Route group for main layout
│       ├── page.tsx           # → CollectionHome
│       ├── find/
│       │   └── page.tsx       # → FindDiscover (Search/Ask/Alchemy)
│       ├── show/[id]/
│       │   └── page.tsx       # → ShowDetail
│       ├── person/[id]/
│       │   └── page.tsx       # → PersonDetail
│       └── settings/
│           └── page.tsx       # → Settings
└── pages/                     # Page-level feature containers
    ├── CollectionHome/
    │   ├── CollectionHome.tsx
    │   ├── hooks/
    │   │   ├── useCollectionHome.ts
    │   │   └── useCollectionFilters.ts
    │   └── features/
    │       ├── StatusGroup/
    │       │   ├── StatusGroup.tsx
    │       │   └── hooks/
    │       │       └── useStatusGroup.ts
    │       ├── FilterSidebar/
    │       │   ├── FilterSidebar.tsx
    │       │   └── hooks/
    │       │       └── useFilterSidebar.ts
    │       └── EmptyState/
    │           └── EmptyState.tsx
    ├── FindDiscover/
    │   ├── FindDiscover.tsx
    │   ├── hooks/
    │   │   └── useFindDiscover.ts
    │   └── features/
    │       ├── SearchMode/
    │       │   ├── SearchMode.tsx
    │       │   └── hooks/
    │       │       └── useSearchMode.ts
    │       ├── AskMode/
    │       │   ├── AskMode.tsx
    │       │   ├── hooks/
    │       │   │   ├── useAskChat.ts
    │       │   │   └── useAskMentions.ts
    │       │   └── features/
    │       │       ├── ChatMessage/
    │       │       │   └── ChatMessage.tsx
    │       │       ├── MentionedShowsStrip/
    │       │       │   └── MentionedShowsStrip.tsx
    │       │       └── StarterPrompts/
    │       │           └── StarterPrompts.tsx
    │       └── AlchemyMode/
    │           ├── AlchemyMode.tsx
    │           ├── hooks/
    │           │   └── useAlchemy.ts
    │           └── features/
    │               ├── ShowPicker/
    │               │   └── ShowPicker.tsx
    │               ├── ConceptSelector/
    │               │   └── ConceptSelector.tsx
    │               └── AlchemyResults/
    │                   └── AlchemyResults.tsx
    ├── ShowDetail/
    │   ├── ShowDetail.tsx
    │   ├── hooks/
    │   │   ├── useShowDetail.ts
    │   │   └── useShowActions.ts
    │   └── features/
    │       ├── HeaderMedia/
    │       │   ├── HeaderMedia.tsx
    │       │   └── hooks/
    │       │       └── useHeaderMedia.ts
    │       ├── CoreFacts/
    │       │   └── CoreFacts.tsx
    │       ├── MyRelationship/
    │       │   ├── MyRelationship.tsx
    │       │   └── hooks/
    │       │       └── useMyRelationship.ts
    │       ├── ScoopSection/
    │       │   ├── ScoopSection.tsx
    │       │   └── hooks/
    │       │       └── useScoop.ts
    │       ├── ExploreSimilar/
    │       │   ├── ExploreSimilar.tsx
    │       │   └── hooks/
    │       │       └── useExploreSimilar.ts
    │       ├── StreamingProviders/
    │       │   └── StreamingProviders.tsx
    │       ├── CastCrew/
    │       │   └── CastCrew.tsx
    │       ├── SeasonsSection/
    │       │   └── SeasonsSection.tsx
    │       └── BudgetRevenue/
    │           └── BudgetRevenue.tsx
    ├── PersonDetail/
    │   ├── PersonDetail.tsx
    │   ├── hooks/
    │   │   └── usePersonDetail.ts
    │   └── features/
    │       ├── PersonHeader/
    │       │   └── PersonHeader.tsx
    │       ├── PersonAnalytics/
    │       │   └── PersonAnalytics.tsx
    │       └── Filmography/
    │           └── Filmography.tsx
    └── SettingsPage/
        ├── SettingsPage.tsx
        ├── hooks/
        │   └── useSettingsPage.ts
        └── features/
            ├── AppSettings/
            │   └── AppSettings.tsx
            ├── UserSettings/
            │   └── UserSettings.tsx
            ├── AISettings/
            │   └── AISettings.tsx
            └── DataManagement/
                └── DataManagement.tsx
```

### 1.3 Key Architectural Principles

1. **Humble components**: TSX files contain markup and binding only. All logic extracted to `useFeatureLogic()` hooks.
2. **No index.tsx**: Main file matches directory name (`ShowTile/ShowTile.tsx`).
3. **Co-location**: Feature-specific hooks/utils live inside that feature's directory.
4. **No magic numbers**: All constants in `config/` or local `constants.ts`. No hex codes or pixel values in TSX — theme tokens only.
5. **Backend is source of truth**: Supabase holds all user data. Client caches are disposable.

---

## 2. Data Layer

### 2.1 Supabase Schema (Migrations)

#### Table: `shows`

Primary table storing catalog metadata + user overlay. Partitioned by `(namespace_id, user_id)`.

```sql
CREATE TABLE shows (
  id              TEXT NOT NULL,
  namespace_id    TEXT NOT NULL,
  user_id         TEXT NOT NULL,
  title           TEXT NOT NULL,
  show_type       TEXT NOT NULL CHECK (show_type IN ('movie', 'tv', 'person', 'unknown')),

  -- External IDs
  external_ids    JSONB DEFAULT '{}',

  -- Catalog metadata
  overview        TEXT,
  genres          TEXT[] DEFAULT '{}',
  tagline         TEXT,
  homepage        TEXT,
  original_language TEXT,
  spoken_languages TEXT[] DEFAULT '{}',
  languages       TEXT[] DEFAULT '{}',

  -- Images
  poster_url      TEXT,
  backdrop_url    TEXT,
  logo_url        TEXT,
  network_logos   TEXT[] DEFAULT '{}',

  -- Ratings / popularity
  vote_average    DOUBLE PRECISION,
  vote_count      INTEGER,
  popularity      DOUBLE PRECISION,

  -- Dates
  last_air_date   TIMESTAMPTZ,
  first_air_date  TIMESTAMPTZ,
  release_date    TIMESTAMPTZ,

  -- Movie-specific
  runtime         INTEGER,
  budget          BIGINT,
  revenue         BIGINT,

  -- TV-specific
  series_status   TEXT,
  number_of_episodes INTEGER,
  number_of_seasons  INTEGER,
  episode_run_time   INTEGER[] DEFAULT '{}',

  -- User data ("My")
  my_tags              TEXT[] DEFAULT '{}',
  my_tags_update_date  TIMESTAMPTZ,
  my_score             DOUBLE PRECISION,
  my_score_update_date TIMESTAMPTZ,
  my_status            TEXT CHECK (my_status IN ('active', 'next', 'later', 'done', 'quit', 'wait')),
  my_status_update_date TIMESTAMPTZ,
  my_interest          TEXT CHECK (my_interest IN ('excited', 'interested')),
  my_interest_update_date TIMESTAMPTZ,

  -- AI data
  ai_scoop              TEXT,
  ai_scoop_update_date  TIMESTAMPTZ,

  -- Management
  details_update_date   TIMESTAMPTZ,
  creation_date         TIMESTAMPTZ DEFAULT NOW(),
  is_test               BOOLEAN DEFAULT FALSE,

  -- Provider data
  provider_data         JSONB,

  PRIMARY KEY (namespace_id, user_id, id)
);

CREATE INDEX idx_shows_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_tags ON shows USING GIN (my_tags);
CREATE INDEX idx_shows_type ON shows (namespace_id, user_id, show_type);
```

#### Table: `cloud_settings`

```sql
CREATE TABLE cloud_settings (
  id              TEXT NOT NULL DEFAULT 'globalSettings',
  namespace_id    TEXT NOT NULL,
  user_id         TEXT NOT NULL,
  user_name       TEXT NOT NULL,
  version         DOUBLE PRECISION NOT NULL,
  catalog_api_key TEXT,
  ai_api_key      TEXT,
  ai_model        TEXT NOT NULL DEFAULT 'gpt-4o',

  PRIMARY KEY (namespace_id, user_id, id)
);
```

#### Table: `app_metadata`

```sql
CREATE TABLE app_metadata (
  namespace_id        TEXT NOT NULL,
  data_model_version  INTEGER NOT NULL DEFAULT 3,

  PRIMARY KEY (namespace_id)
);
```

#### Row Level Security

```sql
ALTER TABLE shows ENABLE ROW LEVEL SECURITY;
ALTER TABLE cloud_settings ENABLE ROW LEVEL SECURITY;

-- Policy: users can only access their own data within their namespace
CREATE POLICY shows_isolation ON shows
  USING (
    namespace_id = current_setting('app.namespace_id', TRUE)
    AND user_id = current_setting('app.user_id', TRUE)
  );

CREATE POLICY settings_isolation ON cloud_settings
  USING (
    namespace_id = current_setting('app.namespace_id', TRUE)
    AND user_id = current_setting('app.user_id', TRUE)
  );
```

### 2.2 Identity & Isolation

**Namespace isolation**: Every Supabase query is scoped by `namespace_id` (from env var `NAMESPACE_ID`). Two builds never touch each other's data.

**User identity**: All user-owned records include `user_id`. In benchmark mode, this is injected via:
- Server: reads `X-User-Id` header (dev mode only) or falls back to env `DEFAULT_USER_ID`
- Client: a dev-only user selector component in settings (hidden in production)

**Migration path to real OAuth**: Replace the header/env injection with Supabase Auth. The `user_id` column is already an opaque string — no schema change needed. RLS policies switch from `current_setting` to `auth.uid()`.

### 2.3 Merge Rules

Implemented in `utils/showMerge.ts`:

1. **Non-user fields**: `selectFirstNonEmpty(newValue, existingValue)` — never overwrite a non-empty stored value with empty/null.
2. **User fields** (`my_*`): Compare `*_update_date` timestamps. Newer wins. If only one side has a date, that side wins.
3. **`details_update_date`**: Set to `now()` after any merge.
4. **`creation_date`**: Set only on first creation; never overwritten.

---

## 3. External Services

### 3.1 TMDB Integration

All TMDB calls proxied through Next.js API routes (keeps API key server-side).

| Endpoint | TMDB API | Used By |
|---|---|---|
| `GET /api/search?q=...&type=...` | `/search/multi` | Search mode |
| `GET /api/tmdb/details/[id]?type=...` | `/movie/{id}` or `/tv/{id}` with `append_to_response=credits,videos,recommendations,similar,images,watch/providers` | Show detail |
| `GET /api/tmdb/credits/[id]?type=...` | `/movie/{id}/credits` or `/tv/{id}/credits` | Cast/crew |
| `GET /api/tmdb/providers/[id]?type=...` | `/movie/{id}/watch/providers` or `/tv/{id}/watch/providers` | Streaming availability |
| `GET /api/tmdb/recommendations/[id]?type=...` | `/movie/{id}/recommendations` or `/tv/{id}/recommendations` | Traditional recs strand |
| `GET /api/tmdb/person/[id]` | `/person/{id}?append_to_response=combined_credits,images` | Person detail |

**Mapping**: `tmdbMapper.ts` converts TMDB JSON responses into the app's `Show` type using the field mapping rules from the storage schema doc. TMDB `id` becomes `Show.id` (prefixed or typed to avoid collisions, e.g., `"tmdb-movie-12345"`). External IDs stored in `externalIds`.

**Image URLs**: Constructed from TMDB's base URL + size + path. Logo selection: prefer English, highest `vote_average`.

### 3.2 AI Integration

All AI calls proxied through Next.js API routes (streaming supported via ReadableStream).

**Client**: `lib/ai/aiClient.ts` wraps OpenAI-compatible chat completions. Reads `AI_API_KEY` and `AI_MODEL` from env or user settings. Supports streaming for Scoop and Ask.

**Prompt architecture** (in `lib/ai/prompts/`):

Each prompt builder function takes structured context and returns a messages array.

#### Scoop Prompt (`scoopPrompt.ts`)
- System: personality spec (warm, opinionated, spoiler-safe, gossipy)
- Context: show title, year, genres, overview, community score, user's status/rating/tags if saved
- Output: structured mini blog post — personal take, honest stack-up, The Scoop centerpiece, fit/warnings, worth-it verdict
- Length target: 150–350 words

#### Ask Prompt (`askPrompt.ts`)
- System: conversational friend persona, taste-aware, TV/movies only
- Context: user's library summary (titles + statuses + ratings), conversation history (with older turns summarized), optional handoff show context
- Output: structured `{ commentary, showList }` where `showList` uses `Title::externalId::mediaType;;` format
- Summarization: after ~10 turns, older messages condensed into 1–2 sentence summaries preserving persona tone

#### Concept Prompt (`conceptPrompt.ts`)
- System: generate evocative 1–3 word concept ingredients
- Context: show title(s), genres, overviews
- Output: bullet list of 8 concepts, ordered by strength
- Rules: no generic concepts, no plot details, diverse across axes (structure/vibe/emotion/craft)

#### Alchemy/Explore Similar Recs Prompt (`alchemyPrompt.ts`)
- System: recommend real shows with excited, concept-grounded reasoning
- Context: selected concepts, input show(s), user's library (to avoid duplicates)
- Output: list of recommended shows with `title`, `tmdb_id`, `media_type`, and concept-grounded reason
- Counts: 5 recs for Explore Similar, 6 for Alchemy
- Rules: recent bias but allow classics, reasons must name which concepts match

#### Summarize Prompt (`summarizePrompt.ts`)
- Condensing older Ask conversation turns into 1–2 sentences preserving persona tone

### 3.3 AI Response Parsing (`utils/aiParser.ts`)

**showList parsing**: Split on `;;`, then split each entry on `::` to extract `[title, externalId, mediaType]`. Look up TMDB by `externalId`; accept if title matches case-insensitively. If not found, show as non-interactive or hand off to Search.

**Concept parsing**: Split bullet-list response into individual concept strings. Trim whitespace and formatting characters.

**Fallback**: If structured output parsing fails, retry once with stricter formatting instructions. Otherwise fall back to unstructured commentary + Search handoff.

---

## 4. Feature Implementation Plan

### Phase 1: Foundation (Infrastructure + Data Layer)

**Goal**: Project scaffold, Supabase schema, identity, env config, shared components.

#### 1.1 Project Setup
- Initialize Next.js app with App Router, TypeScript, Tailwind CSS
- Set up `.env.example` with all required variables:
  ```
  NEXT_PUBLIC_SUPABASE_URL=       # Supabase project URL
  NEXT_PUBLIC_SUPABASE_ANON_KEY=  # Supabase anon/public key
  SUPABASE_SERVICE_ROLE_KEY=      # Server-only; never exposed to browser
  TMDB_API_KEY=                   # TMDB v3 API key
  AI_API_KEY=                     # OpenAI-compatible API key
  AI_MODEL=gpt-4o                 # AI model name
  AI_API_BASE_URL=                # Optional: custom AI endpoint
  NAMESPACE_ID=                   # Build isolation namespace
  DEFAULT_USER_ID=                # Default user for benchmark mode
  NODE_ENV=development            # Controls dev identity injection
  ```
- `.gitignore`: exclude `.env*` (except `.env.example`), `node_modules/`, `.next/`
- Set up `src/config/env.ts` with typed access to all env vars
- Set up `src/config/constants.ts` (status values, interest values, filter types, etc.)

#### 1.2 Supabase Schema
- Create migration files for `shows`, `cloud_settings`, `app_metadata` tables
- Set up RLS policies for namespace + user isolation
- Create seed data script for test namespace

#### 1.3 Identity Middleware
- `lib/supabase/middleware.ts`: Next.js middleware that reads `X-User-Id` header (dev mode) or `DEFAULT_USER_ID`, sets Supabase RLS context
- `hooks/useIdentity.ts`: Client-side hook returning current `{ namespaceId, userId }`
- Dev user selector component (conditionally rendered when `NODE_ENV === 'development'`)

#### 1.4 Theme & Shared Components
- Design tokens in `src/theme/tokens.ts` (colors, spacing, typography, border radii)
- `globals.css` with Tailwind + CSS custom properties from tokens
- Build shared primitives: `ShowTile`, `StatusChips`, `RatingBar`, `TagPicker`, `MediaTypeToggle`, `ConceptChip`, `ShowStrand`, `LoadingStates`
- Each component follows humble pattern — logic in hooks, TSX is markup only

#### 1.5 Core Data Hooks
- `useCollection.ts`: Fetch user's shows from Supabase, filtered by status/tags/genre/decade/score. CRUD operations.
- `useShow.ts`: Fetch single show from Supabase + TMDB, merge per merge rules, persist.
- `useSettings.ts`: Read/write cloud settings and local settings.

#### 1.6 Scripts
- `npm run dev` — start Next.js dev server
- `npm test` — run test suite
- `npm run db:migrate` — apply Supabase migrations
- `npm run db:seed` — seed test data for current namespace
- `npm run test:reset` — delete all data for current namespace (destructive, scoped)

### Phase 2: Collection & Navigation

**Goal**: App shell, sidebar, Collection Home page, basic navigation.

#### 2.1 App Shell (`app/layout.tsx`)
- Left sidebar with filter navigation (All Shows, tag filters, data filters)
- Top bar with app name, Find/Discover button, Settings button
- Main content area for routed pages
- Responsive: sidebar collapses on smaller screens

#### 2.2 Filter Sidebar (`FilterSidebar`)
- "All Shows" (always present)
- Dynamic tag filters (one per user tag, plus "No tags" if applicable)
- Data filters: genre, decade, community score ranges
- Media type toggle (All / Movies / TV) at top
- Selected filter persisted to `lastSelectedFilter` in local storage
- Hook: `useFilterSidebar.ts` computes available filters from user's collection

#### 2.3 Collection Home (`CollectionHome`)
- Renders shows grouped by status sections:
  1. **Active** — prominent larger tiles
  2. **Excited** (Later + Excited interest)
  3. **Interested** (Later + Interested interest)
  4. **Other** (collapsed group): Wait, Quit, Done, unclassified Later
- Each section uses `StatusGroup` feature component
- Applies selected filter + media type toggle
- Empty states: no collection → CTA to Search/Ask; filter yields nothing → "No results found"
- Hook: `useCollectionHome.ts` fetches collection, groups by status, applies filters

### Phase 3: TMDB Integration & Search

**Goal**: Search mode, TMDB proxy routes, show tile rendering with collection indicators.

#### 3.1 TMDB Proxy Routes
- Implement all API routes in `app/api/tmdb/` and `app/api/search/`
- All routes read `TMDB_API_KEY` server-side
- Response mapping through `tmdbMapper.ts`

#### 3.2 Search Mode (`FindDiscover` → `SearchMode`)
- Text input with debounced search
- Results in poster grid via `ShowTile` components
- In-collection items marked with badge indicator
- Selecting a show navigates to `/show/[id]`
- "Search on Launch" setting support: if enabled, auto-focus search on app open
- Hook: `useSearchMode.ts` manages query state, TMDB search calls, collection cross-reference

#### 3.3 Find/Discover Hub (`FindDiscover`)
- Mode switcher tabs: Search | Ask | Alchemy
- Remembers last active mode within session
- Hook: `useFindDiscover.ts` manages active mode

### Phase 4: Show Detail

**Goal**: Full show detail page with all sections, My Data controls, and saving logic.

#### 4.1 Show Detail Page (`ShowDetail`)
- Fetches full TMDB details + existing user data from Supabase
- Merges per merge rules before rendering
- Sections rendered in narrative hierarchy order

#### 4.2 Header Media (`HeaderMedia`)
- Backdrop/poster/logo carousel
- Trailer playback inline when video data available
- Graceful fallback to poster-only layout
- Hook: `useHeaderMedia.ts` selects best media assets

#### 4.3 Core Facts (`CoreFacts`)
- Year, runtime (movies) or seasons/episodes (TV), genres
- Community score bar (vote average)

#### 4.4 My Relationship Controls (`MyRelationship`)
- **Status chips** (toolbar): Active, Interested, Excited, Later, Wait, Done, Quit
  - Selecting Interested/Excited sets `myStatus=later` + `myInterest=interested|excited`
  - Selecting a status on unsaved show → auto-save with that status
  - Reselecting active status → removal confirmation dialog
  - Removal clears all My Data (status, interest, tags, rating, scoop)
  - Confirmation dialog has "don't ask again" option (tracked in UI state)
- **Rating bar**: slider 0-10, rating unsaved show auto-saves as `Done`
- **Tags**: display + picker, adding tag to unsaved show auto-saves as `Later + Interested`
- Hook: `useMyRelationship.ts` implements all saving triggers and default values
- Hook: `useShowActions.ts` handles save/update/remove API calls with optimistic updates

#### 4.5 Overview + Scoop (`ScoopSection`)
- Overview text rendered directly
- Scoop toggle button:
  - No scoop: "Give me the scoop!"
  - Cached scoop (< 4 hours): "Show the scoop"
  - Open: shows "The Scoop" heading + content
- Scoop generation streams progressively (shows "Generating..." indicator)
- Scoop persisted only if show is in collection
- Freshness: regenerate after 4 hours on demand
- Hook: `useScoop.ts` manages scoop state, streaming, caching logic

#### 4.6 Ask About This Show
- CTA button below overview
- Navigates to Find → Ask mode, seeding conversation with show context

#### 4.7 Traditional Recommendations (`ShowStrand`)
- Horizontal scrollable row of similar/recommended shows from TMDB
- In-collection indicators on tiles

#### 4.8 Explore Similar (`ExploreSimilar`)
- Flow: Get Concepts → select concepts (chips, max 8) → Explore Shows
- Get Concepts: calls AI concepts endpoint, renders as selectable `ConceptChip` components
- Explore Shows: calls AI recs endpoint with selected concepts, renders results as `ShowTile` list with per-show reason text
- 5 recommendations per round
- Hook: `useExploreSimilar.ts` manages 3-step flow state

#### 4.9 Streaming Providers (`StreamingProviders`)
- Grouped by type: flatrate (stream), rent, buy
- Provider logos fetched from TMDB provider metadata

#### 4.10 Cast & Crew (`CastCrew`)
- Horizontal strand of person cards with photo + name + role
- Tapping navigates to `/person/[id]`

#### 4.11 Seasons (TV only) (`SeasonsSection`)
- Season list with episode counts
- Only renders for TV shows

#### 4.12 Budget vs Revenue (movies) (`BudgetRevenue`)
- Simple display when data available

### Phase 5: AI Surfaces

**Goal**: Ask chat, Alchemy, AI prompts and streaming.

#### 5.1 AI Client & Prompts
- `lib/ai/aiClient.ts`: OpenAI-compatible client supporting streaming
- Implement all prompt builders per Section 3.2 above
- API routes in `app/api/ai/` that:
  - Accept structured context from client
  - Build prompts
  - Stream responses back
  - Include user library context for taste-awareness

#### 5.2 Ask Mode (`AskMode`)
- Chat UI with user/assistant message bubbles
- Welcome view: 6 random starter prompts from pool of ~80, with refresh button
- Streaming assistant responses
- Mentioned shows: parse `showList` from AI response, resolve via TMDB, render in `MentionedShowsStrip`
- Conversation context: include recent turns, summarize older turns (>10) via summarization prompt
- "Ask about this show" entry point: receive show context from Detail page, seed first system message
- Hook: `useAskChat.ts` manages message history, streaming, context window
- Hook: `useAskMentions.ts` parses and resolves mentioned shows

#### 5.3 Alchemy Mode (`AlchemyMode`)
- Step 1: `ShowPicker` — search and select 2+ shows from library + global catalog
- Step 2: Tap "Conceptualize Shows" → calls concepts API with multiple shows → `ConceptSelector` renders shared concepts as chips (select 1-8)
- Step 3: Tap "ALCHEMIZE!" → calls alchemy recs API → `AlchemyResults` renders 6 recommendations with concept-grounded reasons
- Chaining: "More Alchemy!" uses results as new inputs for another round
- Backtracking: changing shows clears concepts/results; changing concepts clears results
- Step indicators for clarity
- Hook: `useAlchemy.ts` manages multi-step flow state and API orchestration

### Phase 6: Person Detail & Settings

**Goal**: Person page, settings, export.

#### 6.1 Person Detail (`PersonDetail`)
- Fetch from TMDB person endpoint (bio, images, combined credits)
- `PersonHeader`: image gallery, name, bio
- `PersonAnalytics`: lightweight charts — average project ratings, top genres, projects by year (using simple SVG or a lightweight chart library)
- `Filmography`: credits grouped by year, tapping a credit navigates to `/show/[id]`
- Hook: `usePersonDetail.ts` fetches and structures person data

#### 6.2 Settings Page (`SettingsPage`)
- **App Settings** (`AppSettings`): font size selector (XS–XXL), "Search on Launch" toggle
- **User Settings** (`UserSettings`): username field (synced via cloud settings)
- **AI Settings** (`AISettings`): AI API key input, AI model selector
- **Integrations**: TMDB API key input (if user wants their own)
- **Data Management** (`DataManagement`):
  - "Export My Data" button → generates ZIP containing JSON of all shows + settings with ISO-8601 dates
  - Downloads via browser
- Hook: `useSettingsPage.ts` manages settings reads/writes

### Phase 7: Polish & Cross-Cutting

**Goal**: Tile indicators, data export, error handling, testing, responsive design.

#### 7.1 Show Tile Indicators
- In-collection badge when `myStatus` exists
- User rating indicator when `myScore` exists
- Applied globally via `ShowTile` component

#### 7.2 Data Export
- `app/api/export/route.ts`: Server-side ZIP generation of user's full collection + settings as JSON
- ISO-8601 date encoding throughout
- Client triggers download

#### 7.3 Error Handling & Loading States
- Skeleton loaders for show grids, detail sections
- Error boundaries at page level
- Toast notifications for save/remove actions
- AI streaming error recovery (retry once, then show error message)

#### 7.4 Responsive Design
- Sidebar collapses to hamburger on mobile
- Show grids adapt column count
- Detail page sections stack vertically on narrow screens
- Touch-friendly tap targets

#### 7.5 Testing
- Unit tests for critical logic:
  - `showMerge.ts`: merge rules, timestamp resolution, selectFirstNonEmpty
  - `tmdbMapper.ts`: field mapping, edge cases (missing fields, unknown types)
  - `aiParser.ts`: showList parsing, concept parsing, malformed input handling
  - Save trigger logic: auto-save defaults, removal semantics
- API route tests with mocked Supabase/TMDB/AI
- `npm run test:reset`: scoped data cleanup for test namespace

#### 7.6 Data Continuity
- `app_metadata` table tracks `data_model_version`
- Migration scripts handle schema evolution
- Existing user data preserved across updates

---

## 5. Environment & Configuration

### 5.1 `.env.example`

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key  # server-only

# TMDB
TMDB_API_KEY=your-tmdb-api-key

# AI (OpenAI-compatible)
AI_API_KEY=your-ai-api-key
AI_MODEL=gpt-4o
AI_API_BASE_URL=https://api.openai.com/v1  # optional override

# Identity & Isolation
NAMESPACE_ID=benchmark-run-001
DEFAULT_USER_ID=default-user

# App
NODE_ENV=development
```

### 5.2 Security Rules
- `SUPABASE_SERVICE_ROLE_KEY` never imported in client-side code
- `TMDB_API_KEY` and `AI_API_KEY` never exposed to browser — all calls proxied through API routes
- `.env*` files excluded from git (except `.env.example`)
- Dev identity injection (`X-User-Id` header) gated on `NODE_ENV === 'development'`

---

## 6. API Route Summary

| Route | Method | Purpose |
|---|---|---|
| `/api/shows` | GET | Fetch user's collection (with filters) |
| `/api/shows` | POST | Save a show to collection |
| `/api/shows` | DELETE | Remove a show (clear all My Data) |
| `/api/shows/[id]` | GET | Fetch single show (Supabase + TMDB merge) |
| `/api/shows/[id]` | PATCH | Update My Data fields |
| `/api/search` | GET | Search TMDB catalog |
| `/api/tmdb/details/[id]` | GET | Full TMDB show details |
| `/api/tmdb/credits/[id]` | GET | Cast/crew for a show |
| `/api/tmdb/providers/[id]` | GET | Streaming availability |
| `/api/tmdb/recommendations/[id]` | GET | Traditional recs |
| `/api/tmdb/person/[id]` | GET | Person details + filmography |
| `/api/ai/scoop` | POST | Generate AI Scoop (streaming) |
| `/api/ai/ask` | POST | Ask chat turn (streaming) |
| `/api/ai/concepts` | POST | Generate concepts for show(s) |
| `/api/ai/alchemy` | POST | Concept-based recommendations |
| `/api/settings` | GET/PUT | Cloud settings CRUD |
| `/api/export` | GET | Export user data as ZIP |

---

## 7. Data Flow Diagrams

### 7.1 Save Show Flow

```
User action (set status / rate / add tag)
  → useMyRelationship determines defaults:
      - Status set explicitly? Use it.
      - Rating on unsaved show? Default status = Done.
      - Tag on unsaved show? Default status = Later, interest = Interested.
      - No explicit status? Default = Later, interest = Interested.
  → POST /api/shows with show data + user overlay
  → API route: merge with existing (if any) per merge rules
  → Upsert to Supabase (namespace_id, user_id, id)
  → Return merged show
  → Client updates cache optimistically
```

### 7.2 Remove Show Flow

```
User reselects active status chip
  → Confirmation dialog (with "don't ask again" option)
  → If confirmed: DELETE /api/shows with show id
  → API route: delete row from Supabase (scoped to namespace + user)
  → All My Data cleared (status, interest, tags, rating, scoop)
  → Client removes from cache
```

### 7.3 AI Ask Flow

```
User types message
  → useAskChat builds context:
      - System prompt (personality)
      - User library summary
      - Recent conversation history (last ~10 turns)
      - Older turns as summary (if >10)
      - Optional: handoff show context
  → POST /api/ai/ask (streaming)
  → API route builds prompt messages → streams AI response
  → Client parses streaming chunks → renders incrementally
  → On complete: parse showList from structured output
  → useAskMentions resolves each entry via TMDB
  → MentionedShowsStrip renders resolved shows
```

### 7.4 Alchemy Flow

```
Step 1: User selects 2+ shows via ShowPicker
Step 2: "Conceptualize Shows"
  → POST /api/ai/concepts with all selected shows
  → Returns 8+ shared concepts
  → User selects 1-8 concepts
Step 3: "ALCHEMIZE!"
  → POST /api/ai/alchemy with selected concepts + input shows + user library
  → Returns 6 recommendations with reasons
  → User can save any rec, or "More Alchemy!" to chain
```

---

## 8. Implementation Order & Dependencies

```
Phase 1: Foundation
  ├── 1.1 Project setup (no deps)
  ├── 1.2 Supabase schema (no deps)
  ├── 1.3 Identity middleware (depends on 1.1, 1.2)
  ├── 1.4 Theme & shared components (depends on 1.1)
  ├── 1.5 Core data hooks (depends on 1.2, 1.3)
  └── 1.6 Scripts (depends on 1.1, 1.2)

Phase 2: Collection & Navigation (depends on Phase 1)
  ├── 2.1 App shell
  ├── 2.2 Filter sidebar
  └── 2.3 Collection home

Phase 3: TMDB & Search (depends on Phase 1)
  ├── 3.1 TMDB proxy routes
  ├── 3.2 Search mode
  └── 3.3 Find/Discover hub

Phase 4: Show Detail (depends on Phase 2, 3)
  ├── 4.1-4.3 Show detail + header + facts
  ├── 4.4-4.5 My Relationship + Scoop (depends on AI client from 5.1)
  ├── 4.6-4.7 Ask CTA + traditional recs
  ├── 4.8 Explore Similar (depends on 5.1)
  └── 4.9-4.12 Providers, cast, seasons, budget

Phase 5: AI Surfaces (depends on Phase 1, 3)
  ├── 5.1 AI client & prompts
  ├── 5.2 Ask mode (depends on 5.1)
  └── 5.3 Alchemy mode (depends on 5.1)

Phase 6: Person & Settings (depends on Phase 3)
  ├── 6.1 Person detail
  └── 6.2 Settings + export

Phase 7: Polish (depends on all above)
  ├── 7.1-7.2 Tile indicators + export
  ├── 7.3-7.4 Error handling + responsive
  ├── 7.5 Testing
  └── 7.6 Data continuity
```

Phases 2 and 3 can proceed in parallel. Phase 5 can begin as soon as Phase 1 is complete (AI client work is independent of UI). Phase 6 can proceed in parallel with Phase 4/5.

---

## 9. Key Business Rules Checklist

- [ ] Show is "in collection" when `myStatus` is non-null
- [ ] Setting any status saves the show
- [ ] Choosing Interested/Excited sets `myStatus=later` + `myInterest`
- [ ] Rating unsaved show → auto-save as `Done`
- [ ] Adding tag to unsaved show → auto-save as `Later + Interested`
- [ ] Default save without explicit status → `Later + Interested`
- [ ] Removing status clears ALL My Data (status, interest, tags, rating, scoop)
- [ ] Removal requires confirmation (with "don't ask again" option)
- [ ] Re-adding preserves latest My Data; refreshes public metadata
- [ ] Every user field tracks modification timestamp
- [ ] AI Scoop persisted only if show is in collection; 4-hour freshness
- [ ] Alchemy results and Ask chat history are session-only
- [ ] AI recommendations resolve to real TMDB shows
- [ ] All data scoped by `(namespace_id, user_id)`
- [ ] Clearing client storage must not lose user data
- [ ] Export produces ZIP with JSON backup, ISO-8601 dates
- [ ] Concepts: 1-3 words, evocative, no generics, 8 per request
- [ ] Explore Similar: 5 recs; Alchemy: 6 recs
- [ ] AI stays in TV/movies domain, spoiler-safe by default
- [ ] User's version of a show takes precedence everywhere
