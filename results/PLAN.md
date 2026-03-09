# Implementation Plan

## 1. Architecture Overview

**Stack:** Next.js (App Router, latest stable) + Supabase + TMDB API + AI provider (OpenAI-compatible)

**Three-tier separation:**
- **Client (React):** UI components, local state, optimistic updates
- **Server (Next.js API routes / Server Actions):** Business logic, AI orchestration, external API calls, auth gating
- **Persistence (Supabase):** PostgreSQL for user data, Row-Level Security for namespace/user isolation

**Key architectural decisions:**
- Server-side is source of truth; client caches are disposable (PRD rule 8.9)
- All user-owned records partitioned by `(namespace_id, user_id)` (infra rider 4.1–4.3)
- Dev identity injection via `X-User-Id` header + configurable `NAMESPACE_ID` env var
- TMDB as the external catalog provider (external IDs, images, metadata)
- AI calls server-side only (protects API keys, enables structured output parsing)

---

## 2. Project Setup & Repo Structure

### 2.1 Initialize

```
npx create-next-app@latest . --typescript --tailwind --app --src-dir
```

### 2.2 Directory Layout

```
src/
  app/                      # Next.js App Router pages
    layout.tsx              # Root layout with providers
    page.tsx                # Collection Home
    find/
      page.tsx              # Find/Discover hub (Search, Ask, Alchemy)
    show/[id]/
      page.tsx              # Show Detail
    person/[id]/
      page.tsx              # Person Detail
    settings/
      page.tsx              # Settings & Your Data
    api/
      shows/                # CRUD for user's shows
      search/               # TMDB search proxy
      show/[id]/            # TMDB detail fetch + merge
      person/[id]/          # TMDB person fetch
      ai/
        scoop/              # AI Scoop generation
        ask/                # Ask chat
        concepts/           # Concept generation
        recommendations/    # Concept-based recs
      export/               # Data export
      settings/             # Cloud settings CRUD
  lib/
    supabase/
      client.ts             # Browser client (anon key)
      server.ts             # Server client (service role, server-only)
      migrations/           # SQL migration files
    tmdb/
      client.ts             # TMDB API client
      mapper.ts             # External catalog → Show mapping
      types.ts              # TMDB response types
    ai/
      client.ts             # AI provider client
      prompts/              # All prompt templates
        scoop.ts
        ask.ts
        concepts.ts
        recommendations.ts
        summarize.ts
      parser.ts             # Structured output parsing (showList format)
    merge.ts                # Show merge logic (selectFirstNonEmpty, timestamp resolution)
    types.ts                # Shared TypeScript types (Show, Status, Interest, etc.)
    constants.ts            # Status/interest enums, defaults, config
    utils.ts                # Date formatting, helpers
  components/
    layout/
      Sidebar.tsx           # Filter panel
      TopNav.tsx            # Global nav (Find, Settings)
      MediaTypeToggle.tsx   # All / Movies / TV
    home/
      StatusSection.tsx     # Grouped status sections
      ShowTile.tsx          # Poster tile with badges
    detail/
      HeaderCarousel.tsx    # Backdrop/poster/trailer carousel
      CoreFacts.tsx         # Year, runtime, community score
      StatusToolbar.tsx     # Status/interest chips
      RatingBar.tsx         # My Rating slider
      TagPicker.tsx         # My Tags display + picker
      Overview.tsx          # Overview text
      ScoopSection.tsx      # Scoop toggle + streaming display
      RecommendationsStrand.tsx
      ExploreSimilar.tsx    # Get Concepts → select → Explore Shows
      StreamingProviders.tsx
      CastCrew.tsx
      Seasons.tsx
      BudgetRevenue.tsx
    find/
      ModeSwitcher.tsx      # Search / Ask / Alchemy tabs
      SearchView.tsx        # Search input + results grid
      AskView.tsx           # Chat UI
      AlchemyView.tsx       # Multi-step alchemy flow
    person/
      PersonHeader.tsx
      Analytics.tsx
      Filmography.tsx
    settings/
      SettingsForm.tsx
      ExportButton.tsx
    shared/
      ShowGrid.tsx          # Reusable poster grid
      ShowStrip.tsx         # Horizontal scrollable show strip
      ConceptChips.tsx      # Selectable concept chips
      ConfirmDialog.tsx     # Removal confirmation
      LoadingStream.tsx     # Streaming text display
.env.example
.gitignore
supabase/migrations/
```

### 2.3 Environment Variables

`.env.example`:
```
# Supabase
NEXT_PUBLIC_SUPABASE_URL=         # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY=    # Supabase anon/public key
SUPABASE_SERVICE_ROLE_KEY=        # Server-only service role key

# External catalog
TMDB_API_KEY=                     # TMDB v3 API key

# AI provider
AI_API_KEY=                       # AI provider API key
AI_MODEL=                         # AI model identifier (e.g. gpt-4o)
AI_BASE_URL=                      # Optional: custom AI endpoint

# Isolation
NAMESPACE_ID=                     # Build/run namespace (required)
DEFAULT_USER_ID=                  # Default dev user ID (optional, defaults to "default-user")
```

---

## 3. Database Schema & Migrations

### 3.1 Migration 001: Core Schema

```sql
-- Enable UUID extension
create extension if not exists "uuid-ossp";

-- Shows table (user's collection)
create table shows (
  id text not null,
  namespace_id text not null,
  user_id text not null,

  title text not null,
  show_type text not null check (show_type in ('movie', 'tv', 'person', 'unknown')),
  external_ids jsonb default null,

  overview text,
  genres text[] default '{}',
  tagline text,
  homepage text,
  original_language text,
  spoken_languages text[] default '{}',
  languages text[] default '{}',

  poster_url text,
  backdrop_url text,
  logo_url text,
  network_logos text[] default '{}',

  vote_average double precision,
  vote_count integer,
  popularity double precision,

  last_air_date timestamptz,
  first_air_date timestamptz,
  release_date timestamptz,

  runtime integer,
  budget bigint,
  revenue bigint,

  series_status text,
  number_of_episodes integer,
  number_of_seasons integer,
  episode_run_time integer[] default '{}',

  -- User data
  my_tags text[] default '{}',
  my_tags_update_date timestamptz,
  my_score double precision,
  my_score_update_date timestamptz,
  my_status text check (my_status in ('active', 'next', 'later', 'done', 'quit', 'wait')),
  my_status_update_date timestamptz,
  my_interest text check (my_interest in ('excited', 'interested')),
  my_interest_update_date timestamptz,

  -- AI data
  ai_scoop text,
  ai_scoop_update_date timestamptz,

  -- Management
  details_update_date timestamptz,
  creation_date timestamptz default now(),
  is_test boolean default false,

  -- Providers
  provider_data jsonb,

  primary key (id, namespace_id, user_id)
);

-- Indexes for common queries
create index idx_shows_namespace_user on shows(namespace_id, user_id);
create index idx_shows_status on shows(namespace_id, user_id, my_status);
create index idx_shows_type on shows(namespace_id, user_id, show_type);

-- Cloud settings
create table cloud_settings (
  id text not null default 'globalSettings',
  namespace_id text not null,
  user_id text not null,
  user_name text not null,
  version double precision not null default 0,
  catalog_api_key text,
  ai_api_key text,
  ai_model text not null default 'gpt-4o',

  primary key (id, namespace_id, user_id)
);

-- App metadata
create table app_metadata (
  namespace_id text not null primary key,
  data_model_version integer not null default 3
);

-- Row-Level Security
alter table shows enable row level security;
alter table cloud_settings enable row level security;

-- RLS policies: namespace isolation via service role (server routes handle user_id filtering)
create policy "Namespace isolation" on shows
  for all using (namespace_id = current_setting('app.namespace_id', true));

create policy "Namespace isolation" on cloud_settings
  for all using (namespace_id = current_setting('app.namespace_id', true));
```

### 3.2 Migration 002: Test Data Reset Function

```sql
create or replace function reset_namespace_data(target_namespace text)
returns void as $$
begin
  delete from shows where namespace_id = target_namespace;
  delete from cloud_settings where namespace_id = target_namespace;
  delete from app_metadata where namespace_id = target_namespace;
end;
$$ language plpgsql security definer;
```

---

## 4. Identity & Middleware

### 4.1 Auth Middleware

Create middleware that:
1. In development/test mode: reads `X-User-Id` header, falls back to `DEFAULT_USER_ID` env var, falls back to `"default-user"`
2. Injects `namespace_id` from `NAMESPACE_ID` env var
3. Attaches both to the request context (via headers forwarded to API routes)
4. In production mode: would wire to real OAuth (schema doesn't change)

```typescript
// src/middleware.ts
export function middleware(request: NextRequest) {
  const userId = request.headers.get('x-user-id')
    ?? process.env.DEFAULT_USER_ID
    ?? 'default-user';
  const namespaceId = process.env.NAMESPACE_ID;

  if (!namespaceId) throw new Error('NAMESPACE_ID is required');

  const headers = new Headers(request.headers);
  headers.set('x-user-id', userId);
  headers.set('x-namespace-id', namespaceId);

  return NextResponse.next({ headers });
}
```

### 4.2 Server Context Helper

```typescript
// src/lib/context.ts
export function getRequestContext(headers: Headers) {
  return {
    userId: headers.get('x-user-id')!,
    namespaceId: headers.get('x-namespace-id')!,
  };
}
```

---

## 5. Data Layer Implementation

### 5.1 Supabase Clients

- **Browser client:** `createBrowserClient(NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY)` — used only for real-time subscriptions if needed
- **Server client:** `createClient(SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY)` — used in API routes, sets `app.namespace_id` per request

### 5.2 Show Repository (`src/lib/supabase/shows.ts`)

CRUD operations scoped by `(namespace_id, user_id)`:

- `getCollection(ctx)` — all shows for user, ordered by status sections
- `getShow(ctx, showId)` — single show lookup
- `upsertShow(ctx, show)` — insert or merge using merge rules
- `removeShow(ctx, showId)` — delete + clear all My Data
- `updateMyData(ctx, showId, fields)` — partial update for status/interest/tags/rating/scoop
- `getShowsByTag(ctx, tag)` — filter by tag
- `getShowsByFilter(ctx, filter)` — filter by genre/decade/score/status
- `exportAllShows(ctx)` — full collection dump for backup

### 5.3 Merge Logic (`src/lib/merge.ts`)

Implements the merge/overwrite policy from storage-schema.md:

```typescript
function mergeShow(existing: Show, incoming: Partial<Show>): Show {
  // Non-my fields: selectFirstNonEmpty(incoming, existing)
  // My fields: resolve by timestamp (newer wins)
  // Set detailsUpdateDate = now()
  // Preserve creationDate from existing
}

function selectFirstNonEmpty<T>(newVal: T | null, oldVal: T | null): T | null {
  if (newVal !== null && newVal !== undefined && newVal !== '' &&
      !(Array.isArray(newVal) && newVal.length === 0)) {
    return newVal;
  }
  return oldVal;
}
```

### 5.4 Settings Repository (`src/lib/supabase/settings.ts`)

- `getSettings(ctx)` — get or create default CloudSettings
- `updateSettings(ctx, fields)` — partial update with version bump
- Cloud settings sync uses `version` (epoch seconds) for conflict resolution

---

## 6. External Catalog Integration (TMDB)

### 6.1 TMDB Client (`src/lib/tmdb/client.ts`)

Server-side only. Wraps TMDB v3 API:

- `searchMulti(query)` — search movies + TV
- `getMovieDetails(id)` — full movie with credits, videos, recommendations, similar, providers, images
- `getTVDetails(id)` — full TV with credits, videos, recommendations, similar, providers, images, seasons
- `getPersonDetails(id)` — person with combined credits, images
- `getProviders(type, id)` — streaming availability

All calls append `?api_key=TMDB_API_KEY`.

### 6.2 TMDB → Show Mapper (`src/lib/tmdb/mapper.ts`)

Follows the field mapping rules from storage-schema.md:

- Map `id` → `Show.id` (prefixed with media type to avoid collisions: `movie-123`, `tv-456`)
- Title: movie `title` or TV `name` (fail if neither)
- Show type: infer from response shape
- Genres: map genre IDs → names using TMDB genre list
- Images: construct full URLs from TMDB image paths (`https://image.tmdb.org/t/p/{size}{path}`)
- Logo: pick best-rated English logo
- Dates: parse TMDB date strings to ISO-8601
- Provider data: transform TMDB watch/providers response to `ProviderData` shape (IDs only)

### 6.3 Provider Name Resolution

Maintain a small lookup of TMDB provider IDs → display names/logos (fetched once, cached server-side).

---

## 7. AI Integration

### 7.1 AI Client (`src/lib/ai/client.ts`)

OpenAI-compatible client (works with OpenAI, Anthropic via proxy, etc.):
- Reads `AI_API_KEY`, `AI_MODEL`, `AI_BASE_URL` from env
- Supports streaming responses (for Scoop, Ask)
- Supports structured JSON output (for mentioned shows, concepts)

### 7.2 Prompt Templates

All prompts follow the behavioral contracts from `ai_prompting_context.md` and voice spec from `ai_voice_personality.md`.

#### Scoop (`src/lib/ai/prompts/scoop.ts`)

**System prompt:** Establishes the persona (fun chatty TV/movie nerd friend), spoiler-safe default, honest/opinionated voice. Instructs structured output: personal take, honest stack-up, The Scoop (emotional centerpiece), fit/warnings, verdict. 150–350 words.

**User prompt:** Show title, year, overview, genres, community score, user's rating/status if saved.

**Output:** Streaming text.

#### Ask (`src/lib/ai/prompts/ask.ts`)

**System prompt:** Same persona. Conversational, picks favorites, bulleted lists for multi-recs. Stays in TV/movies domain. Taste-aware: include user's library context.

**Extended system prompt (Ask with mentions):** Same as above plus structured output requirement:
```json
{
  "commentary": "user-facing response (no external IDs)",
  "showList": "Title::externalId::mediaType;;Title2::externalId::mediaType"
}
```

**User context injected:** User's library (titles + statuses + tags + ratings), current show context if "Ask about this show."

**Conversation management:** Keep recent turns in messages array. After ~10 messages, summarize older turns into a 1–2 sentence summary that preserves persona tone.

#### Concepts (`src/lib/ai/prompts/concepts.ts`)

**System prompt:** Generate 8 concept ingredients. 1–3 words each, evocative, spoiler-free. No generic concepts. Bullet list only. For multi-show: shared commonalities across all inputs. Order by strength (best "aha" first). Cover diverse axes: structure, vibe, emotion, craft.

**User prompt:** Show title(s), genres, overview(s).

**Output:** Parsed bullet list → string array.

#### Concept-Based Recommendations (`src/lib/ai/prompts/recommendations.ts`)

**System prompt:** Recommend shows based on selected concepts. Each reason must name which concepts align. Bias recent but allow classics/hidden gems. Return title, external ID (TMDB ID), media type for catalog resolution.

**Counts:** 5 for Explore Similar, 6 for Alchemy.

**Output format:** JSON array of `{ title, externalId, mediaType, reason }`.

#### Conversation Summarization (`src/lib/ai/prompts/summarize.ts`)

Summarize older turns into 1–2 sentences preserving persona tone.

### 7.3 Output Parser (`src/lib/ai/parser.ts`)

- Parse `showList` string format: `Title::externalId::mediaType;;...`
- Parse concept bullet lists → string arrays
- Parse recommendation JSON → typed objects
- Fallback: if parsing fails, retry once with stricter formatting, then fall back to unstructured commentary + Search handoff

### 7.4 Show Resolution Pipeline

When AI returns recommendations:
1. Extract `externalId` and `mediaType` from structured output
2. Call TMDB by ID: `getMovieDetails(id)` or `getTVDetails(id)`
3. Verify title matches case-insensitively
4. If match: map to Show object, attach AI reason as transient data
5. If no match: mark as unresolved (show title as non-interactive or link to search)

---

## 8. API Routes

### 8.1 Collection CRUD

| Route | Method | Purpose |
|---|---|---|
| `/api/shows` | GET | Get user's collection (with optional filters) |
| `/api/shows` | POST | Save/upsert a show (handles auto-save rules) |
| `/api/shows/[id]` | GET | Get single show from collection |
| `/api/shows/[id]` | PATCH | Update My Data fields |
| `/api/shows/[id]` | DELETE | Remove from collection |
| `/api/shows/[id]/tags` | PATCH | Add/remove tags |

### 8.2 External Catalog

| Route | Method | Purpose |
|---|---|---|
| `/api/search` | GET | Search TMDB (`?query=...&page=...`) |
| `/api/show/[id]` | GET | Fetch TMDB details + merge with stored show if exists |
| `/api/person/[id]` | GET | Fetch TMDB person details |

### 8.3 AI

| Route | Method | Purpose |
|---|---|---|
| `/api/ai/scoop` | POST | Generate Scoop (streaming response) |
| `/api/ai/ask` | POST | Ask chat turn (streaming response with mentions) |
| `/api/ai/concepts` | POST | Generate concepts for 1+ shows |
| `/api/ai/recommendations` | POST | Get concept-based recommendations |

### 8.4 Settings & Data

| Route | Method | Purpose |
|---|---|---|
| `/api/settings` | GET/PATCH | Read/update cloud settings |
| `/api/export` | GET | Export user data as JSON zip |
| `/api/test/reset` | POST | Reset namespace data (dev/test only) |

### 8.5 Auto-Save Business Logic (in POST `/api/shows`)

Implement the saving triggers and defaults from PRD 5.2–5.3:

```typescript
function applySaveDefaults(show: Partial<Show>, trigger: 'status' | 'interest' | 'rating' | 'tag'): Partial<Show> {
  if (trigger === 'rating' && !show.myStatus) {
    show.myStatus = 'done';
    show.myStatusUpdateDate = new Date().toISOString();
  }
  if (trigger === 'tag' && !show.myStatus) {
    show.myStatus = 'later';
    show.myInterest = 'interested';
    show.myStatusUpdateDate = new Date().toISOString();
    show.myInterestUpdateDate = new Date().toISOString();
  }
  if ((trigger === 'interest' || trigger === 'status') && !show.myStatus) {
    show.myStatus = 'later';
    show.myInterest = 'interested';
    show.myStatusUpdateDate = new Date().toISOString();
    show.myInterestUpdateDate = new Date().toISOString();
  }
  return show;
}
```

---

## 9. UI Implementation

### 9.1 Collection Home (`/`)

**Layout:**
- Left sidebar: filter panel (All Shows, tag filters, genre/decade/score filters)
- Top: media type toggle (All / Movies / TV)
- Main: show tiles grouped by status sections

**Status sections (in order):**
1. **Active** — larger tiles, prominent placement
2. **Excited** — Later + Excited interest
3. **Interested** — Later + Interested interest
4. **Other** — collapsible group containing Wait, Quit, Done

**Show tiles:** Poster image + title + badges (in-collection dot, user rating if set).

**Empty states:**
- No collection: CTA to Search/Ask
- Filter yields nothing: "No results found"

**Data flow:** Fetch from `/api/shows` with filter params. Client-side grouping by status/interest.

### 9.2 Find/Discover Hub (`/find`)

**Mode switcher:** Tabs for Search | Ask | Alchemy

#### Search Mode

- Text input with debounced search
- Calls `/api/search?query=...`
- Results in poster grid
- In-collection shows marked with badge
- Tap opens `/show/[id]`
- "Search on Launch" setting: if enabled, auto-open search on app load

#### Ask Mode

- Chat interface with user/assistant message bubbles
- Welcome state: 6 random starter prompts (from a curated set of ~80), refresh button
- User types message → POST `/api/ai/ask` with conversation history
- Streaming response rendered progressively
- Mentioned shows strip: horizontal scrollable row below the response, parsed from `showList`
- Tap mentioned show → navigate to `/show/[id]`
- After ~10 messages, older turns summarized automatically
- "Ask About a Show" variant: seed conversation with show context when navigating from Detail

#### Alchemy Mode

Multi-step flow with clear step indicators:

**Step 1 — Select Shows (2+):**
- Search bar to find shows (library + global catalog)
- Selected shows displayed as removable chips/cards
- "Conceptualize Shows" button enabled at 2+

**Step 2 — Select Concepts:**
- POST `/api/ai/concepts` with selected shows
- Display concept chips (selectable, max 8)
- "ALCHEMIZE!" button enabled at 1+ selected

**Step 3 — Results:**
- POST `/api/ai/recommendations` with selected concepts
- Display 6 recommendation cards with reasons
- Each card tappable → `/show/[id]`
- "More Alchemy!" button to chain (results become new inputs, back to step 1)

**Backtracking:** Changing shows clears concepts/results. Changing concepts clears results.

### 9.3 Show Detail (`/show/[id]`)

Fetch from `/api/show/[id]` (merges TMDB details with stored user data).

**Section order (matches detail_page_experience.md):**

1. **Header carousel:** Backdrops, posters, logos. Trailer if available (inline playback). Fallback to poster/logo if no backdrops.

2. **Core facts + community score:** Year, runtime (movies) or seasons/episodes (TV), community score bar.

3. **Status toolbar (sticky/fixed):** Status chips — Active, Interested, Excited, Done, Quit, Wait. Selecting sets status (Interested/Excited set `Later + interest`). Reselecting active status triggers removal confirmation.

4. **My Rating:** Slider/star bar. Rating an unsaved show auto-saves as Done.

5. **My Tags:** Tag chips + "Add tag" picker. Adding tag to unsaved show auto-saves as Later + Interested.

6. **Overview:** Text block, truncated with expand.

7. **The Scoop:** Toggle button:
   - No scoop: "Give me the scoop!"
   - Cached scoop (< 4 hours): "Show the scoop"
   - Open: displays scoop with "The Scoop" header
   - Generation: streaming text display with "Generating..." state
   - Freshness: re-generate after 4 hours on next request
   - Persistence: only saved if show is in collection

8. **"Ask about this show" CTA:** Navigates to Ask mode with show context.

9. **Genres + languages.**

10. **Recommendations strand:** Horizontal scrollable strip of similar/recommended shows from TMDB.

11. **Explore Similar:**
    - "Get Concepts" button → POST `/api/ai/concepts`
    - Concept chips (selectable, same cap as Alchemy)
    - "Explore Shows" button → POST `/api/ai/recommendations` (5 recs)
    - Results as cards with reasons

12. **Streaming providers:** Grouped by type (stream, rent, buy) with provider logos.

13. **Cast & Crew:** Horizontal strips. Tap → `/person/[id]`.

14. **Seasons (TV only):** Expandable season list with episode counts.

15. **Budget vs Revenue (movies, when available).**

### 9.4 Person Detail (`/person/[id]`)

Fetch from `/api/person/[id]`.

- **Header:** Image gallery, name, bio
- **Analytics:** Simple charts (average ratings, top genres, projects-by-year) using lightweight charting (e.g., Recharts or CSS-based)
- **Filmography:** Credits grouped by year. Tap credit → `/show/[id]`

### 9.5 Settings (`/settings`)

**App settings:**
- Font size selector (XS through XXL)
- Search on launch toggle

**User:**
- Username field (saved to CloudSettings)

**AI:**
- AI API key field (saved to CloudSettings, never committed)
- AI model selector

**Integrations:**
- Catalog API key field (TMDB key override)

**Your Data:**
- "Export My Data" button → downloads `.zip` containing JSON backup (ISO-8601 dates)
- Import/Restore: placeholder UI noting "coming soon" (PRD open question)

### 9.6 Removal Confirmation Dialog

When removing a show (reselecting active status):
- Confirmation dialog: "Remove [Title] from your collection? This will clear all your data for this show."
- "Don't ask again" checkbox after repeated removals
- Tracks `hideStatusRemovalConfirmation` and `statusRemovalCountKey` in local state

---

## 10. Cross-Cutting Concerns

### 10.1 User Data Overlay Rule

Every component that renders a show must check if the user has a saved version and display the overlaid version (status, tags, rating, scoop). This applies to: search results, recommendations, mentioned shows, Alchemy results, Explore Similar results, recommendation strands.

Implementation: after fetching any list of shows from TMDB or AI, batch-check the user's collection for matches by ID and merge user data onto the display objects.

### 10.2 Streaming AI Responses

Use Server-Sent Events (SSE) or the Vercel AI SDK (`ai` package) for streaming:
- Scoop: stream text chunks to client, render progressively
- Ask: stream commentary, parse `showList` from final structured output
- Concepts and recommendations: non-streaming JSON responses (fast enough)

### 10.3 Error Handling

- TMDB failures: show graceful error states, allow retry
- AI failures: retry once, then fall back (unstructured text + search handoff for mentions)
- Supabase failures: error toast, no silent data loss
- Invalid structured output from AI: retry with stricter prompt, then degrade gracefully

### 10.4 Local UI State

Stored in `localStorage` (disposable per infra rider 6.2):
- `hideStatusRemovalConfirmation`
- `statusRemovalCountKey`
- `lastSelectedFilter`
- `autoSearch`
- `fontSize`

### 10.5 Data Continuity (Migrations)

- `app_metadata.data_model_version` tracks schema version
- On startup, check version and run any needed migrations
- Migrations are additive (add columns, transform data) — never destructive to user data

---

## 11. Build Order (Implementation Phases)

### Phase 1: Foundation
1. Project setup (Next.js, Tailwind, TypeScript, Supabase client)
2. Environment variables + `.env.example`
3. Database migrations (shows, cloud_settings, app_metadata tables)
4. Middleware (namespace + user identity injection)
5. Supabase server client with namespace scoping
6. Shared types (Show, Status, Interest, Filter, etc.)

### Phase 2: Data Layer
7. Show repository (CRUD operations)
8. Merge logic (selectFirstNonEmpty, timestamp resolution)
9. Settings repository
10. TMDB client (search, movie details, TV details, person details)
11. TMDB → Show mapper

### Phase 3: Core UI Shell
12. Root layout with sidebar + top nav
13. Collection Home page (status-grouped grid)
14. Show tiles with badges
15. Media type toggle
16. Filter sidebar (All Shows, tags, genres, decades, scores)

### Phase 4: Show Detail
17. Detail page layout (all 15 sections)
18. Status toolbar with save/remove logic
19. Rating bar with auto-save-as-Done
20. Tag picker with auto-save-as-Later
21. Header carousel (images, poster fallback)
22. Core facts, overview, genres, languages
23. Streaming providers display
24. Cast/crew strips
25. Seasons (TV), Budget/Revenue (movies)
26. Recommendations strand (TMDB similar/recommended)

### Phase 5: AI Features
27. AI client setup (OpenAI-compatible)
28. Scoop generation + streaming display
29. Scoop caching (4-hour freshness)
30. Ask chat UI + streaming responses
31. Ask mentioned shows parsing + strip display
32. Ask conversation summarization (~10 message threshold)
33. Concept generation (single-show + multi-show)
34. Concept-based recommendations
35. Show resolution pipeline (AI output → TMDB lookup → real Show)
36. Explore Similar flow on Detail page

### Phase 6: Discovery Features
37. Search (Find → Search) with TMDB integration
38. Ask (Find → Ask) with welcome state + starter prompts
39. "Ask about this show" from Detail → Ask with context
40. Alchemy multi-step flow (select → conceptualize → alchemize → chain)

### Phase 7: Supporting Features
41. Person Detail page (bio, analytics, filmography)
42. Settings page (font size, search on launch, username, API keys)
43. Export My Data (JSON zip download)
44. Test data reset endpoint
45. Removal confirmation dialog with "don't ask again"

### Phase 8: Polish & Quality
46. Empty states (no collection, no results, no concepts)
47. Loading states and skeleton screens
48. Error handling and retry logic
49. User data overlay everywhere shows appear
50. Responsive layout
51. Accessibility basics (keyboard nav, ARIA labels, focus management)

---

## 12. Testing Strategy

### 12.1 Unit Tests
- Merge logic (selectFirstNonEmpty, timestamp resolution edge cases)
- Auto-save defaults (rating→Done, tag→Later+Interested, etc.)
- TMDB mapper (field mapping, type inference, logo selection)
- AI output parser (showList format, concept bullets, recommendation JSON)
- Filter logic (genre, decade, score, tag, media type)

### 12.2 Integration Tests
- API routes: CRUD operations with Supabase, namespace isolation verification
- AI routes: mock AI responses, verify parsing + resolution pipeline
- TMDB routes: mock TMDB responses, verify mapping + merge

### 12.3 Namespace Isolation Test
- Create data in namespace A
- Verify namespace B cannot read it
- Reset namespace A
- Verify data is gone in A, B is unaffected

### 12.4 Scripts
- `npm run dev` — start Next.js dev server
- `npm test` — run test suite
- `npm run test:reset` — call reset endpoint to clear namespace test data
- `npm run db:migrate` — apply Supabase migrations

---

## 13. Key Risks & Mitigations

| Risk | Mitigation |
|---|---|
| AI structured output unreliable | Retry with stricter prompt; graceful degradation to unstructured text |
| TMDB rate limits | Server-side caching of detail responses; debounced search |
| Scoop freshness creates stale UX | 4-hour TTL with visual regeneration affordance |
| Large collection performance | Paginate collection queries; virtual scrolling for long lists |
| Show ID collisions (movie vs TV) | Prefix IDs with media type (`movie-123`, `tv-456`) |
| Alchemy concept quality varies | Quality constraints in prompts; concept validation (reject generics) |

---

## 14. Compliance Checklist (Infra Rider)

- [x] `.env.example` with all required variables
- [x] `.gitignore` excludes `.env*` (except `.env.example`)
- [x] Configurable without code edits
- [x] `npm run dev`, `npm test`, `npm run test:reset` scripts
- [x] SQL migrations for deterministic schema creation
- [x] `namespace_id` on all persisted data
- [x] `user_id` on all user-owned records
- [x] Partition: `(namespace_id, user_id)`
- [x] Dev identity injection (`X-User-Id` header + default)
- [x] Schema supports real OAuth swap (just change identity source)
- [x] Server-side source of truth; client cache is disposable
- [x] Namespace-scoped test data reset without global teardown
- [x] No Docker required
- [x] Anon key for browser; service role key server-only
