# Implementation Plan: TV & Movie Companion App

## Overview

This plan covers a full implementation of the personal TV + movie companion app described in the PRD, built on Next.js (latest stable) + Supabase, with a taste-aware AI layer for Ask, Scoop, Concepts, Explore Similar, and Alchemy discovery.

The plan is organized into phases. Phases 1–3 establish the foundation. Phases 4–6 build the feature-complete product. Phase 7 covers infrastructure and devops requirements from the Infra Rider PRD.

---

## Phase 1: Project Foundation

### 1.1 Repository Setup

- Initialize Next.js (App Router) project with TypeScript
- Configure Tailwind CSS for styling
- Install and configure Supabase client libraries (`@supabase/supabase-js`, `@supabase/ssr`)
- Install AI SDK for streaming support
- Set up ESLint + Prettier
- Create `.gitignore` that excludes all `.env*` files except `.env.example`
- Create `.env.example` with all required variables (see Phase 7.1)

**Two Supabase clients:**
- Browser client using `NEXT_PUBLIC_SUPABASE_ANON_KEY` (safe to expose)
- Server client using `SUPABASE_SERVICE_ROLE_KEY` (server-only, never sent to client)

### 1.2 Environment Configuration

All runtime configuration flows through environment variables. The build MUST run by filling in `.env` — no source code edits required. This includes:
- Supabase connection details
- External catalog API key (e.g., TMDB)
- AI provider API key + model selection
- `NAMESPACE_ID` — stable build isolation identifier
- `DEFAULT_USER_ID` — injected dev user identity

### 1.3 Identity & Isolation Model

**Namespace ID:** Sourced from `NAMESPACE_ID` env var. Set once per build/run. Immutable during runtime. Used to partition all persisted data.

**User ID:** In benchmark/dev mode, injected via:
1. `X-Dev-User-Id` request header (dev/test only), or
2. `DEFAULT_USER_ID` env var fallback

The identity injection must be:
- Gated behind `NODE_ENV !== 'production'`
- Clearly documented in README
- Designed so swapping in real Supabase Auth OAuth later is purely additive (add auth provider + RLS JWT policy; no schema changes)

All user-owned DB records carry both `namespace_id` and `user_id` columns. The effective data partition is always `(namespace_id, user_id)`.

---

## Phase 2: Database Schema & Migrations

All migrations go in `/supabase/migrations/` as numbered SQL files. A fresh database state is created deterministically by running all migrations in order.

### 2.1 `shows` Table

Stores one row per `(namespace_id, user_id, show_id)` — the user's saved version of a catalog item.

```sql
shows (
  id              uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  namespace_id    text NOT NULL,
  user_id         text NOT NULL,
  show_id         text NOT NULL,          -- external catalog ID
  title           text NOT NULL,
  show_type       text NOT NULL,          -- "movie" | "tv" | "person" | "unknown"
  external_ids    jsonb,

  -- Catalog metadata (non-my fields)
  overview        text,
  genres          text[]   DEFAULT '{}',
  tagline         text,
  homepage        text,
  original_language text,
  spoken_languages  text[] DEFAULT '{}',
  languages         text[] DEFAULT '{}',
  poster_url      text,
  backdrop_url    text,
  logo_url        text,
  network_logos   text[]  DEFAULT '{}',
  vote_average    double precision,
  vote_count      int,
  popularity      double precision,
  last_air_date   timestamptz,
  first_air_date  timestamptz,
  release_date    timestamptz,
  runtime         int,
  budget          bigint,
  revenue         bigint,
  series_status   text,
  number_of_episodes int,
  number_of_seasons  int,
  episode_run_time   int[] DEFAULT '{}',
  provider_data   jsonb,

  -- User data ("my*")
  my_tags              text[]   DEFAULT '{}',
  my_tags_update_date  timestamptz,
  my_score             double precision,
  my_score_update_date timestamptz,
  my_status            text,              -- "active" | "next" | "later" | "done" | "quit" | "wait"
  my_status_update_date timestamptz,
  my_interest          text,              -- "excited" | "interested"
  my_interest_update_date timestamptz,

  -- AI data
  ai_scoop             text,
  ai_scoop_update_date timestamptz,

  -- Management
  details_update_date  timestamptz,
  creation_date        timestamptz DEFAULT now(),
  is_test              boolean DEFAULT false,

  UNIQUE (namespace_id, user_id, show_id)
)
```

Indexes: `(namespace_id, user_id)`, `(namespace_id, user_id, my_status)`, `(namespace_id, user_id, my_tags)` using GIN for array containment queries.

### 2.2 `cloud_settings` Table

```sql
cloud_settings (
  id           uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  namespace_id text NOT NULL,
  user_id      text NOT NULL,
  settings_key text NOT NULL DEFAULT 'globalSettings',
  user_name    text,
  version      double precision,         -- epoch seconds for conflict resolution
  catalog_api_key text,                  -- optional; never committed
  ai_api_key   text,                     -- optional; never committed
  ai_model     text,
  UNIQUE (namespace_id, user_id, settings_key)
)
```

### 2.3 Row-Level Security (RLS)

Enable RLS on both tables. In dev mode, policies pass through when the `X-Dev-User-Id` header is present (server enforces this via service role). In production, policies will be rewritten to check the JWT `sub` claim — no schema changes required.

### 2.4 Namespace Reset Script

`npm run test:reset` executes:
```sql
DELETE FROM shows WHERE namespace_id = $NAMESPACE_ID;
DELETE FROM cloud_settings WHERE namespace_id = $NAMESPACE_ID;
```
No global teardown required. Destructive tests are fully scoped to their namespace.

### 2.5 Local Settings & UI State

Stored client-side (localStorage / cookie), not in Supabase:
- `autoSearch: boolean`
- `fontSize: "XS" | "S" | "M" | "L" | "XL" | "XXL"`
- `hideStatusRemovalConfirmation: boolean`
- `statusRemovalCountKey: number`
- `lastSelectedFilter: FilterConfiguration | null`

These are disposable; clearing them is safe (user data lives in Supabase).

---

## Phase 3: Core Data Layer

### 3.1 External Catalog Integration

The external catalog (e.g., TMDB) is the source of public show/person data. All catalog calls happen server-side only (API key protected).

**Key operations:**
- `searchShows(query, page)` → paginated results
- `getShowDetail(id, mediaType)` → full show object with credits, seasons, videos, recommendations, similar, providers
- `getPerson(id)` → person details + combined credits
- `resolveShowById(externalId, mediaType)` → used when resolving AI recommendations

**Catalog → Show mapping:**
1. Decode catalog payload → fresh `Show` object
2. Lookup existing stored show by `(namespace_id, user_id, show_id)`
3. Merge using field rules:
   - **Non-my fields:** `selectFirstNonEmpty(newValue, oldValue)` — never overwrite non-empty with empty/nil
   - **My fields (tags, status, interest, score):** compare timestamps, keep newer value
4. Set `details_update_date = now()`
5. Persist merged show (upsert)

Transient fields (cast, crew, seasons, videos, recommendations, similar, images) are decoded for UI but **not stored** — always re-fetched from catalog.

### 3.2 Collection Business Logic

**Auto-save triggers** (all apply to previously unsaved shows):
| Action | Status | Interest |
|--------|--------|----------|
| Set any explicit status | as selected | — |
| Choose Interested chip | Later | Interested |
| Choose Excited chip | Later | Excited |
| Rate an unsaved show | Done | — |
| Add tag to unsaved show | Later | Interested |

**Default values when saving without explicit status:**
- Status: `Later`
- Interest: `Interested`
- Exception: first save via rating → Status: `Done`

**Removal:** User reselects their current status → confirmation dialog → clear show + all My Data. After N confirmations, offer "stop asking" to set `hideStatusRemovalConfirmation = true`.

**Re-adding:** Merge preserves latest My Data (timestamp-based per field) + refreshes public metadata.

**Timestamp tracking:** Every My Data field (`myStatus`, `myInterest`, `myTags`, `myScore`, `aiScoop`) updates its corresponding `*UpdateDate` timestamp on every write.

### 3.3 Filter & Grouping Logic

Filter types:
- `all` — entire collection
- `genre` — by genre name match (array contains)
- `myTag` — by tag match (array contains); show "No tags" option if tagless shows exist
- `communityScore` — range query on `vote_average`
- `decade` — year range query on `release_date` / `first_air_date`
- `myStatus` — by status value

Media-type toggle (All/Movies/TV) applied as an additional `WHERE show_type IN (...)` clause on top of any active filter.

Home grouping (applied after filtering):
1. **Active** — `my_status = 'active'`
2. **Excited** — `my_status = 'later' AND my_interest = 'excited'`
3. **Interested** — `my_status = 'later' AND my_interest = 'interested'`
4. **Other** (collapsed) — `my_status IN ('wait', 'quit', 'done')` + any Later without interest

---

## Phase 4: API Routes (Next.js App Router)

All routes validate and inject `namespace_id` and `user_id` from server context before any DB operation.

### 4.1 Collection API

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/shows` | List user's shows; accepts `filter`, `mediaType`, `status`, `tag` query params |
| `GET` | `/api/shows/[showId]` | Get single saved show |
| `PUT` | `/api/shows/[showId]` | Upsert show (status/tags/rating/interest update) |
| `DELETE` | `/api/shows/[showId]` | Remove show + clear all My Data |
| `GET` | `/api/export` | Export full collection as ZIP (JSON inside) |

### 4.2 Catalog API

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/catalog/search` | Search catalog by `q` (title/keywords) |
| `GET` | `/api/catalog/[mediaType]/[id]` | Show detail (merges stored My Data if saved) |
| `GET` | `/api/catalog/person/[id]` | Person detail + filmography |

### 4.3 AI API

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/ai/ask` | Chat turn; returns `{ commentary, showList }` |
| `POST` | `/api/ai/scoop` | Generate/retrieve Scoop for a show |
| `POST` | `/api/ai/concepts` | Generate concepts for 1+ shows |
| `POST` | `/api/ai/recommendations` | Concept-based recs (Explore Similar or Alchemy) |
| `GET/PUT` | `/api/settings` | Read/write cloud settings for user |

### 4.4 Authentication Middleware

A Next.js middleware reads `X-Dev-User-Id` header (dev only) or `DEFAULT_USER_ID` env var and injects `userId` into request context. All API routes call this helper before touching the DB. When real OAuth is added: swap this helper to extract `userId` from the Supabase session JWT — zero schema changes.

---

## Phase 5: AI Feature Implementation

### 5.1 Ask (Chat)

**System prompt context includes:**
- User's library: saved shows with status, interest, tags, rating, scoop (top N by recency to manage token budget)
- Session conversation history (last ~10 turns; older turns summarized)
- AI persona: fun, chatty, spoiler-safe, opinionated friend (see `ai_voice_personality.md`)
- Domain restriction: TV/movies only

**Request/response contract:**
- Input: `{ messages: [...], userLibrary: [...] }`
- Output (structured): `{ commentary: string, showList: string }`
  - `showList` format: `Title::externalId::mediaType;;Title2::externalId::mediaType;;...`
- UI renders `commentary` as the chat bubble
- UI parses `showList` → resolve each to catalog item → render "Mentioned Shows" horizontal strip

**Conversation summarization:**
- After ~10 messages, summarize oldest N turns into 1–2 sentences using same AI persona/tone
- Replace old turns with summary in context before next API call

**Session context is in-memory only** (no DB persistence). Cleared on reset/leave.

**Ask About This Show variant:**
- Seeds initial system prompt with target show's metadata
- Mirrors the show's emotional tone in persona

**Welcome prompts:**
- 6 random starter prompts drawn from a pool of 80 curated prompts (stored in codebase as a constant)
- "Refresh" button re-randomizes selection

**Failure handling:**
- If structured output parsing fails: retry once with stricter formatting instructions
- If second attempt fails: fall back to unstructured commentary + Search handoff for any titles mentioned

### 5.2 AI Scoop

**Input:** Show metadata (title, year, genres, overview, vote_average, vote_count)

**Output structure** (streamed progressively):
- Personal take / opening stance
- Honest stack-up (vs critical/audience reception)
- "The Scoop" centerpiece paragraph (emotional highlight)
- Practical fit / who it's for / warnings
- "Worth it?" gut check

**Length target:** ~150–350 words total

**Tone:** gossipy, vivid, spoiler-safe, like a trusted friend's magazine sidebar

**Caching / freshness:**
- On request: check `ai_scoop_update_date`
- If < 4 hours old and scoop text exists: return cached
- If > 4 hours or missing: regenerate, update `ai_scoop` + `ai_scoop_update_date`

**Persistence rule:**
- Only write `ai_scoop` to DB if the show is in the user's collection (`my_status IS NOT NULL`)
- For unsaved shows: return scoop in response but do not persist

**UX copy states:**
- No scoop generated yet: "Give me the scoop!"
- Cached scoop available: "Show the scoop"
- Scoop open/visible: title header "The Scoop"

### 5.3 Concepts (Single Show)

**Input:** Show metadata (title, year, genres, overview)

**Output:** Exactly 8 concepts
- Format: bullet list only
- Each concept: 1–3 words, evocative, no explanation, no plot/spoilers
- Ordered by "aha" strength (strongest first)
- Covers diverse axes: structure, tone, emotion, relationship dynamics, craft

**Quality rules:**
- Specificity over genericity ("hopeful absurdity" valid; "good characters" invalid)
- Diversity across axes (not 8 synonyms)

**Result:** Displayed as selectable chips. User selects 1+ to feed into Explore Shows.

### 5.4 Concepts (Multi-Show / Alchemy)

**Input:** 2+ show metadata objects

**Output:** Larger concept pool (more than 8 — exact count TBD, suggest 12–15)
- Must represent **shared commonality** across all input shows
- Same format and quality rules as single-show concepts
- UI caps selection at 8

### 5.5 Explore Similar (Concept Recommendations)

**Input:** Source show metadata + list of selected concepts

**Output:** 5 show recommendations
- Each recommendation: `{ title, externalId, mediaType, reason }`
- `reason`: 1–3 sentences citing specific selected concepts
- Must reference at least one selected concept per reason
- Bias toward recent shows (not dogmatic)

**Resolution:** For each rec, call catalog by `externalId`; accept if title matches case-insensitively. If not found: show as non-interactive or hand off to Search.

### 5.6 Alchemy (Multi-Show Concept Recommendations)

**Input:** 2+ input shows + list of selected concepts (max 8)

**Output:** 6 show recommendations (same format as Explore Similar)

**Chaining:** Results can become new input shows for another Alchemy round ("More Alchemy!").

**State rules:**
- Changing input shows clears fetched concepts and results
- Changing concept selection clears results

### 5.7 AI Recommendation Resolution (Shared)

```
1. AI returns: { title, externalId, mediaType }
2. Call catalog.resolveShowById(externalId, mediaType)
3. If found AND title matches case-insensitively:
   → Show becomes a real, selectable Show with AI reason as transient text
4. If not found:
   → Show title as non-interactive text
   → Optionally offer "Search for [title]" affordance
```

---

## Phase 6: UI Pages & Components

### 6.1 Application Layout

```
[Left Panel: Filter Navigation]   [Main Content Area]
- All Shows                        - Route: /           → Collection Home
- Tag filters (one per tag)        - Route: /show/[id]  → Show Detail
- "No tags" (when applicable)      - Route: /person/[id]→ Person Detail
- Genre filters                    - Route: /find       → Find/Discover Hub
- Decade filters                   - Route: /settings   → Settings
- Community score filters
[Media-type toggle: All/Movies/TV]
```

The filter panel and media-type toggle are persistent across all main views.

### 6.2 Collection Home (`/`)

**Display:** Shows matching current filter, grouped by status sections in order:
1. **Active** — prominent, larger tiles
2. **Excited** — (Later + Excited interest)
3. **Interested** — (Later + Interested interest)
4. **Other** — collapsed section containing Wait, Quit, Done, Later-without-interest

**Show tiles contain:**
- Poster image
- Title
- In-collection badge (when `my_status` exists)
- Rating badge (when `my_score` exists)

**Empty states:**
- No shows in collection: prompt card linking to Search or Ask
- Filter yields no results: "No results found" message

### 6.3 Find/Discover Hub (`/find`)

Mode switcher with three modes:

**Search Mode:**
- Text input, live queries to `/api/catalog/search`
- Poster grid results
- In-collection items marked with badge
- Select show → navigate to `/show/[id]`
- Can be auto-opened on launch (`autoSearch` setting)

**Ask Mode:**
- Chat UI: alternating user/assistant message bubbles
- "Mentioned Shows" horizontal scrollable strip (resolved from `showList`)
- Tapping mentioned show → `/show/[id]` (or Search handoff if unresolved)
- Welcome view: 6 random starter prompts with "Refresh" button
- Session state: messages in local React state (not persisted)
- After ~10 messages: auto-summarize older turns in background

**Alchemy Mode:**
- **Step 1:** Show selector (search + library + catalog). Requires ≥2 selections.
- **Step 2:** "Conceptualize Shows" button → concept chips appear (select up to 8)
  - Changing show selection clears concepts and results
- **Step 3:** "ALCHEMIZE!" button → 6 recommendation cards
  - Changing concept selection clears results
- **Step 4:** Optional "More Alchemy!" → selected results become new input shows for next round

### 6.4 Show Detail (`/show/[id]`)

Section order per spec:

1. **Header media carousel**
   - Backdrop/poster/logo images + inline trailer video when available
   - Graceful fallback: poster + logo layout when no backdrop/trailer

2. **Core facts + community score**
   - Year, runtime (movies) or seasons/episodes (TV)
   - Community score bar/display

3. **Toolbar controls (sticky/prominent, not in scroll body)**
   - Status chips: Active | Interested | Excited | Done | Wait | Quit
   - Interested = sets `Later + Interested`; Excited = sets `Later + Excited`
   - Reselecting active chip → removal confirmation dialog (with "stop asking" option)
   - My Rating slider (0–10 scale)
   - Rating unsaved show → auto-save as Done
   - My Tags display + tag picker
   - Adding tag to unsaved show → auto-save as Later + Interested

4. **Overview text + Scoop toggle**
   - Overview paragraph
   - Scoop toggle button with contextual copy:
     - "Give me the scoop!" (no scoop) → triggers generation (streams in progressively)
     - "Show the scoop" (cached scoop) → reveals cached text
     - "The Scoop" header when open
   - Scoop streams in with "Generating…" state (not blank wait)

5. **"Ask about this show" CTA** → navigates to Find/Ask seeded with this show

6. **Genres + languages**

7. **Traditional recommendations strand** — horizontal scroll of similar/recommended shows from catalog

8. **Explore Similar** (concepts → recs)
   - "Get Concepts" button → loads 8 concept chips
   - User selects 1+ concepts
   - "Explore Shows" button → loads 5 recommendation cards
   - Each rec card: poster, title, reason text, save/status controls

9. **Streaming availability** — provider logos grouped by availability type (flatrate/rent/buy)

10. **Cast & Crew** — horizontal strands, each person → `/person/[id]`

11. **Seasons** (TV only) — expandable list of seasons with episode counts

12. **Budget / Revenue** (movies, when data available) — formatted display

### 6.5 Person Detail (`/person/[id]`)

- **Image gallery** — horizontal scroll of person photos
- **Bio** — name, bio text
- **Analytics charts:**
  - Average rating of their projects (bar or line chart)
  - Top genres they work in (pie or bar)
  - Projects by year (histogram)
- **Filmography** — grouped by year, each item is a show tile → opens `/show/[id]`

### 6.6 Settings (`/settings`)

**App:**
- Font size selector (XS / S / M / L / XL / XXL) — persisted in localStorage
- "Search on launch" toggle — persisted in localStorage

**User:**
- Username field → saved to `cloud_settings`

**AI:**
- AI provider API key (text input; note: in benchmark mode, injected via env; storing/syncing user-entered keys is optional and must never be committed)
- AI model selector → saved to `cloud_settings`

**Integrations:**
- Catalog API key (text input; same note as AI key)

**Your Data:**
- "Export My Data" button → downloads ZIP containing JSON backup of all saved shows + My Data (ISO-8601 dates)

---

## Phase 7: Infrastructure & DevOps Requirements

### 7.1 `.env.example`

```bash
# Supabase (required)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...        # browser-safe anon key
SUPABASE_SERVICE_ROLE_KEY=eyJ...            # server-only, elevated access

# External catalog (e.g., TMDB)
CATALOG_API_KEY=                            # server-only

# AI provider
AI_API_KEY=                                 # server-only
AI_MODEL=claude-sonnet-4-6                  # or any supported model

# Benchmark isolation
NAMESPACE_ID=dev-local                      # stable per build/run
DEFAULT_USER_ID=dev-user-001                # dev-mode identity injection

NODE_ENV=development
```

### 7.2 NPM Scripts

```json
{
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "test": "jest --runInBand",
  "test:reset": "tsx scripts/reset-namespace.ts"
}
```

`reset-namespace.ts` uses the Supabase service role key to delete all rows scoped to `NAMESPACE_ID`.

### 7.3 Database Migration Strategy

Migrations in `/supabase/migrations/` as timestamped SQL files:
- `0001_create_shows.sql`
- `0002_create_cloud_settings.sql`
- `0003_create_indexes.sql`
- `0004_enable_rls.sql`

Apply via `supabase db push` (hosted) or `supabase migration up` (local if Docker available). Docker is optional and documented as such.

### 7.4 Cloud Agent Compatibility

- No Docker required to run (Supabase hosted is primary path)
- All secrets via env vars (no code edits)
- No privileged container access needed
- Namespace isolation prevents data collisions between concurrent runs

### 7.5 Migration Path to Real OAuth

When adding Supabase Auth (Google OAuth or similar):
1. Enable Supabase Auth in project settings
2. Replace `X-Dev-User-Id` header injection with `supabase.auth.getUser()` in middleware
3. Update RLS policies to use `auth.uid()` instead of dev header
4. **No schema changes needed** — `user_id` column is already present as an opaque string

---

## Phase 8: Testing Strategy

### 8.1 Unit Tests

- **Merge logic:** `selectFirstNonEmpty`, timestamp-based My Data merge
- **Filter logic:** collection filtering by each `FilterType`, media-type toggle
- **Auto-save triggers:** each trigger scenario produces correct default status/interest
- **Catalog → Show mapping:** field-by-field mapping rules, type inference
- **AI output parsing:** `showList` string parser (valid + malformed inputs)
- **Concept quality:** no generic concepts pass quality gate (optional heuristic test)

### 8.2 Integration Tests

- Collection CRUD via API routes (create, read, update, delete)
- Catalog search + detail fetch
- AI Scoop caching (fresh vs stale)
- Recommendation resolution (found vs not-found paths)
- Namespace isolation (verify cross-namespace reads return empty)

### 8.3 Test Data Management

- All test records use `isTest = true` flag
- `npm run test:reset` deletes all rows for current `NAMESPACE_ID`
- No global teardown needed

---

## Data Flow Summaries

### Saving a Show

```
User action → auto-save trigger check
  → if unsaved: apply default status/interest rules
  → upsert show to DB with (namespace_id, user_id, show_id)
  → set relevant *UpdateDate timestamps
  → return updated show to UI
  → UI reflects status badge on all tile instances
```

### AI Ask Turn

```
User sends message
  → server builds prompt: user library + conversation history + system persona
  → call AI provider (streaming)
  → parse structured output: { commentary, showList }
  → for each item in showList: resolve via catalog
  → return commentary (render as chat bubble) + resolved shows (render as strip)
  → if parse fails: retry once; if fails again: return raw text + Search handoffs
```

### Alchemy Flow

```
User selects ≥2 shows → tap Conceptualize
  → /api/ai/concepts with all show metadata
  → receive concept list → render as selectable chips (cap 8)

User selects concepts → tap ALCHEMIZE!
  → /api/ai/recommendations (type: alchemy, shows: [...], concepts: [...])
  → receive 6 recs with reasons
  → resolve each rec via catalog
  → render rec cards

User taps "More Alchemy!"
  → selected recs become new input shows → repeat from step 1
```

### Export Flow

```
GET /api/export
  → fetch all shows for (namespace_id, user_id)
  → serialize to JSON (ISO-8601 dates)
  → bundle into ZIP file
  → stream as file download (Content-Disposition: attachment)
```

---

## Cross-Cutting Concerns

### Display Rule: User's Version Takes Precedence

Everywhere a show tile appears (collection, search results, recommendations, AI outputs), the system must:
1. Check if `(namespace_id, user_id, show_id)` exists in DB
2. If yes: overlay `my_status`, `my_score`, `my_tags`, `ai_scoop` on the display data
3. This overlay happens at the data-fetch layer, not the UI layer

Implementation: catalog detail/search responses run through a "user data merge" step server-side before returning to client.

### Tile Indicators

Every show tile component accepts:
- `inCollection: boolean` → show in-collection badge
- `myScore?: number` → show rating badge

These are derived from the server-merged show object.

### Spoiler Safety

All AI prompts include an explicit spoiler-safety instruction. The AI persona is spoiler-safe by default. This is enforced in every system prompt across all surfaces (Ask, Scoop, Concepts, Recommendations).

### AI Voice Consistency

All AI surfaces use the same base persona (chatty TV/movie nerd friend). Surface-specific adaptations:
- **Scoop:** gossipy, vivid, magazine-sidebar feel; streams progressively
- **Ask:** conversational, picks favorites, adapts depth to user's question
- **Ask About This Show:** mirrors the emotional color of the specific show
- **Concepts:** aha-inducing, playful, 1–3 word bullets
- **Recommendations:** excited friend sharing gold; reasons cite concepts

**Search has no AI voice** — straightforward catalog search results only.

### Data Continuity

On any schema migration, existing `my*` fields must survive. Migration scripts only ADD columns or create new tables; never drop or rename `my*` fields without a data migration step that preserves values.

---

## Deliverable Summary

| Area | Key Files/Locations |
|------|---------------------|
| Schema migrations | `/supabase/migrations/*.sql` |
| Environment config | `.env.example`, `.gitignore` |
| API routes | `/app/api/**/*.ts` |
| Pages | `/app/(routes)/**/*.tsx` |
| AI service layer | `/lib/ai/*.ts` |
| Catalog service | `/lib/catalog/*.ts` |
| DB client | `/lib/supabase/client.ts`, `/lib/supabase/server.ts` |
| Business logic | `/lib/shows/*.ts` (merge, auto-save, filter) |
| Test reset script | `/scripts/reset-namespace.ts` |
| Tests | `/tests/**/*.test.ts` |
