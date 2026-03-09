# Implementation Plan — ShowCollector

A personal TV & movie companion for collecting, organizing, rating, and discovering entertainment with AI-powered surfaces. Built on **Next.js** (latest stable) with **Supabase** as the persistence layer.

---

## Table of Contents

1. [Architecture Overview](#1-architecture-overview)
2. [Project Structure](#2-project-structure)
3. [Database Schema & Migrations](#3-database-schema--migrations)
4. [Identity & Isolation Model](#4-identity--isolation-model)
5. [Data Layer & Business Rules](#5-data-layer--business-rules)
6. [API Design](#6-api-design)
7. [Feature Build-Out](#7-feature-build-out)
8. [AI Integration](#8-ai-integration)
9. [Cross-Cutting Concerns](#9-cross-cutting-concerns)
10. [Developer Experience & Scripts](#10-developer-experience--scripts)
11. [Testing Strategy](#11-testing-strategy)
12. [Implementation Phases](#12-implementation-phases)

---

## 1. Architecture Overview

### System Topology

```
┌──────────────────────────────────────────────────┐
│                   Next.js App                     │
│                                                   │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────┐ │
│  │ React Server│  │ Client       │  │ API      │ │
│  │ Components  │  │ Components   │  │ Routes   │ │
│  └──────┬──────┘  └──────┬───────┘  └────┬─────┘ │
│         │                │               │        │
│         └────────┬───────┘               │        │
│                  ▼                       ▼        │
│         ┌────────────────┐    ┌─────────────────┐ │
│         │ Supabase Client│    │ AI Proxy Layer  │ │
│         │ (anon key)     │    │ (server-only)   │ │
│         └───────┬────────┘    └────────┬────────┘ │
└─────────────────┼──────────────────────┼──────────┘
                  ▼                      ▼
         ┌────────────────┐    ┌─────────────────┐
         │   Supabase     │    │  AI Provider    │
         │   (Postgres +  │    │  (configurable) │
         │    Auth + RLS) │    │                 │
         └────────────────┘    └─────────────────┘
                  ▲
                  │
         ┌────────────────┐
         │ External Catalog│
         │ API (TMDB etc) │
         └────────────────┘
```

### Key Architectural Decisions

- **Server Components by default.** Client Components only where interactivity is required (status chips, chat UI, rating input, tag editing, filter panel, Alchemy flow).
- **Supabase client library** for all persistence — no raw SQL from the application layer.
- **Row-Level Security (RLS)** enforces `(namespace_id, user_id)` isolation at the database level.
- **AI calls are server-only.** All AI provider keys stay on the server; browser code never touches them. API routes proxy AI requests and stream responses via SSE.
- **External catalog API** (e.g., TMDB) accessed server-side to avoid exposing API keys. Results mapped to the `Show` data model before reaching the client.
- **Environment-driven configuration.** Every tunable value (Supabase URL, keys, namespace ID, AI provider, catalog API key) comes from environment variables. No code edits needed to switch environments.

---

## 2. Project Structure

```
/
├── .env.example                  # All required env vars with comments
├── .gitignore                    # Includes .env*, node_modules, .next
├── next.config.js
├── package.json
├── tsconfig.json
│
├── supabase/
│   ├── migrations/
│   │   ├── 001_create_shows.sql
│   │   ├── 002_create_cloud_settings.sql
│   │   ├── 003_create_app_metadata.sql
│   │   └── 004_rls_policies.sql
│   └── seed.sql                  # Optional fixture data
│
├── src/
│   ├── app/
│   │   ├── layout.tsx            # Root layout, font/theme providers
│   │   ├── page.tsx              # Collection Home
│   │   ├── search/
│   │   │   └── page.tsx          # Search results
│   │   ├── ask/
│   │   │   └── page.tsx          # Ask (AI chat) UI
│   │   ├── alchemy/
│   │   │   └── page.tsx          # Alchemy multi-show blending
│   │   ├── show/[id]/
│   │   │   └── page.tsx          # Show Detail
│   │   ├── person/[id]/
│   │   │   └── page.tsx          # Person Detail
│   │   ├── settings/
│   │   │   └── page.tsx          # Settings & Your Data
│   │   └── api/
│   │       ├── shows/            # Collection CRUD
│   │       ├── tags/             # Tag library
│   │       ├── search/           # Catalog API proxy
│   │       ├── ai/
│   │       │   ├── ask/          # Chat endpoint (streaming)
│   │       │   ├── scoop/        # Scoop generation (streaming)
│   │       │   ├── concepts/     # Concept extraction
│   │       │   └── recommend/    # Concept-based recommendations
│   │       ├── settings/         # Cloud settings CRUD
│   │       └── export/           # Backup/export
│   │
│   ├── components/
│   │   ├── collection/           # Collection home, status groups, tiles
│   │   ├── show/                 # Detail page sections, status chips, rating
│   │   ├── person/               # Person detail, filmography
│   │   ├── search/               # Search bar, poster grid
│   │   ├── ai/                   # Chat UI, scoop display, concept chips
│   │   ├── alchemy/              # Alchemy flow steps
│   │   ├── filters/              # Filter panel, tag/genre/decade pickers
│   │   ├── settings/             # Settings form components
│   │   └── ui/                   # Shared primitives (chips, toolbar, cards)
│   │
│   ├── lib/
│   │   ├── supabase/
│   │   │   ├── client.ts         # Browser Supabase client (anon key)
│   │   │   ├── server.ts         # Server Supabase client (service role)
│   │   │   └── types.ts          # Generated/manual DB types
│   │   ├── catalog/
│   │   │   ├── client.ts         # External catalog API client
│   │   │   └── mapper.ts         # Map external data → Show model
│   │   ├── ai/
│   │   │   ├── provider.ts       # AI provider abstraction
│   │   │   ├── prompts.ts        # System prompts per surface
│   │   │   └── context.ts        # User library context builder
│   │   ├── models/
│   │   │   └── show.ts           # Show type, MyStatus, MyInterest, etc.
│   │   ├── business-rules.ts     # Save triggers, defaults, removal, merge
│   │   └── utils.ts              # Shared helpers
│   │
│   └── middleware.ts             # Dev auth injection (X-User-Id / default)
│
└── scripts/
    ├── dev.sh                    # One-command start
    ├── test.sh                   # Run tests
    └── reset-data.sh             # Namespace-scoped data reset
```

---

## 3. Database Schema & Migrations

### 3.1 `shows` Table

The central table. Stores both canonical catalog data and user-specific overlay ("My Data"). Scoped by `namespace_id` and `user_id`.

```sql
CREATE TABLE shows (
  -- Identity
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  namespace_id    TEXT NOT NULL,
  user_id         TEXT NOT NULL,
  external_id     TEXT,               -- TMDB ID or similar
  title           TEXT NOT NULL,
  show_type       TEXT NOT NULL CHECK (show_type IN ('movie', 'tv', 'person', 'unknown')),

  -- Catalog metadata
  overview        TEXT,
  tagline         TEXT,
  homepage        TEXT,
  genres          JSONB DEFAULT '[]',
  languages       JSONB DEFAULT '[]',
  spoken_languages JSONB DEFAULT '[]',

  -- Images
  poster_url      TEXT,
  backdrop_url    TEXT,
  logo_url        TEXT,
  network_logos   JSONB DEFAULT '[]',

  -- Ratings
  vote_average    REAL,
  vote_count      INTEGER,
  popularity      REAL,

  -- Dates
  release_date    TEXT,
  first_air_date  TEXT,
  last_air_date   TEXT,

  -- Movie-specific
  runtime         INTEGER,
  budget          BIGINT,
  revenue         BIGINT,

  -- TV-specific
  series_status   TEXT,
  number_of_episodes INTEGER,
  number_of_seasons  INTEGER,
  episode_run_time   JSONB DEFAULT '[]',

  -- User data ("My Data")
  my_status       TEXT CHECK (my_status IN ('active', 'next', 'later', 'done', 'quit', 'wait')),
  my_status_update_date    TIMESTAMPTZ,
  my_interest     TEXT CHECK (my_interest IN ('excited', 'interested')),
  my_interest_update_date  TIMESTAMPTZ,
  my_score        REAL,
  my_score_update_date     TIMESTAMPTZ,
  my_tags         JSONB DEFAULT '[]',
  my_tags_update_date      TIMESTAMPTZ,

  -- AI data
  ai_scoop        TEXT,
  ai_scoop_update_date     TIMESTAMPTZ,

  -- Providers
  provider_data   JSONB,

  -- Management
  details_update_date TIMESTAMPTZ DEFAULT NOW(),
  creation_date       TIMESTAMPTZ DEFAULT NOW(),
  is_test             BOOLEAN DEFAULT FALSE,

  -- Constraints
  UNIQUE (namespace_id, user_id, external_id)
);

CREATE INDEX idx_shows_namespace_user ON shows (namespace_id, user_id);
CREATE INDEX idx_shows_status ON shows (namespace_id, user_id, my_status);
CREATE INDEX idx_shows_type ON shows (namespace_id, user_id, show_type);
CREATE INDEX idx_shows_external ON shows (namespace_id, user_id, external_id);
```

### 3.2 `cloud_settings` Table

```sql
CREATE TABLE cloud_settings (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  namespace_id  TEXT NOT NULL,
  user_id       TEXT NOT NULL,
  user_name     TEXT,
  version       BIGINT DEFAULT 0,
  catalog_api_key TEXT,
  ai_api_key    TEXT,
  ai_model      TEXT,
  updated_at    TIMESTAMPTZ DEFAULT NOW(),

  UNIQUE (namespace_id, user_id)
);
```

### 3.3 `app_metadata` Table

```sql
CREATE TABLE app_metadata (
  id                UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  namespace_id      TEXT NOT NULL,
  data_model_version INTEGER DEFAULT 3,

  UNIQUE (namespace_id)
);
```

### 3.4 Row-Level Security

```sql
-- Enable RLS on all tables
ALTER TABLE shows ENABLE ROW LEVEL SECURITY;
ALTER TABLE cloud_settings ENABLE ROW LEVEL SECURITY;
ALTER TABLE app_metadata ENABLE ROW LEVEL SECURITY;

-- Shows: users see only their own data within their namespace
CREATE POLICY "shows_isolation" ON shows
  USING (
    namespace_id = current_setting('app.namespace_id', TRUE)
    AND user_id = current_setting('app.user_id', TRUE)
  );

-- Cloud settings: same isolation
CREATE POLICY "settings_isolation" ON cloud_settings
  USING (
    namespace_id = current_setting('app.namespace_id', TRUE)
    AND user_id = current_setting('app.user_id', TRUE)
  );

-- App metadata: namespace-scoped only
CREATE POLICY "metadata_isolation" ON app_metadata
  USING (
    namespace_id = current_setting('app.namespace_id', TRUE)
  );
```

Alternative approach for RLS: pass `namespace_id` and `user_id` via Supabase client headers or JWT claims rather than Postgres session settings, depending on whether hosted or local Supabase is used.

---

## 4. Identity & Isolation Model

### Namespace Isolation

- `NAMESPACE_ID` set via environment variable (e.g., `NAMESPACE_ID=benchmark_run_42`).
- Every database query is scoped to this namespace via RLS or explicit `WHERE` clause.
- Two namespaces never read or write each other's data.
- Destructive testing (create/delete test data) is scoped to the current namespace only.

### User Identity

- `user_id` is an opaque stable string (UUID recommended).
- For benchmark: dev identity injection via middleware:
  - `X-User-Id` request header, OR
  - Dev user selector in the UI (dropdown/input), OR
  - Fixed default user from env (`DEFAULT_USER_ID`)
- Middleware reads the identity signal and sets it on the Supabase client context.
- Clearly documented and gated behind `NODE_ENV=development` or a feature flag.

### OAuth Migration Path

- Schema uses opaque `user_id` — no assumption about format.
- When real auth is added: replace dev middleware with OAuth flow, map authenticated user to `user_id`.
- No schema redesign required. Only the middleware/auth layer changes.

---

## 5. Data Layer & Business Rules

### 5.1 Collection Membership

A show is "in the collection" if and only if it has a non-null `my_status`. All other My Data fields are optional overlays.

### 5.2 Save Triggers & Defaults

| User Action | Resulting State |
|---|---|
| Set status explicitly | `my_status` = chosen status |
| Tap interest chip (on unsaved show) | `my_status` = "later", `my_interest` = chosen interest |
| Rate an unsaved show | `my_status` = "done", `my_score` = chosen score |
| Add tag to unsaved show | `my_status` = "later", `my_interest` = "interested" |

### 5.3 Interest Constraints

- `my_interest` is only valid when `my_status` = "later".
- Setting status to anything other than "later" clears `my_interest` to null.
- Valid values: "interested", "excited".

### 5.4 Removal

- Removing a show from the collection clears **all** My Data (`my_status`, `my_interest`, `my_score`, `my_tags`, `ai_scoop`).
- Confirmation dialog required. Tracks dismissal count via `hideStatusRemovalConfirmation` / `statusRemovalCountKey` in local UI state.

### 5.5 Re-Adding a Previously Removed Show

- If the show record still exists (matched by `external_id`), re-adding preserves the latest data.
- Merge conflicts resolved by comparing `*_update_date` timestamps — most recent wins.
- For non-user fields: `selectFirstNonEmpty()` — take the first non-null/non-empty value from the new data, falling back to existing.

### 5.6 Timestamp Tracking

Every user-modifiable field has a companion `*_update_date` column. Updated atomically when the field changes. Used for:
- Cross-device sync conflict resolution (latest timestamp wins).
- Merge logic on re-add.
- Scoop cache expiry (4-hour TTL from `ai_scoop_update_date`).

### 5.7 Auto-Save Behavior

All user interactions auto-save immediately. No explicit "save" button. Status changes, ratings, tags, and interest selections persist on interaction.

---

## 6. API Design

### 6.1 Collection API (`/api/shows`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/shows` | List user's collection. Query params: `status`, `showType`, `tag`, `genre`, `decade`, `minScore`, `sortBy` |
| GET | `/api/shows/[id]` | Get single show with full detail |
| POST | `/api/shows` | Save show to collection (upsert by external_id) |
| PATCH | `/api/shows/[id]` | Update My Data fields (status, interest, score, tags) |
| DELETE | `/api/shows/[id]` | Remove from collection (clears all My Data) |

### 6.2 Tag API (`/api/tags`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/tags` | List user's tag library (distinct tags across collection) |

### 6.3 Search API (`/api/search`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/search?q=...&type=...` | Proxy to external catalog API, map results to Show model, annotate with collection membership |

### 6.4 AI API (`/api/ai/*`)

All AI endpoints are **server-only** (service-role or server-side API keys).

| Method | Path | Description |
|---|---|---|
| POST | `/api/ai/ask` | Streaming chat. Body: messages array, optional show context. Returns SSE stream. |
| POST | `/api/ai/scoop` | Generate Scoop for a show. Body: show data + user library context. Returns SSE stream. |
| POST | `/api/ai/concepts` | Extract concepts from 1+ shows. Body: show IDs/data. Returns JSON array of concept strings. |
| POST | `/api/ai/recommend` | Get recommendations from selected concepts. Body: concepts array, source show(s). Returns JSON array of recommendations with reasoning. |

### 6.5 Settings API (`/api/settings`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/settings` | Get user's cloud settings |
| PATCH | `/api/settings` | Update settings (username, AI provider, catalog API key, AI model) |

### 6.6 Export API (`/api/export`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/export` | Export full collection as JSON (all shows with My Data) |

---

## 7. Feature Build-Out

### 7.1 Collection Home (`/`)

The default landing page. Shows the user's saved collection.

**Layout:**
- Top bar: media-type toggle (All / Movies / TV)
- Filter panel (collapsible): filter by status, tags, genres, decades, community score
- Main area: shows grouped by status (Active → Later → Wait → Done → Quit), displayed as poster tile grid
- Each tile: poster image, title, user rating badge (if rated), collection membership indicator

**Interactions:**
- Tap tile → navigate to Show Detail
- Filter selections narrow the visible set
- Empty state: prompt to search or discover

### 7.2 Search (`/search`)

**Layout:**
- Search input (text field, auto-focus if `autoSearch` setting enabled)
- Results: poster grid with tiles
- Each tile annotated with collection membership badge if already saved

**Data flow:**
1. User types query → debounced call to `/api/search?q=...`
2. API proxies to external catalog, maps to Show model
3. Cross-reference with user's collection (by external_id) to annotate tiles
4. Tap tile → navigate to Show Detail

### 7.3 Ask — AI Chat (`/ask`)

**Layout:**
- Chat message list (scrollable)
- Input bar at bottom
- On first load: display 3-5 random starter prompts from a pool of ~80 (per AI personality spec)

**Data flow:**
1. User sends message → POST `/api/ai/ask` with message history + user library context
2. Stream response via SSE → display progressively
3. AI may mention shows in structured format (showList) → render as tappable show cards inline
4. Tapping a mentioned show → navigate to Show Detail
5. Session context is short-term — cleared on page exit

**AI contracts:**
- System prompt sets voice/personality per `ai_voice_personality.md`
- Include user's library (saved shows, statuses, ratings) as context per `ai_prompting_context.md`
- Conversation summarization for older turns (1-2 sentences) to manage context window
- Guardrail: if structured parsing fails, fall back to unstructured text + suggest Search

### 7.4 Alchemy (`/alchemy`)

A structured 5-step discovery flow that blends multiple shows into shared concepts, then generates recommendations.

**Step 1 — Select Shows:**
- User picks 2+ shows from their collection (or searches to add)
- Minimum 2, no hard maximum, UI optimized for 2-5
- Display selected shows as a visual row

**Step 2 — Conceptualize:**
- POST `/api/ai/concepts` with selected shows
- AI returns 8 concept chips (1-3 words each, evocative, per `concept_system.md`)
- Display as selectable chip row

**Step 3 — Select Concepts:**
- User selects 1-8 concepts from the generated set
- Selected concepts highlighted

**Step 4 — Recommend:**
- POST `/api/ai/recommend` with selected concepts + source shows
- AI returns 6 recommendations, each with reasoning that names matching concepts
- Display as cards with poster, title, and reasoning text
- Each recommendation must map to a real show (external ID verification)

**Step 5 — Iterate or Save:**
- User can save any recommended show (triggers save flow with default status)
- User can go back to concept selection and re-run with different concepts
- User can start over with different shows

### 7.5 Show Detail (`/show/[id]`)

The single source of truth for a show. 13 sections in narrative order per `detail_page_experience.md`:

**Section 1 — Header Media Carousel:**
- Backdrop images (horizontal scroll if multiple)
- Fallback to poster if no backdrops available

**Section 2 — Core Facts + Community Score:**
- Year, runtime/episode count, media type badge
- Community score (vote_average) with vote count
- User's rating display (if rated)

**Section 3 — Status Toolbar:**
- Horizontal chip row: Active, Later, Wait, Done, Quit
- Current status highlighted
- Tapping a chip sets/changes status (auto-save)
- If unsaved: tapping any status chip saves with that status
- Interest sub-chips (Interested/Excited) appear only when status = Later

**Section 4 — My Tags:**
- Tag chips (horizontally scrollable)
- Add tag input (free-form text, autocomplete from tag library)
- Adding tag to unsaved show triggers save (Later + Interested)

**Section 5 — Overview + Scoop:**
- Show overview (from catalog)
- Scoop toggle button:
  - Unsaved show: "Get the Scoop"
  - Saved show: "Your Scoop"
- Scoop content area:
  - Streams progressively (SSE from `/api/ai/scoop`)
  - Cached for 4 hours (check `ai_scoop_update_date`)
  - Structure: personal take, honest comparison, centerpiece paragraph, fit/warnings, verdict (~150-350 words)
  - Refresh button after 4-hour expiry

**Section 6 — "Ask about this show" CTA:**
- Button navigates to Ask with this show pre-loaded as context

**Section 7 — Genres + Languages:**
- Genre chips, language list

**Section 8 — Recommendations Strand:**
- Pre-fetched related shows from catalog API (transient, not stored)
- Horizontal scrollable poster row
- Tiles annotated with collection membership

**Section 9 — Explore Similar:**
- "Find Similar" button → POST `/api/ai/concepts` with this show
- Returns concept chips → user selects 1+ → POST `/api/ai/recommend`
- Returns 5 recommendations with reasoning per selected concepts
- Inline display (expandable section)

**Section 10 — Streaming Providers:**
- Display from `provider_data` (flatrate/rent/buy by region)
- Provider logos

**Section 11 — Cast:**
- Scrollable cast list with headshots and role names
- Tap → Person Detail

**Section 12 — Crew:**
- Key crew (director, writer, producer)
- Tap → Person Detail

**Section 13 — Season/Financial Info:**
- TV: season list with episode counts
- Movie: budget and revenue figures

### 7.6 Person Detail (`/person/[id]`)

**Layout:**
- Profile header: photo, name, bio
- Lightweight analytics: number of shows in user's collection featuring this person, average rating
- Filmography: grouped by role (actor, director, etc.), displayed as poster grid
- Each title annotated with collection membership

**Data:** Fetched from catalog API (transient, not stored).

### 7.7 Settings (`/settings`)

**Cloud settings** (synced to Supabase):
- Username
- AI provider selection and API key
- Catalog API key
- AI model selection

**Local settings** (device-only):
- Font size (XS through XXL)
- Auto-search on launch toggle

**Your Data section:**
- Export collection as JSON
- Backup information

---

## 8. AI Integration

### 8.1 Provider Abstraction

```
src/lib/ai/provider.ts
```

Abstract interface supporting pluggable AI providers. User configures their preferred provider and API key in Settings. The server reads keys from cloud_settings or falls back to environment defaults.

### 8.2 System Prompts

Four surface-specific system prompts per `ai_prompting_context.md`:

1. **Ask prompt:** Conversational friend, warm/opinionated, spoiler-safe. Include user's library as context. Supports structured output (showList) for mentioned titles.

2. **Scoop prompt:** Mini blog-post format — personal take, honest comparison, centerpiece "The Scoop" paragraph, fit/warnings, verdict. 150-350 words. Spoiler-free. Honest about mixed reception.

3. **Concept prompt:** Return exactly N short concept bullets (1-3 words). Evocative, spoiler-free, specific (not generic). For multi-show: concepts must be shared across all inputs. Ordered by strength.

4. **Recommendation prompt:** Given selected concepts, return N real shows with reasoning that explicitly names matching concepts. 5 for Explore Similar, 6 for Alchemy. Bias toward recent but include defensible classics. 1-2 surprises allowed if defensible.

### 8.3 Voice & Personality

Per `ai_voice_personality.md`, all AI surfaces share a consistent personality:

- **Tone:** 70% friend / 30% critic, 60% hype / 40% measured
- **Pillars:** Joy-forward, opinionated honesty, vibe-first, specific (not generic), concise by default
- **Language:** Conversational contractions, vivid adjectives tied to vibe, quick contrasts, "fit" framing
- **Adaptation:** Each surface adjusts length and structure but maintains the same voice

### 8.4 Context Building

Per `ai_prompting_context.md`, AI requests include:

- **User's library:** List of saved shows with status, interest, tags, and rating
- **Current show context:** (for Scoop and Explore Similar) full show metadata
- **Selected concepts:** (for recommendations) the user's chosen concept chips
- **Conversation history:** (for Ask) recent messages, with older turns summarized to 1-2 sentences

### 8.5 Quality Bar

Per `discovery_quality_bar.md`, every AI response is held to:

| Dimension | Minimum Score |
|---|---|
| Voice adherence | ≥ 1/2 |
| Taste alignment | ≥ 1/2 |
| Surprise without betrayal | — |
| Specificity of reasoning | — |
| Real-show integrity | = 2/2 (non-negotiable) |
| **Total** | **≥ 7/10** |

**Real-show integrity** is the hard gate: every recommended title must map to a real show with a valid external ID. Implement server-side validation by cross-referencing recommendations against the catalog API before returning results to the client.

### 8.6 Streaming

- Ask and Scoop use Server-Sent Events (SSE) for progressive rendering.
- Concept extraction and recommendation endpoints return complete JSON (non-streaming) since they produce short, structured outputs.

---

## 9. Cross-Cutting Concerns

### 9.1 Cross-Device Sync

- Backend (Supabase) is the source of truth.
- All My Data fields carry `*_update_date` timestamps.
- On conflict: most recent timestamp wins.
- Clients may cache for performance but cache is safe to clear — clearing local storage never loses data.

### 9.2 Tile Indicators

Throughout the app (search results, recommendations, Alchemy, Explore Similar), show tiles display:
- Collection membership badge (if show is saved)
- User rating badge (if rated)

Implemented by cross-referencing browse results against the user's collection by `external_id`.

### 9.3 Spoiler Safety

- AI prompts include explicit spoiler-safety instructions.
- Scoop, Ask, and Concept outputs focus on vibe/structure/tone, never plot spoilers.
- Default behavior everywhere — no toggle needed.

### 9.4 Data Export

- First-class feature, not an afterthought.
- `GET /api/export` returns the user's full collection as JSON.
- Includes all My Data, tags, ratings, statuses.
- Settings page provides a one-click download button.

### 9.5 Filter System

Filters operate over the user's collection. Supported dimensions:

| Filter Type | Values |
|---|---|
| Status | active, next, later, done, quit, wait |
| Media Type | movie, tv, all |
| Genre | Dynamic from user's collection |
| Decade | Dynamic from release dates |
| Community Score | Range (e.g., 7+, 8+) |
| My Tags | Dynamic from user's tag library |

Filters are combinable (AND logic). Persist last-selected filter in local UI state (`lastSelectedFilter`).

---

## 10. Developer Experience & Scripts

### 10.1 Environment Setup

`.env.example`:
```
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key  # Server-only

# Isolation
NAMESPACE_ID=dev_local

# Identity (dev only)
DEFAULT_USER_ID=dev-user-001

# External APIs
CATALOG_API_KEY=your-tmdb-key
CATALOG_API_BASE_URL=https://api.themoviedb.org/3

# AI (defaults, overridable per user in Settings)
DEFAULT_AI_PROVIDER=openai
DEFAULT_AI_API_KEY=your-ai-key
DEFAULT_AI_MODEL=gpt-4
```

### 10.2 Developer Scripts

```bash
# Start the app (install deps if needed, run migrations, start dev server)
./scripts/dev.sh

# Run tests
./scripts/test.sh

# Reset test data (namespace-scoped, no global teardown)
./scripts/reset-data.sh
```

### 10.3 Migration Workflow

- Migrations live in `supabase/migrations/` as numbered SQL files.
- Run via `supabase db push` (hosted) or `supabase db reset` (local).
- Repeatable: running all migrations on a fresh database produces the correct schema.
- Optional seed data in `supabase/seed.sql`.

---

## 11. Testing Strategy

### 11.1 Namespace-Scoped Testing

- Tests use a dedicated `NAMESPACE_ID` (e.g., `test_run_001`).
- Test data creation and deletion scoped to that namespace.
- No global teardown — other namespaces unaffected.
- `reset-data.sh` deletes all data for the test namespace only.

### 11.2 Test Categories

| Category | Scope | Examples |
|---|---|---|
| Unit | Business rules | Save triggers, default values, interest constraints, merge logic |
| Integration | API routes + DB | CRUD operations, filter queries, export |
| AI surface | Prompt + response | Validate structured outputs, real-show integrity check |
| E2E | Full user flows | Key user journeys from PRD Section 9 |

### 11.3 Key User Journeys to Test

Per PRD Section 9, validate these 10 flows:

1. **Build collection via browse:** Search → tap show → set status → appears in collection
2. **Rate-to-save:** Open unsaved show → rate it → auto-saved as Done
3. **Tag-to-save:** Open unsaved show → add tag → auto-saved as Later + Interested
4. **Collection maintenance:** Change status, update rating, modify tags on saved show
5. **Tag-driven filtering:** Filter collection by tag → correct subset shown
6. **Ask discovery:** Chat with AI → receive recommendations → save one → appears in collection
7. **Explore Similar:** From show detail → extract concepts → select → get recommendations
8. **Alchemy chaining:** Select shows → conceptualize → select concepts → get recommendations → save → repeat
9. **Talent deep-dive:** From show → tap cast member → view filmography → navigate to another show
10. **Backup workflow:** Export collection → download JSON → verify contents

---

## 12. Implementation Phases

### Phase 1 — Foundation (Infrastructure + Data Layer)

**Goal:** Running app shell with database, auth, and basic CRUD.

- [ ] Initialize Next.js project with TypeScript
- [ ] Set up Supabase project (hosted or local)
- [ ] Create `.env.example` with all variables
- [ ] Write and run database migrations (shows, cloud_settings, app_metadata)
- [ ] Implement RLS policies for namespace + user isolation
- [ ] Create Supabase client helpers (browser + server)
- [ ] Implement dev auth middleware (X-User-Id header / default user)
- [ ] Define TypeScript types for Show, MyStatus, MyInterest, etc.
- [ ] Build collection CRUD API routes (`/api/shows`)
- [ ] Implement business rules: save triggers, defaults, removal, merge logic
- [ ] Create namespace-scoped reset script

### Phase 2 — Collection & Browse

**Goal:** Users can see their collection, search, and manage shows.

- [ ] Collection Home page: shows grouped by status, media-type toggle
- [ ] Show tile component with poster, title, rating badge, membership indicator
- [ ] Filter panel component: status, tags, genres, decades, community score filters
- [ ] Search page: text input, poster grid results, collection membership annotations
- [ ] Search API proxy (catalog API → Show model mapping)
- [ ] Tag system: add/remove tags, tag library query, autocomplete

### Phase 3 — Show & Person Detail

**Goal:** Rich detail pages as the single source of truth.

- [ ] Show Detail page with all 13 sections (header, facts, status toolbar, tags, overview, genres, providers, cast, crew, seasons/financials)
- [ ] Status chip toolbar: set/change status, interest sub-chips for Later
- [ ] Rating component: select score, auto-save
- [ ] Recommendations strand from catalog API
- [ ] Streaming provider display
- [ ] Person Detail page: bio, filmography grid, analytics
- [ ] Navigation between shows and people

### Phase 4 — AI Surfaces

**Goal:** All AI-powered discovery features operational.

- [ ] AI provider abstraction layer
- [ ] System prompts for all 4 surfaces (Ask, Scoop, Concepts, Recommendations)
- [ ] User library context builder
- [ ] Ask page: chat UI, starter prompts, streaming responses, show cards inline
- [ ] Scoop on Show Detail: toggle, streaming, 4-hour cache, refresh
- [ ] Explore Similar on Show Detail: concept extraction → chip selection → recommendations
- [ ] Alchemy page: 5-step flow (select → conceptualize → select concepts → recommend → iterate)
- [ ] Real-show integrity validation (cross-reference recommendations against catalog)
- [ ] AI voice/personality consistency across all surfaces

### Phase 5 — Settings, Export & Polish

**Goal:** Full feature completeness and quality.

- [ ] Settings page: cloud settings (username, AI config, catalog key) + local settings (font size, auto-search)
- [ ] Export/backup: one-click JSON download of full collection
- [ ] Cross-device sync verification (timestamp conflict resolution)
- [ ] Tile indicators everywhere (collection membership + ratings on all browse surfaces)
- [ ] Empty states for all pages
- [ ] Error handling for AI failures (graceful fallback to search)
- [ ] Conversation summarization for Ask (older turns compressed)
- [ ] End-to-end testing of all 10 key user journeys
