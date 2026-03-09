# Implementation Plan: Personal TV & Movie Companion

## 1. Technology Stack & Project Foundation

- **Framework**: Next.js 15 (App Router, Server Components by default)
- **Persistence**: Supabase (PostgreSQL + official client SDK)
- **Styling**: Tailwind CSS + shadcn/ui component primitives
- **External Catalog**: TMDB API v3 (TV/movie metadata, images, credits, streaming providers)
- **AI Provider**: OpenAI API (configurable model via settings)
- **Language**: TypeScript throughout
- **Package Manager**: npm

### 1.1 Project Structure

```
/
├── .env.example
├── .gitignore
├── supabase/
│   └── migrations/               # Numbered SQL migrations
├── src/
│   ├── app/                      # Next.js App Router
│   │   ├── layout.tsx            # Root layout (sidebar + nav shell)
│   │   ├── page.tsx              # Collection Home
│   │   ├── show/[id]/
│   │   │   └── page.tsx          # Show Detail
│   │   ├── person/[id]/
│   │   │   └── page.tsx          # Person Detail
│   │   ├── find/
│   │   │   ├── layout.tsx        # Find hub with mode switcher
│   │   │   ├── search/page.tsx   # Search mode
│   │   │   ├── ask/page.tsx      # Ask mode
│   │   │   └── alchemy/page.tsx  # Alchemy mode
│   │   ├── settings/
│   │   │   └── page.tsx          # Settings & Your Data
│   │   └── api/
│   │       ├── shows/            # Collection CRUD
│   │       ├── ai/               # AI proxy routes (server-only)
│   │       ├── catalog/          # TMDB proxy routes
│   │       └── export/           # Data export endpoint
│   ├── lib/
│   │   ├── supabase/
│   │   │   ├── client.ts         # Browser Supabase client (anon key)
│   │   │   ├── server.ts         # Server Supabase client (service role)
│   │   │   └── types.ts          # Generated/manual DB types
│   │   ├── tmdb/
│   │   │   ├── client.ts         # TMDB API wrapper
│   │   │   ├── mapper.ts         # TMDB response → Show mapping
│   │   │   ├── image-utils.ts    # Image URL construction
│   │   │   └── types.ts          # TMDB response types
│   │   ├── ai/
│   │   │   ├── client.ts         # OpenAI client factory
│   │   │   ├── resolve-shows.ts  # AI output → real catalog resolution
│   │   │   └── prompts/
│   │   │       ├── scoop.ts      # Scoop system + user prompt
│   │   │       ├── ask.ts        # Ask persona + library context
│   │   │       ├── concepts.ts   # Concept extraction (single/multi)
│   │   │       ├── recommendations.ts  # Concept-based rec prompt
│   │   │       └── summarize.ts  # Conversation summarizer
│   │   ├── identity/
│   │   │   ├── namespace.ts      # Namespace resolution
│   │   │   └── user.ts           # User identity (dev injection)
│   │   ├── merge/
│   │   │   └── show-merge.ts     # Catalog ↔ stored show merge
│   │   ├── shows/
│   │   │   ├── auto-save.ts      # Implicit save trigger logic
│   │   │   └── remove.ts         # Removal + confirmation logic
│   │   └── filters/
│   │       └── index.ts          # Filter types, builders, persistence
│   ├── components/
│   │   ├── layout/               # AppShell, Sidebar, NavBar
│   │   ├── show/                 # ShowTile, ShowGrid, StatusChips, RatingBar, TagPicker
│   │   ├── detail/               # Header carousel, sections, Scoop, ExploreSimlar
│   │   ├── find/                 # SearchResults, ChatUI, AlchemyFlow, ModeSwitcher
│   │   ├── person/               # PersonHeader, Filmography, AnalyticsCharts
│   │   └── ui/                   # shadcn primitives (Button, Dialog, Input, etc.)
│   ├── hooks/
│   │   ├── use-show-mutation.ts  # Optimistic show save/update/remove
│   │   ├── use-filters.ts        # Filter state + persistence
│   │   └── use-ai-stream.ts      # Streaming AI response hook
│   └── types/
│       ├── show.ts               # Show, MyStatusType, MyInterestType
│       ├── person.ts             # Person, Credit
│       ├── filter.ts             # FilterConfiguration, FilterType
│       └── ai.ts                 # AI request/response types
├── scripts/
│   ├── test-reset.ts             # Reset test data for a namespace
│   └── seed.ts                   # Optional seed data
└── tests/
```

### 1.2 Rationale

Next.js App Router provides the server/client component boundary needed to keep API keys server-side while enabling rich client interactivity (chat UIs, drag/select flows). Supabase gives PostgreSQL with RLS for namespace/user isolation out of the box. TMDB is the standard open catalog API for movies and TV. shadcn/ui provides accessible, themeable primitives without heavy runtime cost.

---

## 2. Database Schema (Supabase / PostgreSQL)

### 2.1 `shows` Table

The primary entity. Stores both catalog metadata and the user's personal overlay ("My Data"). Each row is uniquely identified by `(namespace_id, user_id, id)` where `id` is the catalog identifier (e.g., TMDB ID as string).

| Column | Type | Constraints / Default | Purpose |
|---|---|---|---|
| `id` | `text` | NOT NULL | Catalog item identifier |
| `namespace_id` | `text` | NOT NULL | Build/run isolation |
| `user_id` | `text` | NOT NULL | Owner identity |
| `title` | `text` | NOT NULL | Display title |
| `show_type` | `text` | NOT NULL; CHECK in (`movie`,`tv`,`person`,`unknown`) | Media type |
| `external_ids` | `jsonb` | | Optional external identifiers |
| `overview` | `text` | | Catalog overview/description |
| `genres` | `text[]` | DEFAULT `'{}'` | Genre name array |
| `tagline` | `text` | | |
| `homepage` | `text` | | |
| `original_language` | `text` | | ISO language code |
| `spoken_languages` | `text[]` | DEFAULT `'{}'` | ISO 639-1 codes |
| `languages` | `text[]` | DEFAULT `'{}'` | Catalog language codes |
| `poster_url` | `text` | | Full poster image URL |
| `backdrop_url` | `text` | | Full backdrop image URL |
| `logo_url` | `text` | | Best-rated logo URL |
| `vote_average` | `double precision` | | Community score |
| `vote_count` | `integer` | | |
| `popularity` | `double precision` | | |
| `last_air_date` | `timestamptz` | | TV: last episode air date |
| `first_air_date` | `timestamptz` | | TV: first episode air date |
| `release_date` | `timestamptz` | | Movie: theatrical release |
| `runtime` | `integer` | | Movie: minutes |
| `budget` | `bigint` | | Movie: production budget |
| `revenue` | `bigint` | | Movie: box office |
| `series_status` | `text` | | TV: "Returning Series", etc. |
| `number_of_episodes` | `integer` | | TV |
| `number_of_seasons` | `integer` | | TV |
| `episode_run_time` | `integer[]` | DEFAULT `'{}'` | TV |
| `my_tags` | `text[]` | DEFAULT `'{}'` | User's free-form tags |
| `my_tags_update_date` | `timestamptz` | | |
| `my_score` | `double precision` | | User rating |
| `my_score_update_date` | `timestamptz` | | |
| `my_status` | `text` | CHECK in (`active`,`next`,`later`,`done`,`quit`,`wait`) | Relationship status |
| `my_status_update_date` | `timestamptz` | | |
| `my_interest` | `text` | CHECK in (`excited`,`interested`) | Priority within Later |
| `my_interest_update_date` | `timestamptz` | | |
| `ai_scoop` | `text` | | Cached AI personality review |
| `ai_scoop_update_date` | `timestamptz` | | |
| `details_update_date` | `timestamptz` | | Last catalog data refresh |
| `creation_date` | `timestamptz` | DEFAULT `now()` | First persisted |
| `is_test` | `boolean` | DEFAULT `false` | Test data marker |
| `provider_data` | `jsonb` | | Streaming availability by region |

**Primary key**: `(namespace_id, user_id, id)`

### 2.2 `cloud_settings` Table

Per-user synced application settings. Keyed by `(namespace_id, user_id)`.

| Column | Type | Default | Purpose |
|---|---|---|---|
| `id` | `text` | `'globalSettings'` | Settings document key |
| `namespace_id` | `text` | NOT NULL | Build isolation |
| `user_id` | `text` | NOT NULL | Owner |
| `user_name` | `text` | | Display name |
| `version` | `double precision` | | Epoch seconds for conflict resolution |
| `catalog_api_key` | `text` | | User-provided TMDB key |
| `ai_api_key` | `text` | | User-provided AI key |
| `ai_model` | `text` | `'gpt-4o'` | Selected AI model |

**Primary key**: `(namespace_id, user_id, id)`

### 2.3 `app_metadata` Table

Tracks data model version for safe migrations across updates.

| Column | Type | Default |
|---|---|---|
| `namespace_id` | `text` PK | |
| `data_model_version` | `integer` | `3` |

### 2.4 Indexes

```sql
CREATE INDEX idx_shows_ns_user ON shows (namespace_id, user_id);
CREATE INDEX idx_shows_ns_user_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_ns_user_test ON shows (namespace_id, user_id, is_test);
```

### 2.5 Row-Level Security

RLS is enabled on all tables. In benchmark/dev mode, the server-side Supabase client uses the service role key and explicitly filters queries by `namespace_id` + `user_id`. This avoids coupling RLS to Supabase Auth during benchmark mode while preserving the constraint surface. Upgrading to real auth later means adding RLS policies that read `auth.uid()` and restricting the service role to admin paths only -- no schema changes.

---

## 3. Identity & Isolation Layer

### 3.1 Namespace Resolution (`src/lib/identity/namespace.ts`)

Every server-side query includes `namespace_id` in its WHERE clause. The value is resolved in priority order:

1. `NEXT_PUBLIC_NAMESPACE_ID` environment variable (primary)
2. Fallback: generate a deterministic ID from the repo/build context

The namespace is injected into all API route handlers via a shared `getNamespaceId()` helper. Client-side code reads `NEXT_PUBLIC_NAMESPACE_ID` for display/debugging only.

### 3.2 User Identity (`src/lib/identity/user.ts`)

In benchmark/dev mode:

- Default user ID read from `DEFAULT_USER_ID` env var
- API routes accept an `X-User-Id` header override, gated by `NODE_ENV !== 'production'`
- Settings page includes a dev-only "Switch User" input for testing multi-user scenarios

`getUserId(request)` helper resolves the effective user for every API call.

### 3.3 Combined Partition

All data access is scoped to `(namespace_id, user_id)`. This is the effective partition key across all tables. Two different namespaces never see each other's data. Within a namespace, multiple users may exist and are independently isolated.

### 3.4 Auth Migration Path

`user_id` is an opaque string with no Supabase Auth dependency. Replacing dev identity with real OAuth means:

1. Wire `auth.uid()` into the `getUserId()` helper
2. Add RLS policies referencing `auth.uid()`
3. Remove the `X-User-Id` header path

No schema changes, no data migration.

---

## 4. External Catalog Integration (TMDB)

### 4.1 TMDB Client (`src/lib/tmdb/client.ts`)

Wraps TMDB API v3 with typed methods:

- `searchMulti(query)` -- combined movie + TV search
- `getMovieDetails(id, appendToResponse)` -- full movie details with credits, videos, images, recommendations, similar, providers
- `getTvDetails(id, appendToResponse)` -- full TV details with same appended responses
- `getPersonDetails(id, appendToResponse)` -- person bio + combined credits
- `getProviders(mediaType, id)` -- streaming availability

API key resolution: env `TMDB_API_KEY` first, then user's stored `catalog_api_key` from `cloud_settings`.

All TMDB calls are server-side only (via API routes) to keep the key off the client.

### 4.2 Response Mapping (`src/lib/tmdb/mapper.ts`)

Transforms TMDB responses into the app's `Show` shape:

**`mapTmdbMovieToShow(tmdbMovie): Partial<Show>`**
- `id` ← TMDB movie `id` (as string)
- `title` ← `title` (required; reject if missing)
- `show_type` ← `'movie'`
- `release_date` ← parsed from `release_date` string
- `runtime`, `budget`, `revenue` ← direct
- `genres` ← map TMDB genre objects to name strings
- Image URLs ← constructed using TMDB image base URL + file paths
- `logo_url` ← from `images.logos`, pick highest-voted English logo, fallback to first available
- `provider_data` ← structured from `watch/providers` response

**`mapTmdbTvToShow(tmdbTv): Partial<Show>`**
- `id` ← TMDB TV `id` (as string)
- `title` ← `name` (required; reject if missing)
- `show_type` ← `'tv'`
- `first_air_date`, `last_air_date` ← parsed
- `series_status` ← `status`
- `number_of_episodes`, `number_of_seasons` ← direct
- Same image/genre/provider logic as movies

**`mapTmdbPersonToPerson(tmdbPerson): Person`**
- Bio, profile images, known-for department
- Combined credits mapped to filmography entries

### 4.3 Image Utilities (`src/lib/tmdb/image-utils.ts`)

- `buildImageUrl(path, size)` -- constructs full URL from TMDB base + size + path
- Standard sizes: `w185` (tile poster), `w500` (detail poster), `w780` (backdrop), `original` (full res)
- Null-safe: returns undefined for null paths

### 4.4 Catalog API Routes

**`GET /api/catalog/search?q={query}&page={page}`**
- Calls `searchMulti`, maps results, checks each against user's stored shows to set `inCollection` flag
- Returns: `{ results: Show[], totalPages: number }`

**`GET /api/catalog/show/[id]?type={movie|tv}`**
- Calls `getMovieDetails` or `getTvDetails` with all append_to_response fields
- Maps to Show + transient data (cast, crew, seasons, videos, images, recommendations, similar, providers)
- If user has this show stored, merges stored My Data onto the response

**`GET /api/catalog/person/[id]`**
- Calls `getPersonDetails` with combined credits
- Returns mapped Person with filmography

---

## 5. Show Merge Logic (`src/lib/merge/show-merge.ts`)

When fresh catalog data arrives for a show that already exists in the user's collection, the merge algorithm preserves user data while updating catalog fields:

### 5.1 Non-My Fields: `selectFirstNonEmpty(newValue, oldValue)`

```
function selectFirstNonEmpty<T>(newVal: T | null, oldVal: T | null): T | null {
  if (newVal is non-empty) return newVal
  return oldVal   // keep existing even if new is empty/null
}
```

Applied to: `overview`, `genres`, `tagline`, `poster_url`, `backdrop_url`, `logo_url`, `vote_average`, `spoken_languages`, `provider_data`, and all other catalog-sourced fields.

Rule: never overwrite a non-empty stored value with an empty/null incoming value.

### 5.2 My Fields: Timestamp-Based Resolution

For `my_tags`, `my_score`, `my_status`, `my_interest`:

```
if both sides have update dates → keep the side with the newer date
if only one side has an update date → keep that side
if neither has a date → keep stored value (existing wins by default)
```

This handles cloud sync conflict resolution and prevents catalog refreshes from overwriting user edits.

### 5.3 Management Fields

- `details_update_date` ← set to `now()` after every merge
- `creation_date` ← preserved from original row; never overwritten on catalog refresh

---

## 6. Auto-Save & Removal Business Rules

### 6.1 Auto-Save Triggers (`src/lib/shows/auto-save.ts`)

These actions implicitly save an unsaved show to the collection:

| Trigger | Resulting Status | Resulting Interest |
|---|---|---|
| Set any status | The chosen status | (none unless Later) |
| Choose "Interested" chip | `later` | `interested` |
| Choose "Excited" chip | `later` | `excited` |
| Rate an unsaved show | `done` | (none) |
| Add a tag to an unsaved show | `later` | `interested` |

When auto-saving, the show's catalog data is fetched/mapped from TMDB and persisted alongside the user data in a single upsert.

### 6.2 Removal Flow (`src/lib/shows/remove.ts`)

Trigger: user reselects the currently active status chip.

1. Show confirmation dialog: "Remove [title] from your collection? This clears all your data for this show."
2. Track `statusRemovalCountKey` (increment each confirmation)
3. After a threshold (e.g., 3), offer "Don't ask again" checkbox → sets `hideStatusRemovalConfirmation = true`
4. If `hideStatusRemovalConfirmation` is already true, skip the dialog
5. On confirm: `DELETE FROM shows WHERE namespace_id = $ns AND user_id = $uid AND id = $showId`
6. All My Data is cleared (status, interest, tags, rating, AI Scoop) because the row is deleted

### 6.3 Interest ↔ Status Coupling

- Interest (`excited`/`interested`) only applies when `my_status = 'later'`
- If status changes away from `later`, interest becomes irrelevant but MAY be retained in the row for when the show returns to `later`
- UI only displays interest chips as selectable when status is `later` or when setting status to `later`

---

## 7. AI Integration Layer

### 7.1 Architecture

All AI calls are server-side API routes under `src/app/api/ai/`. The AI key never reaches the browser. The pipeline:

```
Client request
  → API route (validates identity, builds prompt)
    → AI provider call (OpenAI SDK)
      → Parse structured output
        → Resolve show references against TMDB
          → Return enriched response to client
```

### 7.2 AI Client Factory (`src/lib/ai/client.ts`)

- Creates OpenAI client instance
- Key resolution: env `AI_API_KEY` → user's stored `ai_api_key` from `cloud_settings`
- Model resolution: env `AI_MODEL` → user's stored `ai_model`
- Supports streaming responses (for Scoop and Ask)

### 7.3 Prompt Builders

All prompts share the base personality defined in the voice spec: fun, chatty TV/movie nerd friend who is joy-forward, opinionated, vibe-first, specific, and concise-by-default.

**`src/lib/ai/prompts/scoop.ts`**

System prompt establishes:
- Personality: gossipy, vivid, useful mini blog-post of taste
- Structure: personal take → honest stack-up → "The Scoop" centerpiece → fit/warnings → "Worth it?" verdict
- Constraints: spoiler-safe, 150-350 words, never generic
- Tone sliders: 70% friend / 30% critic, 60% hype / 40% measured

User prompt includes: show title, year, genres, overview, community score, media type.

**`src/lib/ai/prompts/ask.ts`**

System prompt establishes:
- Personality: friend-in-dialogue, picks favorites, adapts depth to question
- Output format: structured JSON with `commentary` (user-facing text) and `showList` (machine-readable `Title::externalId::mediaType;;...` format)
- Constraints: stay in TV/movies, spoiler-safe, bulleted lists for multi-recs, 1-3 tight paragraphs
- Library context injection: summary of user's collection (titles, statuses, ratings, tags) for taste-awareness

Variant for "Ask About a Show": prepends show context (title, overview, genres, user's status/rating) to seed the conversation.

**`src/lib/ai/prompts/concepts.ts`**

Two variants:

*Single-show* (Explore Similar):
- Input: show title, genres, overview, year
- Output: exactly 8 concepts as a bullet list
- Constraints: 1-3 words each, evocative, no explanation, no plot/spoilers, ordered by strength, diverse across axes (structure/vibe/emotion/craft)

*Multi-show* (Alchemy):
- Input: 2+ show titles with genres/overviews
- Output: concept bullet list (larger pool than single-show, ~10-12)
- Constraint: concepts must represent shared commonality across ALL input shows

**`src/lib/ai/prompts/recommendations.ts`**

- Input: selected concepts, source show(s), user's library (to avoid recommending already-saved shows)
- Output: structured list of 5 (Explore Similar) or 6 (Alchemy) recommendations, each with: `title`, `externalId` (TMDB ID), `mediaType`, `reason`
- Constraints: reasons cite specific selected concepts, recent bias but classics allowed, all titles must be real

**`src/lib/ai/prompts/summarize.ts`**

- Input: older conversation turns
- Output: 1-2 sentence summary preserving the same persona tone
- Purpose: compress chat history after ~10 messages to control token depth while preserving feel

### 7.4 Show Resolution Pipeline (`src/lib/ai/resolve-shows.ts`)

When AI returns show references (Ask mentions or recommendations):

1. Parse the structured output to extract `title`, `externalId`, `mediaType`
2. If `externalId` is present: look up TMDB by that ID, verify title matches case-insensitively
3. If TMDB lookup succeeds: map to a full Show object, attach the AI `reason` as transient data
4. If `externalId` lookup fails or is missing: fall back to TMDB search by title, accept first result with case-insensitive title match
5. If resolution completely fails: mark as non-interactive (title-only display) or hand off to Search

### 7.5 AI API Routes

**`POST /api/ai/scoop`**
- Body: `{ showId, title, year, genres, overview, communityScore, mediaType }`
- Checks `ai_scoop_update_date`: if cached and < 4 hours old, returns cached scoop
- Otherwise: calls AI, streams response, persists scoop + timestamp if show is in collection
- Response: streamed text (SSE or ReadableStream)

**`POST /api/ai/ask`**
- Body: `{ messages: ChatMessage[], handoffShow?: ShowContext }`
- Builds conversation with system prompt + library context + message history (with summarization if > 10 messages)
- Calls AI with structured output format
- Parses `showList`, resolves each show against TMDB
- Response: `{ commentary: string, mentionedShows: Show[] }`

**`POST /api/ai/concepts`**
- Body: `{ shows: ShowContext[], mode: 'single' | 'multi' }`
- Calls AI with appropriate prompt variant
- Response: `{ concepts: string[] }`

**`POST /api/ai/recommendations`**
- Body: `{ concepts: string[], sourceShows: ShowContext[], mode: 'explore' | 'alchemy' }`
- Calls AI (5 recs for explore, 6 for alchemy)
- Resolves each recommendation against TMDB
- Response: `{ recommendations: Array<{ show: Show, reason: string }> }`

### 7.6 Structured Output Parsing & Fallbacks

- Primary: request JSON mode from OpenAI for structured outputs
- If parsing fails: retry once with stricter formatting instructions in the prompt
- If retry fails: return unstructured commentary + hand off mentioned shows to Search
- `showList` format parser: split on `;;`, then split each entry on `::` → `[title, externalId, mediaType]`

---

## 8. UI Architecture

### 8.1 Root Layout (`src/app/layout.tsx`)

Desktop-oriented layout with sidebar + main content:

```
┌─────────────────────────────────────────────────┐
│  [App Logo]              [Find/Discover] [⚙]   │
├──────────┬──────────────────────────────────────┤
│ SIDEBAR  │                                      │
│          │           MAIN CONTENT               │
│ All Shows│                                      │
│ ──────── │                                      │
│ Tag 1    │                                      │
│ Tag 2    │                                      │
│ No Tags  │                                      │
│ ──────── │                                      │
│ Genre ▸  │                                      │
│ Decade ▸ │                                      │
│ Score ▸  │                                      │
│          │                                      │
│ [All|Mov │                                      │
│  ies|TV] │                                      │
└──────────┴──────────────────────────────────────┘
```

- Sidebar: filter navigation (All Shows, tag filters, data filters)
- Top nav: persistent Find/Discover and Settings entry points
- Media-type toggle (All / Movies / TV) applies globally on top of any filter

### 8.2 Collection Home (`src/app/page.tsx`)

Fetches user's saved shows for the current namespace + user, filtered by sidebar selection and media type.

**Status-grouped sections:**

1. **Active** — prominent, larger tiles (the user is currently watching these)
2. **Excited** — shows with `my_status = 'later'` AND `my_interest = 'excited'`
3. **Interested** — shows with `my_status = 'later'` AND `my_interest = 'interested'`
4. **Other** — collapsed group containing: Wait, Quit, Done, and any Later items without an interest level

Each section header shows a count. Each tile displays:
- Poster image
- Title
- In-collection indicator badge
- User rating badge (if rated)

**Empty states:**
- No shows at all: "Your collection is empty. Search for shows or Ask for recommendations."
- Filter yields no results: "No results found."

### 8.3 Show Detail Page (`src/app/show/[id]/page.tsx`)

Server component fetches show from TMDB (with all appended data) and merges with stored user data. The page is the single source of truth for a show.

**Sections in order (preserving the hierarchy from the detail page spec):**

1. **Header media carousel** — backdrops, posters, logos. Trailers play inline when available. Graceful fallback to poster/logo only when no backdrops or trailers exist.

2. **Core facts + community score** — year, runtime (movies) or season/episode counts (TV), community score bar.

3. **Tag chips (My Tags)** — display current tags with an "add tag" affordance. Adding a tag to an unsaved show auto-saves as `later` + `interested`.

4. **Overview + Scoop toggle/stream** — overview text shown by default. Scoop toggle below:
   - No scoop generated yet: button reads "Give me the scoop!"
   - Cached scoop exists: button reads "Show the scoop"
   - Scoop expanded: section titled "The Scoop"
   - When generating: streams progressively with "Generating..." indicator
   - Freshness: regenerate after 4 hours on demand

5. **"Ask about this show" CTA** — navigates to `/find/ask` with show context seeded.

6. **Genres + languages** — genre chips, spoken language list.

7. **Recommendations strand** — horizontal scrollable row of TMDB similar/recommended shows. Low-effort next steps for users who don't want AI steering.

8. **Explore Similar (concept discovery)** — three-step inline flow:
   - "Get Concepts" button → calls `/api/ai/concepts` with single-show mode
   - Concept chips appear for selection (1-8)
   - "Explore Shows" button → calls `/api/ai/recommendations` with explore mode → 5 recommendation cards with reasons

9. **Streaming Availability ("Stream It")** — provider logos grouped by availability type (streaming, rent, buy), sourced from TMDB providers data.

10. **Cast & Crew** — horizontal strands of person cards. Tapping opens Person Detail.

11. **Seasons** (TV only) — season list with episode counts.

12. **Budget vs Revenue** (movies, when available) — simple comparison display.

**Status/Interest toolbar** — fixed or sticky toolbar (not in the scroll body) with status chips: Active, Interested, Excited, Wait, Done, Quit. Setting a status saves; reselecting the active status triggers removal confirmation.

**My Rating** — rating slider in the toolbar area. Rating an unsaved show auto-saves as `done`.

### 8.4 Find/Discover Hub (`src/app/find/layout.tsx`)

Mode switcher (segmented control or tabs) at the top: **Search | Ask | Alchemy**

**Search** (`src/app/find/search/page.tsx`)
- Text input with search-on-type (debounced)
- Results in a poster grid
- In-collection shows marked with a badge
- Tapping a result navigates to Show Detail
- If `autoSearch` setting is true, Search is opened on app launch

**Ask** (`src/app/find/ask/page.tsx`)
- Chat interface with user/assistant message bubbles
- Welcome view (no messages yet): display 6 random starter prompts with a refresh button
- Each assistant response that mentions shows renders a "Mentioned Shows" horizontal strip below the message
- Tapping a mentioned show opens Show Detail (or falls back to Search if resolution failed)
- Conversation context retained for the session; after ~10 messages, older turns are summarized automatically
- "Ask About a Show" variant: when navigating from a Show Detail "Ask about..." button, the conversation is seeded with that show's context

**Alchemy** (`src/app/find/alchemy/page.tsx`)
- Multi-step card-based flow:

  **Step 1: Select shows** — search/browse to add 2+ starting shows (from library + global catalog). Show selected shows as removable chips/cards.

  **Step 2: Conceptualize** — "Conceptualize Shows" button calls `/api/ai/concepts` with multi-show mode. Loading state while AI generates. Concept chips appear for selection (max 8).

  **Step 3: Alchemize** — "ALCHEMIZE!" button calls `/api/ai/recommendations` with alchemy mode. 6 recommendation cards appear, each with a short reason citing the selected concepts.

  **Step 4 (optional): Chain** — "More Alchemy!" button: results become new inputs, flow returns to Step 2 with the new show set.

- Backtracking: changing the selected shows clears concepts and results. Changing selected concepts clears results.

### 8.5 Person Detail (`src/app/person/[id]/page.tsx`)

- **Header**: profile image gallery, name, bio
- **Analytics charts** (lightweight, using recharts or similar):
  - Average project ratings distribution
  - Top genres breakdown
  - Projects-by-year timeline
- **Filmography**: credits grouped by year, each credit tappable → Show Detail

### 8.6 Settings (`src/app/settings/page.tsx`)

**App Settings**
- Font size selector: XS, S, M, L, XL, XXL (stored in local settings, applied via CSS custom property)
- "Search on Launch" toggle (stored as `autoSearch`)

**User**
- Username text input (stored in `cloud_settings.user_name`)

**AI**
- AI API key input (stored in `cloud_settings.ai_api_key`, never committed)
- AI model selector dropdown (stored in `cloud_settings.ai_model`)

**Integrations**
- Catalog API key input (stored in `cloud_settings.catalog_api_key`)

**Your Data**
- "Export My Data" button → calls `/api/export` → downloads a `.zip` containing a JSON file with all saved shows and My Data, dates encoded as ISO-8601
- Import/Restore: not implemented (noted as open question)

**Dev-only (gated by NODE_ENV)**
- Current namespace display
- User ID input for identity switching

---

## 9. Sidebar Filter System

### 9.1 Filter Types (`src/lib/filters/index.ts`)

```typescript
type FilterType = 'all' | 'genre' | 'myStatus' | 'communityScore' | 'decade' | 'myTag';

interface FilterConfiguration {
  type: FilterType;
  label: string;
  value: string;
}
```

### 9.2 Filter Sections in Sidebar

**Quick filters:**
- "All Shows" (default, always present)

**Tag filters:**
- One entry per distinct tag across the user's collection
- "No Tags" entry appears if any shows have an empty `my_tags` array
- Dynamically rebuilt when tags change

**Data filters:**
- Genre: expandable list of genres present in the user's collection
- Decade: expandable list of decades (e.g., 2020s, 2010s, 2000s)
- Community Score: ranges (e.g., 90+, 80-89, 70-79, etc.)

### 9.3 Filter Application

All filters translate to SQL WHERE clauses on the `shows` table:
- `all`: no additional clause
- `myTag`: `$tag = ANY(my_tags)` or (for "No Tags") `my_tags = '{}'`
- `genre`: `$genre = ANY(genres)`
- `decade`: date range on `release_date` / `first_air_date`
- `communityScore`: range on `vote_average`

### 9.4 Media-Type Toggle

Applies on top of any filter: `show_type = 'movie'`, `show_type = 'tv'`, or no type clause.

### 9.5 Persistence

`lastSelectedFilter` stored in `localStorage` (keyed by namespace+user). Restored on page load.

---

## 10. Data Export (`src/app/api/export/route.ts`)

- Queries all shows for the current namespace + user
- Serializes as JSON with ISO-8601 dates
- Packages into a `.zip` file using a library like `jszip`
- Returns as a downloadable attachment with `Content-Disposition: attachment; filename="my-data-export.zip"`

---

## 11. Environment & Configuration

### `.env.example`

```bash
# ── Supabase ──
NEXT_PUBLIC_SUPABASE_URL=          # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=     # Supabase public/anon key (client-safe)
SUPABASE_SERVICE_ROLE_KEY=         # Server-only elevated key (NEVER expose to client)

# ── Build Isolation ──
NEXT_PUBLIC_NAMESPACE_ID=          # Unique stable ID for this build/run
DEFAULT_USER_ID=                   # Default user ID for dev/benchmark mode

# ── External Catalog (TMDB) ──
TMDB_API_KEY=                      # TMDB API v3 read access key

# ── AI Provider ──
AI_API_KEY=                        # OpenAI API key (server-only)
AI_MODEL=gpt-4o                    # Default AI model identifier

# ── App Mode ──
NODE_ENV=development               # 'development' enables dev identity injection
```

### `.gitignore`

```
.env
.env.local
.env.*.local
node_modules/
.next/
```

---

## 12. Database Migrations

### Migration 001: Initial Schema (`supabase/migrations/001_initial_schema.sql`)

Creates all three tables, constraints, indexes, and enables RLS:

- `shows` table with all columns, composite PK `(namespace_id, user_id, id)`, CHECK constraints on `show_type`, `my_status`, `my_interest`
- `cloud_settings` table with composite PK `(namespace_id, user_id, id)`
- `app_metadata` table with PK `namespace_id`
- All indexes defined in section 2.4
- RLS enabled on all tables

---

## 13. Scripts & Developer Experience

### `npm run dev`
Starts Next.js development server (`next dev`). Requires `.env.local` with valid Supabase and TMDB credentials.

### `npm test`
Runs the test suite. Tests create data with `is_test = true` and scoped to the configured namespace.

### `npm run test:reset`
Executes `scripts/test-reset.ts`:

```sql
DELETE FROM shows WHERE namespace_id = $NAMESPACE_ID AND is_test = true;
```

For full namespace reset (all data, not just test):
```sql
DELETE FROM shows WHERE namespace_id = $NAMESPACE_ID;
DELETE FROM cloud_settings WHERE namespace_id = $NAMESPACE_ID;
DELETE FROM app_metadata WHERE namespace_id = $NAMESPACE_ID;
```

### `npm run db:migrate`
Applies Supabase migrations via the Supabase CLI or direct SQL execution.

---

## 14. Data Flow Diagrams

### 14.1 Show Save Flow

```
User action (set status / rate / tag)
  → Client: determine auto-save defaults (auto-save.ts)
  → Client: POST /api/shows { showData, userData }
  → Server: resolve namespace + user identity
  → Server: fetch current stored show (if exists)
  → Server: merge catalog data + user data (show-merge.ts)
  → Server: upsert into Supabase shows table
  → Server: return merged show
  → Client: optimistic UI update confirmed
```

### 14.2 AI Recommendation Flow (Explore Similar)

```
User taps "Get Concepts" on Show Detail
  → Client: POST /api/ai/concepts { show, mode: 'single' }
  → Server: build concept prompt (concepts.ts)
  → Server: call OpenAI → parse concept list
  → Client: display concept chips

User selects concepts, taps "Explore Shows"
  → Client: POST /api/ai/recommendations { concepts, sourceShows, mode: 'explore' }
  → Server: build recommendation prompt (recommendations.ts)
  → Server: call OpenAI → parse recommendation list
  → Server: for each rec → resolve against TMDB (resolve-shows.ts)
  → Server: return enriched recommendations with full Show objects
  → Client: display recommendation cards with reasons
```

### 14.3 Ask Chat Flow

```
User types message in Ask
  → Client: POST /api/ai/ask { messages, handoffShow? }
  → Server: if messages > 10, summarize older turns
  → Server: inject library context into system prompt
  → Server: call OpenAI with structured output
  → Server: parse { commentary, showList }
  → Server: parse showList "Title::id::type;;" format
  → Server: resolve each mentioned show against TMDB
  → Server: return { commentary, mentionedShows: Show[] }
  → Client: display assistant message + mentioned shows strip
```

---

## 15. Cross-Cutting Concerns

### 15.1 Error Handling

- API routes return structured error responses: `{ error: string, code: string }`
- Client-side: error boundaries at page level, toast notifications for mutation failures
- TMDB failures: graceful degradation (show with partial data, missing images)
- AI failures: retry once, then fall back to error message with "Try again" affordance

### 15.2 Loading States

- Collection Home: skeleton grid
- Show Detail: skeleton sections that match the final layout
- AI Scoop: "Generating..." text with streaming text appearing progressively
- Search: loading spinner in results area
- Ask: typing indicator for assistant responses
- Alchemy: step-specific loading states ("Extracting concepts...", "Finding recommendations...")

### 15.3 Responsive Design

- Sidebar collapses to a drawer on smaller viewports
- Show grid adapts column count to viewport width
- Detail page sections stack vertically, horizontal strands become scrollable

### 15.4 Data Continuity Across Versions

- `app_metadata.data_model_version` tracks the current schema version
- Future migrations check this version and apply transformations incrementally
- User libraries are preserved across updates without manual intervention

---

## 16. Implementation Order

Build in this dependency-driven sequence:

| Phase | Deliverable | Dependencies |
|---|---|---|
| 1 | Project scaffolding (Next.js, Tailwind, shadcn/ui, env, Supabase client) | None |
| 2 | Database migration (all tables, RLS, indexes) | Phase 1 |
| 3 | Identity layer (namespace + user resolution, middleware) | Phase 1 |
| 4 | TMDB integration (client, mappers, catalog API routes) | Phase 1 |
| 5 | Show CRUD + merge (save/update/delete/list API routes) | Phases 2, 3, 4 |
| 6 | Collection Home (status-grouped grid, tiles, media toggle) | Phase 5 |
| 7 | Show Detail page (all sections, status/rating/tag with auto-save) | Phases 4, 5 |
| 8 | Search (TMDB search UI, in-collection badges) | Phases 4, 5 |
| 9 | AI layer foundation (client, prompt builders, resolution pipeline) | Phase 4 |
| 10 | AI Scoop (generation, streaming, freshness, persistence) | Phases 7, 9 |
| 11 | Ask (chat UI, conversation context, mentioned shows, summarization) | Phase 9 |
| 12 | Concepts + Explore Similar (concept generation, selection, recs) | Phases 7, 9 |
| 13 | Alchemy (multi-show selection, concept extraction, chaining) | Phase 12 |
| 14 | Person Detail (TMDB person data, filmography, analytics) | Phase 4 |
| 15 | Settings + Export (all settings, data export zip) | Phase 5 |
| 16 | Sidebar filters (tag/genre/decade/score filters, persistence) | Phase 6 |
| 17 | Polish (empty states, loading skeletons, error handling, responsive) | All |
