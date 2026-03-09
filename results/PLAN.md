# Implementation Plan — ShowCompanion

## Overview

A personal TV + movie companion built with **Next.js** and **Supabase**. Users collect, organize, rate, and discover entertainment through a personal collection with AI-powered discovery features (Ask, Alchemy, Explore Similar, AI Scoop).

---

## Phase 1: Project Foundation & Infrastructure

### 1.1 Project Scaffolding

- Initialize Next.js (latest stable) with App Router, TypeScript, Tailwind CSS
- Configure ESLint, Prettier, `tsconfig.json`
- Create `.env.example` with all required variables:
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - `SUPABASE_SERVICE_ROLE_KEY` (server-only)
  - `CATALOG_API_KEY` (TMDB)
  - `AI_API_KEY` (Anthropic/OpenAI)
  - `AI_MODEL`
  - `NAMESPACE_ID`
  - `DEFAULT_USER_ID`
- Add `.env*` (except `.env.example`) to `.gitignore`
- Create npm scripts: `dev`, `test`, `test:reset`

### 1.2 Supabase Schema & Migrations

Create migration files for the following tables:

**`shows`** — Primary entity storing catalog data + user overlay

| Column | Type | Notes |
|--------|------|-------|
| `id` | `text` PK | Stable catalog ID |
| `namespace_id` | `text` NOT NULL | Run isolation |
| `user_id` | `text` NOT NULL | User scoping |
| `title` | `text` NOT NULL | Display title |
| `show_type` | `text` NOT NULL | "movie" \| "tv" \| "person" \| "unknown" |
| `external_ids` | `jsonb` | External catalog IDs |
| `overview` | `text` | Synopsis |
| `genres` | `text[]` | Genre names |
| `tagline` | `text` | |
| `homepage` | `text` | |
| `original_language` | `text` | |
| `spoken_languages` | `text[]` | ISO 639-1 codes |
| `languages` | `text[]` | |
| `poster_url` | `text` | Full poster URL |
| `backdrop_url` | `text` | Full backdrop URL |
| `logo_url` | `text` | Best-rated logo URL |
| `vote_average` | `double precision` | Community score |
| `vote_count` | `integer` | |
| `popularity` | `double precision` | |
| `last_air_date` | `timestamptz` | |
| `first_air_date` | `timestamptz` | |
| `release_date` | `timestamptz` | |
| `runtime` | `integer` | Movie runtime |
| `budget` | `integer` | |
| `revenue` | `integer` | |
| `series_status` | `text` | TV series status |
| `number_of_episodes` | `integer` | |
| `number_of_seasons` | `integer` | |
| `episode_run_time` | `integer[]` | |
| `provider_data` | `jsonb` | Provider availability by region |
| `my_tags` | `text[]` DEFAULT `'{}'` | User tags |
| `my_tags_update_date` | `timestamptz` | |
| `my_score` | `double precision` | User rating |
| `my_score_update_date` | `timestamptz` | |
| `my_status` | `text` | Active\|Later\|Wait\|Done\|Quit\|Next |
| `my_status_update_date` | `timestamptz` | |
| `my_interest` | `text` | Interested\|Excited |
| `my_interest_update_date` | `timestamptz` | |
| `ai_scoop` | `text` | AI-generated description |
| `ai_scoop_update_date` | `timestamptz` | |
| `details_update_date` | `timestamptz` | Last catalog refresh |
| `creation_date` | `timestamptz` DEFAULT `now()` | |
| `is_test` | `boolean` DEFAULT `false` | |

- Composite unique constraint: `(id, namespace_id, user_id)`
- Indexes on `(namespace_id, user_id)`, `(namespace_id, user_id, my_status)`, `(namespace_id, user_id, my_tags)`

**`cloud_settings`** — Synced app settings

| Column | Type | Notes |
|--------|------|-------|
| `id` | `text` PK DEFAULT `'globalSettings'` | |
| `namespace_id` | `text` NOT NULL | |
| `user_id` | `text` NOT NULL | |
| `user_name` | `text` | Random on first launch |
| `version` | `double precision` | Epoch seconds for conflict resolution |
| `catalog_api_key` | `text` | |
| `ai_api_key` | `text` | |
| `ai_model` | `text` | |

- Composite unique constraint: `(id, namespace_id, user_id)`

**`app_metadata`** — Data model versioning

| Column | Type | Notes |
|--------|------|-------|
| `id` | `serial` PK | |
| `namespace_id` | `text` NOT NULL | |
| `data_model_version` | `integer` DEFAULT `3` | |

**Row-Level Security (RLS):**
- Enable RLS on all tables
- Policies filter by `namespace_id` header/claim and `user_id`
- Anon key access limited to current namespace + user scope

### 1.3 Identity & Namespace Isolation

- Create a Supabase client wrapper that injects `namespace_id` and `user_id` into all queries
- Dev identity injection: read `X-User-Id` header or fall back to `DEFAULT_USER_ID` env var
- All data operations filter by `(namespace_id, user_id)` — two namespaces never collide
- Document the identity model in README for future OAuth migration

### 1.4 Test Infrastructure

- Set up Jest + React Testing Library
- `npm run test:reset` script: deletes all rows where `namespace_id` matches test namespace or `is_test = true`
- Seed script for test fixtures (sample shows with various statuses)

**Files created:**
- `supabase/migrations/001_initial_schema.sql`
- `src/lib/supabase/client.ts` (browser client with namespace/user injection)
- `src/lib/supabase/server.ts` (server client with service role key)
- `src/lib/supabase/types.ts` (generated types)
- `scripts/reset-test-data.ts`
- `.env.example`, `.gitignore`

---

## Phase 2: Data Layer & Catalog Integration

### 2.1 Show Data Types & Validation

- Define TypeScript types mirroring the storage schema:
  - `Show`, `ShowType`, `MyStatusType`, `MyInterestType`, `ProviderData`, `CloudSettings`
- Create Zod schemas for validation at system boundaries
- Enum types: `ShowType = "movie" | "tv" | "person" | "unknown"`, `MyStatusType = "Active" | "Later" | "Wait" | "Done" | "Quit" | "Next"`, `MyInterestType = "Interested" | "Excited"`

**Files:**
- `src/types/show.ts`
- `src/types/settings.ts`
- `src/lib/validation/schemas.ts`

### 2.2 TMDB Catalog Integration

- Create TMDB API client (server-side only, API key never exposed to browser)
- Endpoints to wrap:
  - Search multi (movies + TV)
  - Movie details (with credits, videos, recommendations, similar, watch providers, images)
  - TV details (with credits, videos, recommendations, similar, watch providers, images, seasons)
  - Person details (with combined credits)
  - Trending / popular (for empty-state suggestions)
- Map TMDB responses to `Show` type using field mapping rules from storage-schema spec
- Genre ID → name mapping via TMDB genre lists (fetch once, cache)

**Files:**
- `src/lib/tmdb/client.ts`
- `src/lib/tmdb/mappers.ts` (TMDB response → Show)
- `src/lib/tmdb/types.ts` (TMDB-specific response types)
- `src/app/api/catalog/search/route.ts`
- `src/app/api/catalog/[mediaType]/[id]/route.ts`
- `src/app/api/catalog/person/[id]/route.ts`

### 2.3 Show Repository (CRUD)

- Implement data access layer for `shows` table
- Core operations:
  - `getCollection(namespaceId, userId, filters?)` — Get all user's saved shows
  - `getShow(namespaceId, userId, showId)` — Get single show
  - `upsertShow(namespaceId, userId, show)` — Save/update with merge logic
  - `removeShow(namespaceId, userId, showId)` — Clear all My Data fields (status, interest, tags, rating, scoop)
  - `updateMyData(namespaceId, userId, showId, fields)` — Partial update of user overlay fields
- Merge logic per spec:
  - Non-my fields: `selectFirstNonEmpty(new, old)` — never overwrite with empty
  - My fields: resolve by timestamp — keep whichever side has the more recent update date
  - `creation_date` set only on first insert
  - `details_update_date` set to now on every merge

**Files:**
- `src/lib/data/show-repository.ts`
- `src/lib/data/merge.ts` (merge/overwrite logic)
- `src/lib/data/settings-repository.ts`

### 2.4 API Routes for Collection

- `GET /api/collection` — List user's collection with optional filters (status, tags, genre, decade, score range, media type)
- `POST /api/collection/[showId]` — Upsert show into collection
- `PATCH /api/collection/[showId]` — Update My Data fields
- `DELETE /api/collection/[showId]` — Remove from collection (clear My Data)
- `GET /api/collection/tags` — List all user's unique tags

All routes inject `namespace_id` and `user_id` from headers/env.

**Files:**
- `src/app/api/collection/route.ts`
- `src/app/api/collection/[showId]/route.ts`
- `src/app/api/collection/tags/route.ts`

---

## Phase 3: Core UI Shell & Navigation

### 3.1 App Layout

- Top-level layout with sidebar/filters panel + main content area
- Sidebar contains:
  - "All Shows" (default filter)
  - Tag-based filters (populated from user's tag library)
  - Genre filters
  - Decade filters
  - Community score range filters
  - Media-type toggle (All / Movies / TV)
- Main content area renders active page
- Responsive: sidebar collapses on mobile

**Files:**
- `src/app/layout.tsx` (root layout)
- `src/components/layout/Sidebar.tsx`
- `src/components/layout/FilterPanel.tsx`
- `src/components/layout/MainContent.tsx`

### 3.2 Navigation Structure

Routes:
- `/` — Home (collection view)
- `/show/[mediaType]/[id]` — Show detail
- `/person/[id]` — Person detail
- `/find` — Find/Discover hub
- `/find/search` — Search
- `/find/ask` — Ask (AI chat)
- `/find/alchemy` — Alchemy
- `/settings` — Settings & Your Data

### 3.3 Shared Components

- `ShowTile` — Poster card with:
  - Poster image (fallback to placeholder)
  - In-collection indicator (when `my_status` exists)
  - User rating indicator (when `my_score` exists)
  - Click → Show detail
- `ShowGrid` — Responsive grid of ShowTiles
- `ShowStrip` — Horizontal scrollable row of ShowTiles (for recommendations, mentions)
- `StatusChips` — Selectable status pills (Active/Later/Wait/Done/Quit)
- `InterestChips` — Interested/Excited (shown when status is Later)
- `RatingBar` — Interactive star/score input
- `TagInput` — Tag chip editor with autocomplete from tag library
- `ScoreBar` — Community score display (read-only)
- `MediaTypeToggle` — All/Movies/TV segmented control
- `EmptyState` — Contextual empty state with CTAs

**Files:**
- `src/components/show/ShowTile.tsx`
- `src/components/show/ShowGrid.tsx`
- `src/components/show/ShowStrip.tsx`
- `src/components/show/StatusChips.tsx`
- `src/components/show/InterestChips.tsx`
- `src/components/show/RatingBar.tsx`
- `src/components/show/TagInput.tsx`
- `src/components/ui/ScoreBar.tsx`
- `src/components/ui/MediaTypeToggle.tsx`
- `src/components/ui/EmptyState.tsx`

---

## Phase 4: Collection Home

### 4.1 Home Page

- Fetch user's collection grouped by status
- Display groups in priority order:
  1. **Active** — prominent, top of page
  2. **Excited** — Later shows with interest=Excited
  3. **Interested** — Later shows with interest=Interested
  4. **Other** — collapsed section containing Wait, Quit, Done
- Each group renders as a `ShowGrid` or `ShowStrip`
- Media-type toggle filters across all groups
- Sidebar tag/genre/decade/score filters apply across all groups
- Empty state when no shows: prompt to Search or Ask

### 4.2 Filter Logic

- Client-side filtering from fetched collection for speed
- Filter types:
  - `all` — no filter
  - `myTag` — shows containing specific tag
  - `genre` — shows containing specific genre
  - `decade` — shows released in specific decade
  - `communityScore` — score range bracket
  - `myStatus` — specific status filter
- Persist `lastSelectedFilter` in localStorage
- Media-type toggle is independent of sidebar filter

**Files:**
- `src/app/page.tsx` (Home)
- `src/lib/filters/filter-logic.ts`
- `src/hooks/useCollection.ts` (SWR/React Query hook)
- `src/hooks/useFilters.ts`

---

## Phase 5: Search & Catalog Browse

### 5.1 Search Page

- Text input for title/keyword search
- Debounced search hitting `/api/catalog/search`
- Results displayed in `ShowGrid`
- Each tile shows in-collection indicator if show exists in user's collection
- Clicking tile navigates to Show Detail
- Optional: auto-open on launch if `autoSearch` setting enabled

### 5.2 Search API

- Server-side route calls TMDB search/multi
- Maps results to `Show[]`
- Cross-references with user's collection to set tile indicators
- Returns enriched results

**Files:**
- `src/app/find/search/page.tsx`
- `src/components/search/SearchInput.tsx`
- `src/components/search/SearchResults.tsx`

---

## Phase 6: Show Detail Page

### 6.1 Detail Page Layout

Sections in narrative order per detail page spec:

1. **Header Media Carousel** — Backdrops, posters, logos, trailer thumbnails. Prioritize trailers when present. Graceful fallback to poster/backdrop.
2. **Core Facts Row** — Year, runtime (movie) or seasons/episodes (TV), community score bar
3. **My Tags** — Tag chips, editable inline
4. **Overview + Scoop** — Overview text, then "Give me the scoop!" toggle
5. **Ask About This Show CTA** — Button linking to Ask with show context seeded
6. **Genres + Languages** — Genre chips, language list
7. **Recommendations Strand** — Traditional TMDB recommendations in `ShowStrip`
8. **Explore Similar** — Concept-based discovery (Get Concepts → select → Explore Shows)
9. **Streaming Availability** — Provider logos by region from `provider_data`
10. **Cast & Crew** — Scrollable cast strip → Person Detail links
11. **Seasons** — TV only, season list with episode counts
12. **Budget vs Revenue** — Movies only, formatted display

### 6.2 My Relationship Controls (Toolbar)

Sticky toolbar (not in scroll body) containing:
- **Status chips**: Active / Later / Wait / Done / Quit
  - Selecting a status on unsaved show → auto-save with that status
  - Reselecting active status → confirmation dialog → removes from collection (clears all My Data)
- **Interest chips**: Interested / Excited (visible only when status = Later)
  - Selecting interest on unsaved show → saves as Later + chosen interest
- **My Rating bar**: Interactive rating
  - Rating unsaved show → auto-save as Done
- **Tags**: Add/remove tags
  - Adding tag to unsaved show → auto-save as Later + Interested

### 6.3 AI Scoop Section

- Toggle states: "Give me the scoop!" → "Generating…" (streaming) → "The Scoop" (expanded)
- On toggle: call AI endpoint to generate scoop
- Stream response progressively into UI
- Freshness: regenerate if `ai_scoop_update_date` > 4 hours old
- Persistence: only save to DB if show is in collection; otherwise ephemeral
- Subsequent visits: show "Show the scoop" toggle if cached, "Give me the scoop!" if stale/missing

### 6.4 Explore Similar Section

- "Get Concepts" button → calls AI concepts endpoint for this show
- Display concepts as selectable chips
- User selects 1+ concepts (UI cap: 8)
- "Explore Shows" button → calls AI recommendations endpoint with selected concepts
- Display 5 recommendations with per-rec reasons
- Selecting/deselecting concepts clears previous results

### 6.5 Data Fetching

- Fetch show details from TMDB (credits, videos, recommendations, similar, providers, images, seasons)
- Fetch user's overlay from collection API
- Merge for display

**Files:**
- `src/app/show/[mediaType]/[id]/page.tsx`
- `src/components/detail/HeaderCarousel.tsx`
- `src/components/detail/CoreFacts.tsx`
- `src/components/detail/MyRelationshipToolbar.tsx`
- `src/components/detail/OverviewScoop.tsx`
- `src/components/detail/ExploreSimlar.tsx`
- `src/components/detail/StreamingProviders.tsx`
- `src/components/detail/CastCrew.tsx`
- `src/components/detail/Seasons.tsx`
- `src/components/detail/BudgetRevenue.tsx`
- `src/hooks/useShowDetail.ts`

---

## Phase 7: Person Detail Page

### 7.1 Person Page Layout

- **Image gallery**: Profile images
- **Bio**: Biography text
- **Analytics charts**: Ratings distribution, genre breakdown, projects-by-year timeline
- **Filmography**: Grouped by year, showing poster tiles. Click → Show Detail
- **Credit selection**: Filter by department (acting, directing, etc.)

### 7.2 Data Fetching

- Fetch person details + combined credits from TMDB
- Compute analytics from credits data client-side

**Files:**
- `src/app/person/[id]/page.tsx`
- `src/components/person/PersonHeader.tsx`
- `src/components/person/PersonAnalytics.tsx`
- `src/components/person/Filmography.tsx`

---

## Phase 8: AI Infrastructure

### 8.1 AI Client & Prompt Engine

- Server-side AI client that supports configurable provider/model (from settings or env)
- Prompt template system loading persona + surface-specific instructions
- Shared context builder: assembles user library summary, current show context, conversation history
- Streaming response support via Server-Sent Events or ReadableStream

### 8.2 AI Persona Prompt

- Single base persona prompt used across all surfaces (fun, chatty TV/movie nerd friend)
- Surface-specific modifiers layered on top (Scoop structure, Ask conversational style, Concepts bullet format, Recommendations format)
- Non-negotiable rules baked in: spoiler-safe, domain-scoped, opinionated honesty, real shows only

### 8.3 Structured Output Parsing

- Parse AI responses for structured data (show mentions, concept lists, recommendations)
- Show mention format: `Title::externalId::mediaType`
- Retry with stricter instructions on parse failure
- Fallback to unstructured + Search handoff if parsing fails repeatedly

**Files:**
- `src/lib/ai/client.ts` (AI provider client, streaming support)
- `src/lib/ai/prompts/base-persona.ts`
- `src/lib/ai/prompts/scoop.ts`
- `src/lib/ai/prompts/ask.ts`
- `src/lib/ai/prompts/concepts.ts`
- `src/lib/ai/prompts/recommendations.ts`
- `src/lib/ai/context-builder.ts` (assembles user library + show context)
- `src/lib/ai/parsers.ts` (structured output parsing)

---

## Phase 9: AI Discovery Features

### 9.1 Ask (AI Chat)

- Chat UI with user/assistant message turns
- Welcome view: 6 random starter prompts from curated set
- User sends message → server streams AI response
- Mentioned shows extracted from structured output → displayed in horizontal `ShowStrip` below assistant message
- Mentioned show resolution: lookup by external ID in TMDB, accept if case-insensitive title matches. If not found, show non-interactive or handoff to search.
- Context retention: include recent turns in prompt. After ~10 messages, summarize older turns into 1-2 sentences (preserving persona tone).
- Variant: "Ask about this show" — seeds conversation with show context from Detail page

**API Route:** `POST /api/ai/ask` (streaming)

**Files:**
- `src/app/find/ask/page.tsx`
- `src/components/ask/ChatUI.tsx`
- `src/components/ask/MessageBubble.tsx`
- `src/components/ask/WelcomeView.tsx`
- `src/components/ask/MentionedShows.tsx`
- `src/app/api/ai/ask/route.ts`
- `src/lib/ai/conversation-manager.ts` (history + summarization)

### 9.2 Alchemy

Flow:
1. **Select starting shows** — User picks 2+ shows from collection or search
2. **Conceptualize Shows** — Calls concept generation API with selected shows
3. **Select concept catalysts** — Concepts displayed as selectable chips (max 8)
4. **ALCHEMIZE!** — Calls recommendation API with selected concepts + input shows
5. **Review recommendations** — 6 recs displayed with per-rec reasons tied to concepts
6. **Chain** — Optionally add recs to input set and repeat from step 2

- Selecting/deselecting concepts clears downstream results
- Session-only: results and reasons not persisted

**API Routes:**
- `POST /api/ai/concepts` (concept generation)
- `POST /api/ai/recommend` (concept-based recommendations)

**Files:**
- `src/app/find/alchemy/page.tsx`
- `src/components/alchemy/ShowPicker.tsx`
- `src/components/alchemy/ConceptChips.tsx`
- `src/components/alchemy/AlchemyResults.tsx`
- `src/app/api/ai/concepts/route.ts`
- `src/app/api/ai/recommend/route.ts`

### 9.3 Explore Similar (Detail Page)

- Reuses the same concept + recommendation APIs as Alchemy
- Single show input instead of multi-show
- Returns 5 recs per round (vs 6 for Alchemy)
- UI embedded in Show Detail page (Phase 6.4)

### 9.4 AI Scoop Generation

- Dedicated endpoint for scoop generation
- Takes show context + user library for taste-awareness
- Returns structured scoop: personal take, honest stack-up, Scoop centerpiece, fit/warnings, verdict
- Streams progressively
- ~150-350 words

**API Route:** `POST /api/ai/scoop` (streaming)

**Files:**
- `src/app/api/ai/scoop/route.ts`

### 9.5 AI Recommendation → Real Show Resolution

- AI outputs `title + externalId + mediaType`
- System calls TMDB lookup by external ID
- Accepts match if case-insensitive title comparison passes
- If found: show becomes interactive `ShowTile` with AI "reason" attached
- If not found: displayed as non-interactive text, or handed off to Search

**Files:**
- `src/lib/ai/show-resolver.ts`

---

## Phase 10: Settings & Data Management

### 10.1 Settings Page

Sections:
- **App Settings**: Font size selector (XS/S/M/L/XL/XXL), Search on launch toggle
- **User**: Username input (synced via `cloud_settings`)
- **AI**: Provider API key input, model selection dropdown (synced)
- **Integrations**: TMDB API key input (synced)
- **Your Data**: Export/Backup button

### 10.2 Export/Backup

- Export generates `.zip` file containing:
  - `collection.json` — All shows with My Data, ISO-8601 dates
  - `settings.json` — Cloud settings
- Downloaded to user's device
- Import/Restore: not implemented in this version (noted as future extension)

### 10.3 Local Settings Persistence

- `autoSearch`, `fontSize`, `hideStatusRemovalConfirmation`, `statusRemovalCountKey`, `lastSelectedFilter` stored in localStorage
- Read on app init, applied globally

**Files:**
- `src/app/settings/page.tsx`
- `src/components/settings/AppSettings.tsx`
- `src/components/settings/AISettings.tsx`
- `src/components/settings/IntegrationSettings.tsx`
- `src/components/settings/DataExport.tsx`
- `src/lib/settings/local-settings.ts`
- `src/app/api/settings/route.ts`
- `src/app/api/export/route.ts`

---

## Phase 11: Business Logic & Auto-Save Rules

### 11.1 Auto-Save Triggers

Implement centralized save logic that handles implicit saves:

| Trigger | Result |
|---------|--------|
| Set status on unsaved show | Save with chosen status |
| Set Interested/Excited on unsaved show | Save as Later + chosen interest |
| Rate unsaved show | Save as Done + rating |
| Add tag to unsaved show | Save as Later + Interested + tag |

- All auto-saves fetch full show details from TMDB first, then upsert with merge logic
- Each user field update sets its corresponding timestamp to `now()`

### 11.2 Remove from Collection

- Triggered by reselecting active status → confirmation dialog
- Confirmation tracks `statusRemovalCountKey`; after threshold, option to suppress future confirmations
- On confirm: clear `my_status`, `my_interest`, `my_tags`, `my_score`, `ai_scoop` (and their timestamps)
- Show remains in DB with empty My Data (or deleted entirely — implementation choice)

### 11.3 Re-Add After Removal

- If show row still exists: preserve latest user data, merge conflicts by most recent timestamp per field
- If show row deleted: create fresh

### 11.4 Interest ↔ Status Coupling

- Interest (Interested/Excited) only valid when status = Later
- Changing status away from Later clears interest
- Setting interest when no status → set status to Later

**Files:**
- `src/lib/data/auto-save.ts`
- `src/lib/data/collection-actions.ts`

---

## Phase 12: Find/Discover Hub

### 12.1 Find Page

- Hub page with three modes accessible via tabs/segments:
  - **Search** — Text search (Phase 5)
  - **Ask** — AI chat (Phase 9.1)
  - **Alchemy** — Concept blending (Phase 9.2)
- Default to Search tab
- Each mode maintains its own state within the session

**Files:**
- `src/app/find/layout.tsx`
- `src/components/find/FindTabs.tsx`

---

## Phase 13: Polish & Cross-Cutting Concerns

### 13.1 Responsive Design

- Mobile-first layout
- Sidebar collapses to drawer on mobile
- Toolbar/status chips adapt to smaller screens
- Image loading with blur placeholders and lazy loading

### 13.2 Loading & Error States

- Skeleton loaders for collection, detail page, search results
- Error boundaries with retry actions
- Optimistic updates for status/rating/tag changes
- Toast notifications for save confirmations

### 13.3 Font Size System

- CSS custom properties driven by `fontSize` setting
- Applied globally via root layout

### 13.4 Accessibility

- Semantic HTML, ARIA labels on interactive elements
- Keyboard navigation for status chips, rating bar, concept chips
- Focus management on page transitions

---

## Phase 14: Testing

### 14.1 Unit Tests

- Merge logic (`selectFirstNonEmpty`, timestamp-based my-field resolution)
- Filter logic (all filter types)
- TMDB response mapping
- AI structured output parsing
- Auto-save trigger logic

### 14.2 Integration Tests

- Collection CRUD operations via API routes
- Search → save flow
- AI endpoint responses (mocked AI, real parsing)
- Export generation

### 14.3 E2E Smoke Tests

- Build collection journey: search → save → verify on home
- Rate-to-save: search → rate → verify as Done
- Detail page: navigate → view all sections → set status → verify
- Settings: update font size → verify applied

---

## Build Order & Dependencies

```
Phase 1  (Foundation)
  ↓
Phase 2  (Data Layer + TMDB)
  ↓
Phase 3  (UI Shell + Components)
  ↓
Phase 4  (Home) ←── Phase 11 (Business Logic, concurrent)
  ↓
Phase 5  (Search)
  ↓
Phase 6  (Show Detail)
  ↓
Phase 7  (Person Detail)
  ↓
Phase 8  (AI Infrastructure)
  ↓
Phase 9  (AI Features: Ask, Alchemy, Scoop, Explore Similar)
  ↓
Phase 10 (Settings & Export)
  ↓
Phase 12 (Find Hub, wires together Search + Ask + Alchemy)
  ↓
Phase 13 (Polish)
  ↓
Phase 14 (Testing)
```

Phases 4 and 11 can be developed concurrently. Phase 8 is prerequisite for all of Phase 9. Phase 12 is largely wiring that connects existing pages.

---

## Key Technical Decisions

1. **Next.js App Router** — Server components for data fetching, client components for interactivity. API routes for all server-side operations.
2. **Supabase JS Client** — Direct client calls from server components/API routes. Namespace + user filtering applied at the data access layer.
3. **Streaming AI responses** — Use `ReadableStream` in API routes to stream AI responses to the client for progressive rendering (Scoop, Ask).
4. **SWR or React Query** — Client-side data fetching with caching, revalidation, and optimistic updates for collection operations.
5. **Tailwind CSS** — Utility-first styling. CSS custom properties for font size system.
6. **No external state management library** — React context + hooks sufficient for app state (filters, settings, active show).
7. **TMDB as catalog provider** — Fits all field mapping requirements. API key managed via env/settings.
8. **AI provider agnostic** — Client supports configurable provider/model. Default to Anthropic Claude but architecture supports switching via settings.
