# Implementation Plan

## 1. Architecture Overview

### Stack

- **Framework:** Next.js (latest stable, App Router)
- **Persistence:** Supabase (hosted or local, via official JS client)
- **AI Provider:** OpenAI-compatible API (configurable model via settings)
- **External Catalog:** TMDB API (movies + TV)
- **Styling:** Tailwind CSS + shadcn/ui component primitives
- **State Management:** React Server Components for data fetching; minimal client state via React context for transient UI (filter selection, modal state, Alchemy session)

### Key Architectural Decisions

1. **Server Components by default.** Data-fetching pages (Home, Detail, Person, Search results) are RSC. Client Components only where interactivity demands it (chat UI, status chips, rating slider, Alchemy flow, concept selection).
2. **API Routes as the server boundary.** All Supabase writes and AI calls go through Next.js Route Handlers (`app/api/...`). The browser never holds the Supabase service-role key. The anon/public key is used for client-side Supabase reads where appropriate, but writes are server-mediated.
3. **Namespace + User scoping everywhere.** Every Supabase query includes `namespace_id` and `user_id` filters. Row-Level Security (RLS) policies enforce this at the database level.
4. **Dev identity injection.** In development/test mode, `X-User-Id` and `X-Namespace-Id` headers (or `.env` defaults) set identity. A dev-only user selector in the UI allows switching users. Gated behind `NODE_ENV !== 'production'`.

---

## 2. Project Structure

```
/
├── app/
│   ├── layout.tsx                  # Root layout (nav shell, providers)
│   ├── page.tsx                    # Collection Home
│   ├── detail/[id]/page.tsx        # Show Detail
│   ├── person/[id]/page.tsx        # Person Detail
│   ├── find/
│   │   ├── layout.tsx              # Find/Discover layout with mode switcher
│   │   ├── search/page.tsx         # Search
│   │   ├── ask/page.tsx            # Ask (chat)
│   │   └── alchemy/page.tsx        # Alchemy
│   ├── settings/page.tsx           # Settings & Your Data
│   └── api/
│       ├── shows/                  # CRUD for user shows
│       │   ├── route.ts            # GET (collection), POST (save)
│       │   └── [id]/route.ts       # PATCH (update my data), DELETE (remove)
│       ├── catalog/                # TMDB proxy
│       │   ├── search/route.ts
│       │   ├── detail/[id]/route.ts
│       │   ├── person/[id]/route.ts
│       │   └── trending/route.ts
│       ├── ai/
│       │   ├── scoop/route.ts
│       │   ├── ask/route.ts
│       │   ├── concepts/route.ts
│       │   └── recommendations/route.ts
│       ├── settings/route.ts
│       └── export/route.ts
├── components/
│   ├── layout/                     # Nav, sidebar, filter panel
│   ├── show/                       # ShowTile, ShowGrid, StatusChips, RatingBar, TagPicker
│   ├── detail/                     # Detail page sections
│   ├── find/                       # Search bar, chat UI, Alchemy steps
│   ├── person/                     # Person card, filmography, charts
│   └── ui/                         # Shared primitives (shadcn wrappers)
├── lib/
│   ├── supabase/
│   │   ├── client.ts               # Browser client (anon key)
│   │   ├── server.ts               # Server client (service role)
│   │   ├── middleware.ts            # Identity extraction
│   │   └── types.ts                # Generated DB types
│   ├── tmdb/
│   │   ├── client.ts               # TMDB fetch wrapper
│   │   ├── mappers.ts              # TMDB response → Show mapping
│   │   └── types.ts                # TMDB-specific types
│   ├── ai/
│   │   ├── client.ts               # AI provider wrapper
│   │   ├── prompts/                # Prompt templates per surface
│   │   │   ├── scoop.ts
│   │   │   ├── ask.ts
│   │   │   ├── concepts.ts
│   │   │   └── recommendations.ts
│   │   └── parsers.ts              # Structured output parsing
│   ├── domain/
│   │   ├── show.ts                 # Show type, merge logic, defaults
│   │   ├── status.ts               # Status/Interest enums, transition rules
│   │   ├── filters.ts              # Filter types, application logic
│   │   └── collection.ts           # Save triggers, removal logic
│   └── identity.ts                 # Namespace + user resolution
├── hooks/                          # Client-side React hooks
│   ├── use-collection.ts
│   ├── use-filters.ts
│   └── use-alchemy-session.ts
├── supabase/
│   ├── migrations/                 # Ordered SQL migrations
│   │   ├── 001_initial_schema.sql
│   │   ├── 002_rls_policies.sql
│   │   └── 003_seed_data.sql
│   └── seed.sql                    # Optional dev seed
├── scripts/
│   ├── reset-namespace.ts          # Destructive test reset for a namespace
│   └── export-data.ts              # Backup/export helper
├── .env.example
├── .gitignore
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

---

## 3. Database Schema (Supabase / PostgreSQL)

### 3.1 Tables

#### `shows`

The primary user-owned show table. One row per (namespace, user, show).

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PK, default `gen_random_uuid()` | Internal row ID |
| `namespace_id` | `text` | NOT NULL | Build isolation |
| `user_id` | `text` | NOT NULL | User scoping |
| `external_id` | `text` | NOT NULL | TMDB ID (string) |
| `show_type` | `text` | NOT NULL, CHECK in ('movie','tv','unknown') | |
| `title` | `text` | NOT NULL | |
| `overview` | `text` | | |
| `tagline` | `text` | | |
| `genres` | `text[]` | DEFAULT '{}' | Genre display names |
| `homepage` | `text` | | |
| `original_language` | `text` | | |
| `spoken_languages` | `text[]` | DEFAULT '{}' | |
| `languages` | `text[]` | DEFAULT '{}' | |
| `poster_url` | `text` | | Full URL |
| `backdrop_url` | `text` | | Full URL |
| `logo_url` | `text` | | Full URL |
| `vote_average` | `double precision` | | |
| `vote_count` | `integer` | | |
| `popularity` | `double precision` | | |
| `release_date` | `timestamptz` | | Movies |
| `first_air_date` | `timestamptz` | | TV |
| `last_air_date` | `timestamptz` | | TV |
| `runtime` | `integer` | | Movies (minutes) |
| `budget` | `bigint` | | Movies |
| `revenue` | `bigint` | | Movies |
| `series_status` | `text` | | TV |
| `number_of_episodes` | `integer` | | TV |
| `number_of_seasons` | `integer` | | TV |
| `episode_run_time` | `integer[]` | DEFAULT '{}' | TV |
| `provider_data` | `jsonb` | | Opaque provider blob |
| `my_status` | `text` | CHECK in ('active','next','later','done','quit','wait') | Nullable = not in collection |
| `my_status_updated_at` | `timestamptz` | | |
| `my_interest` | `text` | CHECK in ('excited','interested') | Only meaningful when status = 'later' |
| `my_interest_updated_at` | `timestamptz` | | |
| `my_tags` | `text[]` | DEFAULT '{}' | |
| `my_tags_updated_at` | `timestamptz` | | |
| `my_score` | `double precision` | | |
| `my_score_updated_at` | `timestamptz` | | |
| `ai_scoop` | `text` | | |
| `ai_scoop_updated_at` | `timestamptz` | | |
| `details_updated_at` | `timestamptz` | | Last catalog refresh |
| `created_at` | `timestamptz` | DEFAULT now() | |
| `is_test` | `boolean` | DEFAULT false | |

**Unique constraint:** `(namespace_id, user_id, external_id)` — one row per show per user per namespace.

**Indexes:**
- `(namespace_id, user_id)` — collection queries
- `(namespace_id, user_id, my_status)` — status-grouped queries
- `(namespace_id, user_id, my_tags)` — GIN index for tag filtering

#### `cloud_settings`

| Column | Type | Constraints |
|---|---|---|
| `id` | `text` | PK, default `'globalSettings'` |
| `namespace_id` | `text` | NOT NULL |
| `user_id` | `text` | NOT NULL |
| `user_name` | `text` | NOT NULL |
| `version` | `double precision` | Epoch seconds, conflict resolution |
| `catalog_api_key` | `text` | Encrypted at rest |
| `ai_api_key` | `text` | Encrypted at rest |
| `ai_model` | `text` | NOT NULL |
| `updated_at` | `timestamptz` | |

**Unique constraint:** `(namespace_id, user_id)`

#### `app_metadata`

| Column | Type | Constraints |
|---|---|---|
| `id` | `uuid` | PK |
| `namespace_id` | `text` | NOT NULL, UNIQUE |
| `data_model_version` | `integer` | DEFAULT 3 |

### 3.2 Row-Level Security

Every table gets RLS enabled with policies scoped to `namespace_id` and `user_id`, extracted from the request context (JWT claims or custom headers in dev mode):

```sql
CREATE POLICY "Users can only access their own data"
  ON shows FOR ALL
  USING (
    namespace_id = current_setting('app.namespace_id')
    AND user_id = current_setting('app.user_id')
  );
```

The server sets these session variables via `SET LOCAL` before each query block.

### 3.3 Migrations Strategy

Migrations are numbered SQL files in `supabase/migrations/`. Applied via `supabase db push` (hosted) or `supabase migration up` (local). The `app_metadata.data_model_version` column tracks the schema version for future upgrade paths.

---

## 4. Identity & Isolation

### 4.1 Namespace Resolution

1. Check `X-Namespace-Id` header (dev/test only).
2. Fall back to `NAMESPACE_ID` environment variable.
3. If neither is set, generate a deterministic namespace from the deployment URL hash.

### 4.2 User Resolution

1. Check `X-User-Id` header (dev/test only).
2. Fall back to `DEFAULT_USER_ID` environment variable.
3. In production (future): extract from JWT/session after OAuth integration.

### 4.3 Middleware

A Next.js middleware (`middleware.ts`) resolves namespace and user on every request and injects them into headers for downstream Route Handlers. In dev mode, a small floating UI component lets the developer switch user IDs.

---

## 5. External Catalog Integration (TMDB)

### 5.1 API Client (`lib/tmdb/client.ts`)

Thin wrapper around TMDB REST API v3 using `fetch`. Reads `TMDB_API_KEY` from environment (or user-configured `catalog_api_key` from settings).

Endpoints used:
- `GET /search/multi` — search by title
- `GET /movie/{id}` — movie detail (append `credits,videos,recommendations,similar,watch/providers,images`)
- `GET /tv/{id}` — TV detail (same appends + `seasons`)
- `GET /person/{id}` — person detail (append `combined_credits,images`)
- `GET /trending/{media_type}/week` — optional trending for empty states

### 5.2 Response Mapping (`lib/tmdb/mappers.ts`)

Implements the mapping rules from `storage-schema.md`:

- `tmdbMovieToShow(tmdbMovie): Partial<Show>` — maps movie response fields.
- `tmdbTvToShow(tmdbTv): Partial<Show>` — maps TV response fields.
- `resolveShowType(payload)` — infers type from field presence (`name` → tv, `title` → movie).
- `selectBestLogo(logos[])` — deterministic selection (prefer English, highest vote average).
- `buildImageUrl(path, size)` — constructs full TMDB image URL from path + configured base URL.

### 5.3 Merge Logic (`lib/domain/show.ts`)

When refreshing catalog data into a stored show:

```
mergeShow(existing: Show, incoming: Partial<Show>): Show
```

- Non-user fields: `selectFirstNonEmpty(incoming, existing)` — never overwrite a non-empty value with empty/null.
- User fields (`my_*`): compare `*_updated_at` timestamps, keep the newer value.
- Set `details_updated_at` to now.
- Preserve `created_at` from existing.

---

## 6. Domain Logic

### 6.1 Status & Interest (`lib/domain/status.ts`)

Enums:
```ts
type MyStatus = 'active' | 'later' | 'wait' | 'done' | 'quit' | 'next'
type MyInterest = 'interested' | 'excited'
```

Rules:
- Setting "Interested" or "Excited" chip → `my_status = 'later'`, `my_interest = <selected>`.
- Changing status away from "later" → interest becomes irrelevant (retained in DB but not displayed).
- Reselecting the currently active status → triggers removal confirmation flow.

### 6.2 Save Triggers (`lib/domain/collection.ts`)

A show enters the collection on any of:
1. **Status set** → status = chosen, interest = null (unless Later).
2. **Interest chip selected** → status = later, interest = chosen.
3. **Rating set on unsaved show** → status = done, interest = null.
4. **Tag added on unsaved show** → status = later, interest = interested.

### 6.3 Removal (`lib/domain/collection.ts`)

- Trigger: user reselects the currently active status.
- Confirmation dialog shown (suppressible after repeated dismissals via `hideStatusRemovalConfirmation`).
- On confirm: DELETE row from `shows` table (clears all my data).

### 6.4 Filter System (`lib/domain/filters.ts`)

Filter types:
- `all` — no filter
- `myTag` — shows with a specific tag
- `genre` — shows with a specific genre
- `decade` — shows from a decade (release_date / first_air_date)
- `communityScore` — vote_average ranges
- `myStatus` — status filter (data model supports, UI optional per PRD)

Media-type toggle (`all` / `movie` / `tv`) applies on top of any filter.

Sidebar construction:
- "All Shows" always present.
- One entry per unique tag in user's collection.
- "No tags" entry if any tagless shows exist.
- Genre filters derived from genres across the collection.
- Decade and community score filters as static ranges.

---

## 7. API Route Handlers

### 7.1 Shows (`app/api/shows/`)

**`GET /api/shows`**
- Query params: `filter`, `mediaType`, `status`, `tag`, etc.
- Returns the user's collection, grouped by status sections.

**`POST /api/shows`**
- Body: `{ externalId, showType, ...catalogData, myStatus?, myInterest?, myScore?, myTags? }`
- Applies save-trigger defaults.
- Upserts into `shows` table (on conflict of `namespace_id, user_id, external_id`).

**`PATCH /api/shows/[id]`**
- Body: partial update of any my_* field.
- Sets corresponding `*_updated_at` to now.
- Applies implicit save rules if show not yet in collection.

**`DELETE /api/shows/[id]`**
- Deletes the row entirely.

### 7.2 Catalog Proxy (`app/api/catalog/`)

**`GET /api/catalog/search?q=...&page=...`**
- Proxies TMDB search. Maps results through `tmdbToShow` mapper.
- Enriches each result with collection status if user has it saved.

**`GET /api/catalog/detail/[id]?type=movie|tv`**
- Fetches full TMDB detail. Returns merged view (catalog + stored my_data if saved).

**`GET /api/catalog/person/[id]`**
- Fetches person detail + combined credits from TMDB.

### 7.3 AI (`app/api/ai/`)

All AI routes:
- Read AI API key from user settings (or `AI_API_KEY` env var fallback).
- Read AI model from user settings (or `AI_MODEL` env var fallback).
- Include user library context where appropriate.

**`POST /api/ai/scoop`**
- Body: `{ showId, showTitle, showType, genres, overview, voteAverage }`
- Checks cache: if `ai_scoop_updated_at` < 4 hours ago, return cached scoop.
- Otherwise generates new scoop via AI, persists to `shows.ai_scoop` (only if show is in collection), returns result.
- Supports streaming response.

**`POST /api/ai/ask`**
- Body: `{ messages[], showContext?, library[] }`
- Returns streaming chat response with structured `{ commentary, showList }` output.
- `showList` uses the `Title::externalId::mediaType;;` format.
- Parses `showList`, resolves each via TMDB lookup, returns enriched mentioned-shows array alongside commentary.

**`POST /api/ai/concepts`**
- Body: `{ shows[] }` (1 show = Explore Similar, 2+ = Alchemy)
- Returns bullet list of 8 concepts (1-3 words each).
- Single-show: concepts for that show. Multi-show: shared concepts across all inputs.

**`POST /api/ai/recommendations`**
- Body: `{ concepts[], sourceShows[], count }` (5 for Explore Similar, 6 for Alchemy)
- Returns list of recommended shows with reasons tied to selected concepts.
- Each recommendation includes TMDB ID + media type for resolution.
- Resolves each recommendation against TMDB catalog. Unresolvable titles are flagged as non-interactive.

### 7.4 Settings (`app/api/settings/`)

**`GET /api/settings`** — returns current `cloud_settings` + `local_settings` for user.

**`PATCH /api/settings`** — updates settings fields. Validates AI key format. Never commits secrets.

### 7.5 Export (`app/api/export/`)

**`GET /api/export`**
- Queries all shows for current user + namespace.
- Produces a JSON backup with ISO-8601 dates.
- Streams as a `.zip` download containing the JSON file.

---

## 8. UI Components

### 8.1 Layout Shell (`components/layout/`)

- **`AppShell`**: persistent sidebar (filters) + top nav + main content area.
- **`Sidebar`**: filter list (All Shows, tag filters, data filters). Collapsible on mobile.
- **`TopNav`**: app name/logo, Find/Discover entry point, Settings entry point.
- **`MediaTypeToggle`**: All / Movies / TV segmented control, applied globally.

### 8.2 Collection Home (`app/page.tsx`)

Server Component that fetches the user's collection, applies active filter + media type toggle, and renders status-grouped sections:

1. **Active** — larger poster tiles, prominent placement.
2. **Excited** (Later + Excited) — standard tiles.
3. **Interested** (Later + Interested) — standard tiles.
4. **Other** (Wait, Quit, Done, unclassified Later) — collapsed accordion group.

**Empty states:**
- No collection: CTA to "Search for shows" or "Ask for recommendations."
- Filter yields nothing: "No results found."

### 8.3 Show Tile (`components/show/ShowTile.tsx`)

Client Component displaying:
- Poster image (with fallback gradient).
- Title overlay.
- In-collection badge (dot or checkmark) when `my_status` exists.
- User rating badge when `my_score` exists.
- Click → navigates to `/detail/[id]?type=movie|tv`.

### 8.4 Show Detail Page (`app/detail/[id]/page.tsx`)

Mixed RSC + Client Component page. Sections in order:

1. **HeaderCarousel** — backdrop/poster/logo images + trailer video (inline playback). Graceful fallback to poster-only.
2. **CoreFacts** — year, runtime/seasons+episodes, community score bar.
3. **StatusToolbar** (Client) — sticky toolbar with status/interest chips. Handles save triggers, removal confirmation.
4. **RatingBar** (Client) — slider for user rating. Auto-saves as Done if unsaved.
5. **TagSection** (Client) — current tags + "Add tag" picker. Auto-saves as Later+Interested if unsaved.
6. **Overview** — text block.
7. **ScoopSection** (Client) — toggle button ("Give me the scoop!" / "Show the scoop" / "The Scoop"). Streaming generation with "Generating..." indicator.
8. **AskAboutCTA** — "Ask about [Title]" button → navigates to `/find/ask?showId=...`.
9. **GenresLanguages** — genre chips + language list.
10. **RecommendationsStrand** — horizontal scrolling row of traditional similar/recommended shows from TMDB.
11. **ExploreSimilar** (Client) — Get Concepts button → concept chips → Explore Shows button → AI recommendation cards.
12. **StreamingProviders** — provider logos grouped by type (stream/rent/buy).
13. **CastCrew** — horizontal strands of person cards → link to `/person/[id]`.
14. **Seasons** (TV only) — season/episode list.
15. **BudgetRevenue** (Movies, when available) — formatted display.

### 8.5 Find/Discover (`app/find/`)

Shared layout with a **mode switcher** (tab bar or segmented control): Search | Ask | Alchemy.

#### Search (`app/find/search/page.tsx`)
- Text input with debounced search.
- Results in a poster grid (ShowTile components).
- In-collection items marked.
- No AI voice in search.

#### Ask (`app/find/ask/page.tsx`)
Client Component with:
- Chat message list (user/assistant bubbles).
- Input bar at bottom.
- Welcome view with 6 random starter prompts (from a pool of ~80) + refresh button.
- Mentioned shows horizontal strip below each assistant message.
- Streaming responses.
- Context: injects user library summary into system prompt.
- Show-seeded mode: if `showId` query param present, seed conversation with show context.
- Conversation summarization: after ~10 messages, older turns are summarized to control token depth.

#### Alchemy (`app/find/alchemy/page.tsx`)
Client Component with stepped flow:

1. **Select Shows** — search + library picker. Minimum 2, displayed as selected cards.
2. **Conceptualize** — "Conceptualize Shows" button → loading → concept chips appear.
3. **Select Concepts** — user picks 1-8 concepts. Hint: "pick the ingredients you want more of."
4. **Alchemize** — "ALCHEMIZE!" button → loading → 6 recommendation cards with reasons.
5. **Chain** — "More Alchemy!" button to use results as new inputs.

Backtracking: changing shows clears concepts + results. Changing concepts clears results.

### 8.6 Person Detail (`app/person/[id]/page.tsx`)

- Image gallery.
- Name + bio.
- Analytics charts (lightweight): average ratings, top genres, projects-by-year (using a small chart library like recharts or chart.js).
- Filmography grouped by year.
- Each credit links to `/detail/[id]`.

### 8.7 Settings (`app/settings/page.tsx`)

Sections:
- **App**: Font size selector (XS-XXL), Search on launch toggle.
- **User**: Username text input.
- **AI**: API key input (masked), Model selector dropdown.
- **Integrations**: Catalog API key input (masked).
- **Your Data**: "Export My Data" button (triggers zip download).

---

## 9. AI Prompt Architecture

### 9.1 Shared System Context

All AI prompts share a base personality preamble:

- You are a fun, chatty TV/movie nerd friend.
- Warm, opinionated, spoiler-safe by default.
- Vibe-first reasoning, not plot summaries.
- Stay within TV/movies domain.
- If the user has a library, reference it for taste-awareness.

### 9.2 Scoop Prompt (`lib/ai/prompts/scoop.ts`)

Input: show title, type, genres, overview, vote average, user's library summary.

Output structure: personal take → honest stack-up → "The Scoop" centerpiece → fit/warnings → "Worth it?" verdict.

Length target: 150-350 words. Tone: gossipy, vivid, useful.

### 9.3 Ask Prompt (`lib/ai/prompts/ask.ts`)

System prompt: base personality + user library summary (titles, statuses, ratings, tags).

Output: structured `{ commentary, showList }` where `showList` uses the `Title::tmdbId::mediaType;;` delimiter format.

Conversation summarization: after 10 turns, summarize older turns in-persona (not a sterile system summary).

### 9.4 Concepts Prompt (`lib/ai/prompts/concepts.ts`)

Input: show title(s), types, genres, overviews.

Instructions: generate 8 concepts. Each 1-3 words. Evocative, spoiler-free. Cover different axes (structure, vibe, emotion, craft). Order by strength. For multi-show: concepts must be shared across all inputs.

Output: bullet list only.

### 9.5 Recommendations Prompt (`lib/ai/prompts/recommendations.ts`)

Input: selected concepts, source show(s), user library, count (5 or 6).

Instructions: recommend real shows. Each with a 1-3 sentence reason naming which concepts align. Bias toward recent but allow classics. Include TMDB ID and media type for each recommendation.

Output: structured list with title, tmdbId, mediaType, reason.

### 9.6 Parsing & Fallbacks (`lib/ai/parsers.ts`)

- Parse `showList` string by splitting on `;;` then `::`.
- If parsing fails: retry once with stricter formatting instructions.
- If still fails: return commentary only, flag mentioned shows as unresolvable.
- TMDB resolution: for each parsed show, call `GET /search/multi?query=title` and match by TMDB ID. If ID match fails, accept first result with case-insensitive title match. If no match: show as non-interactive or hand off to Search.

---

## 10. Data Behaviors Implementation

### 10.1 Save Trigger Middleware

A server-side function `applySaveTriggers(show, update)` that runs before any show upsert:

```
if show not in collection:
  if update has myStatus       → save with that status
  if update has myInterest     → save with status=later, interest=<value>
  if update has myScore        → save with status=done
  if update has myTags (added) → save with status=later, interest=interested
```

### 10.2 Collection Membership Check

`isInCollection(show) = show.my_status !== null`

### 10.3 Timestamps

Every `PATCH /api/shows/[id]` that modifies a `my_*` field also sets the corresponding `*_updated_at` to `new Date().toISOString()`.

### 10.4 AI Scoop Freshness

On scoop request:
1. Check `ai_scoop_updated_at` on stored show.
2. If exists and < 4 hours old → return cached.
3. Otherwise → generate, update `ai_scoop` + `ai_scoop_updated_at`.
4. Only persist if show is in collection.

### 10.5 Merge on Catalog Refresh

When a show detail page is loaded, fresh TMDB data is fetched and merged into the stored record (if it exists) using `mergeShow()` logic. Non-empty stored values are never overwritten with empty values. User fields use timestamp-based resolution.

---

## 11. Testing Strategy

### 11.1 Scripts

- `npm run dev` — start Next.js dev server.
- `npm test` — run test suite.
- `npm run test:reset` — runs `scripts/reset-namespace.ts` which deletes all `shows` and `cloud_settings` rows for the current namespace.
- `npm run db:migrate` — apply migrations.

### 11.2 Test Categories

- **Unit tests**: domain logic (save triggers, merge, filter application, status transitions). Using Vitest.
- **API route tests**: each route handler with mocked Supabase client. Verify namespace/user scoping, save trigger defaults, AI output parsing.
- **Integration tests**: end-to-end flows (search → save → collection → update → remove) against a test Supabase instance with a dedicated test namespace.

### 11.3 Namespace Isolation in Tests

All test runs use a dedicated `namespace_id` (e.g., `test-{timestamp}`). The reset script only deletes data for that namespace, never touching other namespaces.

---

## 12. Environment Variables

```env
# .env.example

# Supabase
NEXT_PUBLIC_SUPABASE_URL=       # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=  # Supabase anon/public key (safe for browser)
SUPABASE_SERVICE_ROLE_KEY=      # Supabase service role key (server-only, never exposed to client)

# TMDB
TMDB_API_KEY=                   # TMDB API v3 key

# AI Provider
AI_API_KEY=                     # OpenAI-compatible API key (fallback if not set in user settings)
AI_MODEL=gpt-4o                 # Default AI model (fallback if not set in user settings)
AI_BASE_URL=                    # Optional: custom AI API base URL

# Identity (dev/benchmark mode)
NAMESPACE_ID=                   # Build namespace for isolation
DEFAULT_USER_ID=                # Default user ID for dev mode

# App
NODE_ENV=development            # development | production | test
```

---

## 13. Implementation Phases

### Phase 1: Foundation (Priority: Critical)
1. Project scaffolding (Next.js, Tailwind, shadcn/ui, Supabase client setup).
2. Database schema + migrations + RLS policies.
3. Identity middleware (namespace + user resolution).
4. `.env.example` + `.gitignore`.
5. TMDB client + response mappers.
6. Domain logic (show types, status/interest enums, save triggers, merge logic, filter types).

### Phase 2: Core Collection (Priority: Critical)
7. API routes: shows CRUD (`/api/shows`).
8. API routes: catalog proxy (`/api/catalog`).
9. Layout shell: sidebar, top nav, media type toggle.
10. Collection Home page with status-grouped sections + empty states.
11. Show Tile component with badges.
12. Filter sidebar (All Shows, tags, genres, decades, community score).

### Phase 3: Show Detail (Priority: Critical)
13. Show Detail page — all sections in narrative hierarchy order.
14. Status toolbar with chips + removal confirmation.
15. Rating bar with auto-save behavior.
16. Tag picker with auto-save behavior.
17. Scoop section (toggle, streaming generation, caching).
18. Recommendations strand (TMDB similar/recommended).
19. Streaming providers display.
20. Cast/Crew strands linking to Person Detail.
21. Seasons display (TV) and Budget/Revenue display (Movies).

### Phase 4: Discovery — Search & Ask (Priority: High)
22. Find/Discover layout + mode switcher.
23. Search page (debounced TMDB search, poster grid, collection marking).
24. AI client wrapper + prompt templates (shared personality, Ask, Scoop).
25. Ask page — chat UI, streaming responses, mentioned shows strip.
26. Ask conversation summarization.
27. "Ask about this show" flow (show-seeded Ask).
28. Welcome view with starter prompts.

### Phase 5: Discovery — Alchemy & Explore Similar (Priority: High)
29. Concepts prompt + API route.
30. Recommendations prompt + API route.
31. Explore Similar on Detail page (concepts → selection → recommendations).
32. Alchemy page — full stepped flow with show picker, concept chips, results, chaining.

### Phase 6: Person & Settings (Priority: Medium)
33. Person Detail page (bio, filmography, analytics charts).
34. Settings page (all sections: app, user, AI, integrations, export).
35. Export/backup endpoint producing zip with JSON.

### Phase 7: Polish & Testing (Priority: Medium)
36. Empty states and loading skeletons across all pages.
37. Error handling and fallback UI (AI failures, TMDB failures, network errors).
38. Responsive design (mobile sidebar collapse, tile grid adaptation).
39. Unit + API route tests.
40. Integration tests with test namespace.
41. Reset script for destructive test cleanup.

---

## 14. Key Risk Mitigations

| Risk | Mitigation |
|---|---|
| AI hallucinated show titles | Every recommendation is resolved against TMDB by ID first, then title match. Unresolvable titles shown non-interactive or handed to Search. |
| AI structured output parsing failures | Retry once with stricter instructions. Fall back to unstructured commentary + Search handoff. |
| TMDB rate limits | Server-side proxy with request queuing. Cache TMDB detail responses for the session. |
| Supabase RLS misconfiguration | Unit tests verify that queries without proper namespace/user context return empty results. |
| Schema evolution breaking existing data | `app_metadata.data_model_version` tracks version. Migration scripts are idempotent and additive. |
| Secret exposure | RLS + server-only service role key. `.env.example` documents but `.gitignore` excludes `.env*`. AI/catalog keys validated server-side only. |

---

## 15. Data Continuity & Migration Path

- The `app_metadata.data_model_version` column enables future schema migrations.
- Migrations are written as additive SQL (add columns, add tables) rather than destructive operations.
- A migration runner checks the current version and applies pending migrations in order.
- User data (my_status, my_interest, my_tags, my_score, ai_scoop) is never dropped by migrations — only reshaped with safe defaults.
- OAuth migration path: `user_id` is already an opaque string. Swapping from dev identity injection to JWT-based identity requires only wiring Supabase Auth and removing the dev header mechanism. No schema changes needed.
