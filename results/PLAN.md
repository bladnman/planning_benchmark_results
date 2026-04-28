# Implementation Plan

## 1. Scope And Ground Rules

This plan covers a full implementation of the TV and movie companion app described in `docs/prd/`, using the benchmark infrastructure baseline:

- Next.js latest stable for the application runtime, UI, and server boundary.
- Supabase for persistent server-side storage through official client libraries.
- Environment-variable configuration only, with `.env.example` documenting all required values.
- Server-side persisted user data as the source of truth.
- Required isolation by `(namespace_id, user_id)` for all user-owned records.
- No reliance on Docker for the primary development, test, or benchmark path.

The implementation should prioritize behavioral parity over copying any legacy mechanics. The app must make the user's version of every show visible everywhere, support durable collection management, and power discovery through catalog search, Ask, Alchemy, and Explore Similar.

## 2. Product Model Summary

The app has three overlapping domains:

1. Personal collection management:
   - A saved show is any movie or TV item with `myStatus`.
   - User-owned fields are status, interest, tags, rating, and AI Scoop.
   - User fields always override public catalog refreshes.
   - Clearing status removes the show from the collection and clears all My Data.

2. Catalog and detail exploration:
   - Global search returns external catalog results.
   - Detail pages combine public metadata, user overlay, traditional recommendations, providers, cast, crew, seasons, and movie financials.
   - Person pages expose bio, images, credits, and lightweight analytics.

3. AI-assisted discovery:
   - Ask is conversational, session-based, taste-aware, and spoiler-safe.
   - AI mentions resolve into real selectable shows where possible.
   - AI Scoop is generated on demand, cached for 4 hours, and persisted only when the show is saved.
   - Explore Similar and Alchemy use concepts as user-selectable taste ingredients.

## 3. Architecture Overview

Use a Next.js App Router architecture with a clear server/client split:

```text
src/
  app/
    page.tsx
    find/
    show/[mediaType]/[id]/
    person/[id]/
    settings/
    api/
  config/
  theme/
  components/
  features/
  lib/
    ai/
    catalog/
    identity/
    persistence/
    supabase/
  server/
    actions/
    services/
    repositories/
  test/
```

Apply the benchmark coding standards:

- Pages compose feature modules.
- TSX components stay humble: markup, binding, and layout only.
- Hooks own local UI state and event handlers.
- Shared business rules live in pure utilities and service modules.
- Avoid `index.tsx`; the main file name should match the directory name.
- Constants live in `config/` or local `constants.ts`.
- Styling uses theme tokens, not inline hex values or magic numbers in TSX.

Recommended feature boundaries:

- `CollectionHome`
- `FilterNavigation`
- `MediaTypeToggle`
- `ShowTile`
- `ShowDetail`
- `RelationshipToolbar`
- `RatingControl`
- `TagsEditor`
- `ScoopPanel`
- `ExploreSimilar`
- `FindHub`
- `CatalogSearch`
- `AskChat`
- `MentionedShowsStrip`
- `AlchemyWorkbench`
- `PersonDetail`
- `SettingsAndData`

## 4. Environment And Configuration

Create configuration that can run in local and cloud-agent environments.

Required files:

- `.env.example`
- `.gitignore`
- Supabase migration files
- Package scripts for dev, test, migration, and namespace reset

Required environment variables:

```text
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
NEXT_PUBLIC_NAMESPACE_ID=
DEFAULT_USER_ID=
CATALOG_API_KEY=
CATALOG_API_BASE_URL=
AI_PROVIDER=
AI_API_KEY=
AI_MODEL=
NODE_ENV=
```

Rules:

- `SUPABASE_SERVICE_ROLE_KEY` is server-only.
- Browser code uses only anon/public credentials.
- Missing required env vars should fail fast with clear errors.
- `namespace_id` is not a user-facing concept.
- `user_id` is an opaque stable string.
- Development identity injection is allowed but must be gated away from production.

## 5. Identity And Request Context

Implement a central request context resolver:

```text
resolveRequestContext(request) -> {
  namespaceId,
  userId,
  isDevelopmentIdentity,
}
```

Behavior:

- In development/test, accept `X-User-Id` or a configured default user.
- In production, reject dev identity injection.
- All server actions and route handlers that touch user data must require this context.
- All repository methods must accept namespace and user explicitly rather than reading globals.

This keeps future OAuth adoption straightforward: replace the identity resolver, not the database schema.

## 6. Supabase Data Model

Use Supabase tables that preserve the storage semantics in the technical schema while adding required benchmark partitioning.

### 6.1 Core Tables

#### `shows`

Stores merged public catalog data plus selected non-user persistent fields.

Key columns:

- `namespace_id text not null`
- `show_id text not null`
- `title text not null`
- `show_type text not null`
- `external_ids jsonb`
- `overview text`
- `genres text[] not null default '{}'`
- `tagline text`
- `homepage text`
- `original_language text`
- `spoken_languages text[] not null default '{}'`
- `languages text[] not null default '{}'`
- `poster_url text`
- `backdrop_url text`
- `logo_url text`
- `network_logos text[] not null default '{}'`
- `vote_average numeric`
- `vote_count integer`
- `popularity numeric`
- `first_air_date timestamptz`
- `last_air_date timestamptz`
- `release_date timestamptz`
- `runtime integer`
- `budget bigint`
- `revenue bigint`
- `series_status text`
- `number_of_episodes integer`
- `number_of_seasons integer`
- `episode_run_time integer[] not null default '{}'`
- `last_episode_run_time integer`
- `provider_data jsonb`
- `details_update_date timestamptz`
- `creation_date timestamptz not null default now()`
- `is_test boolean not null default false`

Primary key:

- `(namespace_id, show_id)`

#### `user_show_overlays`

Stores all My Data. This is the collection membership table.

Key columns:

- `namespace_id text not null`
- `user_id text not null`
- `show_id text not null`
- `my_status text`
- `my_status_update_date timestamptz`
- `my_interest text`
- `my_interest_update_date timestamptz`
- `my_tags text[] not null default '{}'`
- `my_tags_update_date timestamptz`
- `my_score numeric`
- `my_score_update_date timestamptz`
- `ai_scoop text`
- `ai_scoop_update_date timestamptz`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`
- `is_test boolean not null default false`

Primary key:

- `(namespace_id, user_id, show_id)`

Constraint:

- `my_status` may be null only transiently in service logic; persisted overlays should be deleted when status is cleared.

#### `user_tags`

Optional normalized tag library for filtering and suggestions.

Key columns:

- `namespace_id text not null`
- `user_id text not null`
- `tag text not null`
- `created_at timestamptz not null default now()`
- `last_used_at timestamptz`

Primary key:

- `(namespace_id, user_id, tag)`

#### `user_settings`

Stores synced settings.

Key columns:

- `namespace_id text not null`
- `user_id text not null`
- `user_name text not null`
- `settings_version numeric not null`
- `catalog_api_key text`
- `ai_api_key text`
- `ai_model text`
- `auto_search boolean not null default false`
- `font_size text not null default 'M'`
- `hide_status_removal_confirmation boolean not null default false`
- `status_removal_count integer not null default 0`
- `last_selected_filter jsonb`
- `updated_at timestamptz not null default now()`

Primary key:

- `(namespace_id, user_id)`

#### `app_metadata`

Tracks migration state.

Key columns:

- `namespace_id text not null`
- `data_model_version integer not null default 3`
- `updated_at timestamptz not null default now()`

Primary key:

- `(namespace_id)`

### 6.2 Transient Data

Do not persist:

- Ask chat history
- Mentioned shows strip
- Alchemy concepts/results/reasons
- Explore Similar recommendations
- Cast, crew, seasons, videos, images, similar, and recommendation payloads fetched for detail UI

These may be cached client-side or in short-lived server memory, but correctness must not depend on them.

### 6.3 Indexes

Add indexes for:

- `user_show_overlays(namespace_id, user_id, my_status)`
- `user_show_overlays(namespace_id, user_id, my_interest)`
- `user_show_overlays(namespace_id, user_id, updated_at desc)`
- `user_tags(namespace_id, user_id, tag)`
- `shows(namespace_id, show_type)`
- `shows(namespace_id, title)`
- `shows(namespace_id, genres)`
- `shows(namespace_id, release_date)`
- `shows(namespace_id, first_air_date)`
- `shows(namespace_id, vote_average)`

## 7. Data Merge And Overlay Rules

Centralize merge behavior in pure functions with tests.

### 7.1 Catalog Merge

When catalog data is refreshed:

- Decode external payload into a normalized `CatalogShow`.
- Reject records without title or inferable media type.
- Merge into stored public show using `selectFirstNonEmpty(newValue, oldValue)`.
- Never overwrite a non-empty stored field with empty, null, or undefined.
- Set `details_update_date` to now.
- Preserve `creation_date`.
- Persist provider IDs by country only, not full provider objects.

### 7.2 User Overlay Merge

For each user field:

- If both versions have update timestamps, keep the newer field value.
- If only one version has a timestamp, keep that side.
- If neither has a timestamp, use deterministic service precedence and add a timestamp when saving.

Fields:

- `myStatus` and `myStatusUpdateDate`
- `myInterest` and `myInterestUpdateDate`
- `myTags` and `myTagsUpdateDate`
- `myScore` and `myScoreUpdateDate`
- `aiScoop` and `aiScoopUpdateDate`

### 7.3 Display Overlay

Build a single projection function:

```text
composeDisplayShow(publicShow, userOverlay?) -> DisplayShow
```

Every list, search result, recommendation, AI mention, detail page, and exported item must use this projection so saved shows display the user's status, tags, rating, interest, and scoop.

## 8. Catalog Integration

Implement a provider abstraction:

```text
CatalogProvider
  search(query, mediaType?)
  getDetails(mediaType, id)
  getRecommendations(mediaType, id)
  getSimilar(mediaType, id)
  getCredits(mediaType, id)
  getPerson(id)
  getPersonCredits(id)
  getImages(mediaType, id)
  getVideos(mediaType, id)
  getProviders(mediaType, id, region)
  resolveRecommendation({ title, externalId, mediaType })
```

The concrete provider can use the configured external catalog service. Keep provider-specific field mapping out of UI components.

Resolution rules for AI recommendations:

- Prefer external ID when present.
- Confirm title match case-insensitively.
- Confirm media type when available.
- If resolution succeeds, return a real selectable show.
- If resolution fails, show non-interactive text or provide Search handoff.

## 9. Application Routes

Recommended top-level routes:

- `/` collection home
- `/find?mode=search`
- `/find?mode=ask`
- `/find?mode=alchemy`
- `/show/[mediaType]/[id]`
- `/person/[id]`
- `/settings`

Deep links should remain stable and identity-free. `namespace_id` and `user_id` stay in request context, not visible paths.

## 10. Collection Home Plan

Build the collection home around server-loaded library data and client-side view controls.

### 10.1 Data Loading

Server flow:

1. Resolve request context.
2. Query overlays for `(namespace_id, user_id)`.
3. Join with public show rows.
4. Compose display shows.
5. Return grouped/filterable library data.

### 10.2 Grouping

Groups:

- Active: `myStatus = active`
- Excited: `myStatus = later` and `myInterest = excited`
- Interested: `myStatus = later` and `myInterest = interested`
- Other: `wait`, `quit`, `done`, and `later` without interest

Active should be more visually prominent. Other can be collapsed by default.

### 10.3 Filters

Implement filter modes:

- All Shows
- Tags
- No tags
- Genre
- Decade
- Community score range
- Media type: All, Movies, TV

Filters apply on top of the collection projection. Tag filters come from user overlays, not catalog fields.

### 10.4 Empty States

Cases:

- No saved shows: prompt to Search or Ask.
- Filter has no results: show "No results found."
- Tagless filter unavailable unless at least one saved show has no tags.

## 11. Search Plan

Search lives inside the Find hub and has no AI voice.

Behavior:

- Text search by title/keyword.
- Optional media type narrowing.
- Results shown as poster grid.
- In-collection badge when `myStatus` exists.
- Rating badge when `myScore` exists.
- Selecting a show opens Detail.
- If user setting `autoSearch` is true, open Search on launch or Find entry.

Implementation:

- Debounced client input.
- Server route/action calls catalog provider.
- For each result, fetch matching overlay by external show ID and compose display result.
- Save or refresh public show data when opening Detail, not necessarily for every search result.

## 12. Show Detail Plan

The Show Detail page is the single source of truth for public facts, My Data, and discovery launch points.

### 12.1 Server Load

1. Resolve context.
2. Fetch catalog details.
3. Normalize and merge public show row.
4. Fetch user overlay.
5. Compose display show.
6. Fetch transient detail data:
   - images
   - videos
   - recommendations/similar
   - credits
   - providers
   - seasons for TV
7. Render page with interactive client features for My Data and AI actions.

### 12.2 Section Order

Preserve the documented hierarchy unless a later product decision changes it:

1. Header media carousel
2. Core facts and community score
3. My relationship controls
4. My Tags
5. Overview and Scoop
6. Ask about this show
7. Genres and languages
8. Recommendations strand
9. Explore Similar
10. Streaming availability
11. Cast and Crew
12. Seasons for TV
13. Budget and revenue for movies

### 12.3 Relationship Controls

Status/interest behavior:

- Active sets `myStatus = active`.
- Done sets `myStatus = done`.
- Quit sets `myStatus = quit`.
- Wait sets `myStatus = wait`.
- Interested sets `myStatus = later` and `myInterest = interested`.
- Excited sets `myStatus = later` and `myInterest = excited`.
- Reselecting the active status opens removal confirmation unless suppressed.
- Removal deletes overlay and clears My Data.

Rating behavior:

- Rating an unsaved show creates an overlay with `myStatus = done`.
- Clearing rating removes `myScore` but does not remove collection membership unless status is also cleared.

Tag behavior:

- Adding a tag to an unsaved show creates an overlay with `myStatus = later` and `myInterest = interested`.
- Removing the last tag does not remove the show if status remains.
- Tag updates refresh `myTagsUpdateDate`.

### 12.4 AI Scoop

Behavior:

- Button text:
  - No scoop: "Give me the scoop!"
  - Cached scoop: "Show the scoop"
  - Open state title: "The Scoop"
- Generate on demand.
- Spoiler-safe by default.
- Stream partial output if the provider supports it.
- Cache freshness is 4 hours.
- Persist only if show is saved.
- For unsaved shows, keep generated scoop ephemeral unless the user later saves before leaving detail.

Scoop content contract:

- Personal take.
- Honest stack-up.
- Main Scoop paragraph.
- Fit/warnings.
- Worth it verdict.
- About 150 to 350 words.

## 13. Person Detail Plan

Person pages should be reachable from cast/crew rows.

Load:

- Person profile
- Image gallery
- Biography
- Credits grouped by year
- Project metadata needed for analytics

Analytics:

- Average community rating across known projects.
- Top genres.
- Projects by year.

Interactions:

- Selecting a credit opens Show Detail.
- Saved show overlays must appear on credits where applicable.

## 14. Find Hub Plan

Build one Find page with clear mode switching:

- Search
- Ask
- Alchemy

Mode should be URL-addressable through query params or subroutes. The mode switcher should not reset unrelated mode state unless the user explicitly resets.

## 15. Ask Plan

Ask is session-based and not persisted.

### 15.1 UI

Features:

- Chat transcript.
- Input composer.
- Welcome view with 6 random starter prompts and refresh.
- Mentioned shows horizontal strip.
- Reset conversation action.
- Loading and retry states.

### 15.2 Session Context

Maintain:

- Recent turns.
- 1 to 2 sentence summaries of older turns after roughly 10 messages.
- Optional handoff show context when launched from Detail.
- User library summary including status, tags, interest, rating, and AI Scoop when useful.

Do not persist conversation history.

### 15.3 AI Output Contract

Use a structured response for mention-aware chat:

```text
{
  commentary: string,
  showList: "Title::externalId::mediaType;;Title2::externalId::mediaType"
}
```

Rules:

- User-facing commentary must not expose IDs.
- Parser must exactly match the structured format.
- Retry once with stricter formatting if parsing fails.
- Fallback to commentary plus Search handoff if structured parsing still fails.

### 15.4 Voice

Ask should feel like a warm, chatty TV/movie friend:

- Direct answer in first few lines.
- Opinionated without being harsh.
- Spoiler-safe unless invited otherwise.
- Bulleted lists for multiple recommendations.
- Honest about mixed reception.
- No recommendations outside TV/movies.

## 16. Alchemy Plan

Alchemy is a structured discovery workbench.

### 16.1 Flow

1. Select at least 2 starting shows from saved library or global catalog.
2. Tap "Conceptualize Shows".
3. Generate shared concepts across all inputs.
4. Select 1 to 8 concepts.
5. Tap "ALCHEMIZE!".
6. Show 6 resolved recommendations with reasons.
7. Optionally choose "More Alchemy!" to chain using results as new inputs.

### 16.2 State Rules

- Changing input shows clears concepts and results.
- Changing selected concepts clears results.
- Alchemy results and reasons are session-only.
- Chained rounds are session-only.

### 16.3 Concept Generation

Multi-show concepts must be:

- Shared across all input shows.
- 1 to 3 words.
- Bullet list only.
- Evocative, specific, and spoiler-free.
- Ordered by strength.
- Varied across structure, tone, emotion, relationship dynamics, and craft.

### 16.4 Recommendation Results

Requirements:

- Return 6 recommendations.
- Resolve each to a real catalog item where possible.
- Each reason names selected concepts.
- Include 1 to 2 defensible surprises when possible.
- Do not display hallucinated results as selectable shows.

## 17. Explore Similar Plan

Explore Similar lives on the Show Detail page.

Flow:

1. User taps "Get Concepts".
2. App generates 8 default concepts for the single show.
3. User selects 1 or more concepts, capped consistently with Alchemy.
4. User taps "Explore Shows".
5. App returns 5 resolved recommendations.

Rules:

- Concepts are 1 to 3 word ingredients.
- Reasons must explicitly connect to selected concepts.
- Results are transient.
- Downstream recommendations clear when concept selection changes.
- UI copy should communicate "pick the ingredients you want more of."

## 18. AI Service Layer

Create provider-neutral AI services:

```text
AiProvider
  generateAskResponse(context)
  summarizeConversation(turns)
  generateScoop(show, userOverlay, libraryContext)
  generateConcepts(inputShows, mode)
  generateConceptRecommendations(input)
```

Service modules:

- `askService`
- `scoopService`
- `conceptService`
- `recommendationResolutionService`
- `libraryContextService`

Guardrails:

- Keep prompts out of UI components.
- Include user library context only as needed.
- Keep all AI surfaces in TV/movie domain.
- Enforce spoiler-safe defaults.
- Retry structured outputs once on parse failure.
- Log structured failures without storing secrets or full private prompts.

## 19. Settings And Your Data Plan

Settings sections:

- App settings:
  - font size
  - Search on Launch
- User:
  - username
- AI:
  - provider API key if user-entered keys are supported
  - model selection
- Integrations:
  - catalog API key if user-entered keys are supported
- Your Data:
  - Export My Data
  - Import/Restore placeholder or disabled entry if not implemented

Export behavior:

- Produce a `.zip`.
- Include JSON with all saved shows and My Data.
- Encode dates as ISO-8601.
- Scope export to `(namespace_id, user_id)`.
- Do not include Ask history, Alchemy sessions, or transient recommendations.

Key handling:

- Secrets must never be committed.
- User-entered keys may be stored only if explicitly supported and documented.
- Environment keys remain the benchmark default.

## 20. Import And Migration Plan

Import/Restore is optional/open in the PRD, so implement only if time allows. If implemented:

- Validate backup schema version.
- Import into the current namespace and user only.
- Merge public shows with catalog merge rules.
- Merge user overlays by timestamp.
- Report conflicts and skipped rows.

Migrations:

- Use Supabase migrations for deterministic schema evolution.
- Track `data_model_version`.
- Preserve saved shows, tags, statuses, interest, ratings, and AI Scoop across upgrades.

## 21. Namespace Reset And Test Data

Add a one-command reset script:

```text
npm run test:reset -- --namespace $NEXT_PUBLIC_NAMESPACE_ID
```

Behavior:

- Deletes only rows in the provided namespace.
- Refuses to run without namespace.
- Refuses to run against production unless explicitly allowed by a protected flag.
- Can create seed fixtures for deterministic tests.

All tests that write data should use a test namespace and test user.

## 22. Error Handling And Loading States

Required user-facing states:

- Catalog search failure.
- Catalog detail lookup failure.
- AI provider unavailable.
- AI structured output parse failure.
- Recommendation cannot resolve to real show.
- Supabase write failure.
- Namespace/user context missing.
- Empty collection.
- Empty filtered collection.
- No provider availability.
- No cast/crew.
- No videos/images.

Principles:

- Keep existing user data visible if a refresh fails.
- Avoid blank waits during Scoop generation.
- Do not block collection management because AI is unavailable.
- Treat local cache as disposable.

## 23. Accessibility And Responsive UI

Implement:

- Keyboard-accessible chips, tabs, sliders, and grids.
- Clear focus states.
- Semantic buttons for all actions.
- ARIA labels for icon-only controls.
- Responsive poster grids.
- Readable font size setting.
- Reduced motion respect for carousels and streaming text.
- Non-color-only badges for in-collection and rating indicators.

The UI should feel direct and powerful without modal walls except destructive removal confirmation.

## 24. Implementation Phases

### Phase 0: Project Foundation

Deliver:

- Next.js app scaffold.
- TypeScript, linting, formatting, and tests.
- Theme tokens and shared UI primitives.
- Environment validation.
- Supabase client setup.
- `.env.example` and `.gitignore`.
- Package scripts:
  - `dev`
  - `build`
  - `test`
  - `lint`
  - `test:reset`

Exit criteria:

- App boots locally with configured env.
- Tests can run without Docker.
- Missing env vars fail clearly.

### Phase 1: Persistence And Identity

Deliver:

- Supabase migrations for core tables.
- Request context resolver.
- Repository layer requiring namespace and user.
- Pure merge utilities.
- Namespace reset script.
- Unit tests for merge and partitioning rules.

Exit criteria:

- Two namespaces cannot see each other's rows.
- Two users in one namespace cannot see each other's overlays.
- Clearing local browser storage does not delete persisted data.
- Reset script only deletes one namespace.

### Phase 2: Catalog Provider And Show Projection

Deliver:

- Catalog provider abstraction.
- External payload normalizers.
- Public show upsert/merge.
- Display show composer.
- Recommendation resolver.
- Tests for mapping, non-empty merge, title matching, and overlay projection.

Exit criteria:

- Catalog details persist public fields.
- Public refresh never removes stored non-empty values.
- Saved overlays display everywhere through the same projection.

### Phase 3: Collection Home

Deliver:

- Home route.
- Filter navigation.
- Media type toggle.
- Status grouping.
- Show tiles with collection and rating indicators.
- Empty states.

Exit criteria:

- Saved shows group into Active, Excited, Interested, and Other.
- Tag, no-tag, genre, decade, score, and media type filters work.
- User overlay values are visible on all tiles.

### Phase 4: Search And Find Hub

Deliver:

- Find hub mode switcher.
- Search mode.
- Search result grid.
- In-collection and rating indicators.
- Search on Launch behavior.

Exit criteria:

- Search results open Detail.
- Saved shows are marked in search results.
- Search has no AI voice.

### Phase 5: Show Detail And My Data

Deliver:

- Detail route.
- Header media with fallbacks.
- Core facts.
- Relationship toolbar.
- Rating control.
- Tags editor.
- Overview.
- Traditional recommendations.
- Providers.
- Cast and crew.
- TV seasons.
- Movie budget/revenue.

Exit criteria:

- Status/interest mapping exactly matches PRD.
- Rating unsaved show saves as Done.
- Tagging unsaved show saves as Later + Interested.
- Clearing status confirms removal and clears all My Data.
- Public refresh preserves user overlay.

### Phase 6: AI Scoop

Deliver:

- Scoop service.
- Scoop prompt/response adapter.
- Streaming UI when supported.
- 4-hour freshness check.
- Saved-only persistence rule.
- Tests for caching and persistence behavior.

Exit criteria:

- Saved show Scoop persists.
- Unsaved show Scoop remains ephemeral.
- Expired Scoop regenerates on demand.
- UI never waits on a blank state.

### Phase 7: Ask

Deliver:

- Ask chat UI.
- Starter prompts with random refresh.
- Session state and reset.
- Conversation summarization.
- Mentioned shows structured parsing.
- Mentioned shows strip.
- Ask-about-this-show handoff from Detail.

Exit criteria:

- Ask history is not persisted.
- Mentioned shows resolve to real selectable items where possible.
- Failed mappings hand off to Search or non-interactive display.
- Older turns are summarized after roughly 10 messages.

### Phase 8: Concepts, Explore Similar, And Alchemy

Deliver:

- Concept generation service.
- Explore Similar panel.
- Alchemy workbench.
- Concept chip selection limits.
- Recommendation generation and resolution.
- Chaining support for Alchemy.

Exit criteria:

- Single-show concepts default to 8.
- Explore Similar returns 5 recommendations.
- Alchemy requires at least 2 inputs and returns 6 recommendations.
- Selecting/changing inputs or concepts clears downstream results.
- Concept and rec reasons meet quality bar.

### Phase 9: Person Detail

Deliver:

- Person route.
- Profile, images, bio.
- Credits grouped by year.
- Analytics charts.
- Credit-to-detail navigation.

Exit criteria:

- Cast/crew links open person pages.
- Credits show saved overlays where applicable.
- Selecting a credit opens Show Detail.

### Phase 10: Settings, Export, And Data Continuity

Deliver:

- Settings route.
- Font size and Search on Launch.
- Username.
- AI model and optional key settings.
- Catalog integration key setting if supported.
- Export My Data zip.
- Migration/version tracking.

Exit criteria:

- Export contains all saved shows and My Data for one `(namespace_id, user_id)`.
- Export excludes transient AI/chat/session data.
- Settings persist server-side.
- Secrets are not committed.

### Phase 11: Quality Hardening

Deliver:

- Unit test coverage for business rules.
- Integration tests for server actions and repositories.
- Route/component tests for major workflows.
- AI golden-set style manual test fixtures.
- Accessibility pass.
- Build and lint cleanup.

Exit criteria:

- `npm test` passes.
- `npm run build` passes.
- Namespace destructive tests pass.
- Manual smoke test covers core journeys.

## 25. Test Plan

### 25.1 Unit Tests

Cover:

- Status and interest mapping.
- Save defaults.
- Rating-to-save default Done.
- Tag-to-save default Later + Interested.
- Removal clears all My Data.
- Catalog `selectFirstNonEmpty`.
- User overlay timestamp conflict resolution.
- AI Scoop freshness.
- Concept output parser.
- Mentioned shows parser.
- Recommendation resolver title/media matching.
- Filter grouping and sorting.
- Export serialization.

### 25.2 Integration Tests

Cover:

- Create saved show in one namespace and verify another namespace cannot see it.
- Create saved show for one user and verify another user cannot see overlay.
- Search result opens detail and refreshes public data.
- Updating status persists and appears on Home and Search.
- Clearing status removes from collection.
- Rating unsaved show persists as Done.
- Adding tag unsaved persists as Later + Interested.
- Reset script deletes only test namespace.

### 25.3 Component And Route Tests

Cover:

- Collection empty state.
- Filter empty state.
- Home grouping.
- Find mode switching.
- Ask starter prompt refresh.
- Mentioned shows strip rendering.
- Alchemy step transitions.
- Explore Similar selection clearing.
- Detail page critical controls.
- Settings export action.

### 25.4 AI Quality Checks

Use the discovery quality bar:

- Voice adherence.
- Taste alignment.
- Surprise without betrayal.
- Specific reasoning.
- Real-show integrity.

Passing threshold:

- Voice at least 1.
- Taste alignment at least 1.
- Real-show integrity exactly 2.
- Total at least 7 out of 10.

## 26. Manual Smoke Test Checklist

Run with a fresh namespace and default user:

1. Start app with configured Supabase and catalog keys.
2. Search for a movie.
3. Open Detail.
4. Set Interested.
5. Return Home and confirm it appears under Interested.
6. Change to Excited and confirm it moves groups.
7. Add tags and filter by tag.
8. Rate an unsaved show and confirm it saves as Done.
9. Clear status and confirm all My Data disappears.
10. Generate Scoop on saved show and confirm persistence.
11. Generate Scoop on unsaved show and confirm it does not persist after leaving.
12. Use Ask to request recommendations and open a mentioned show.
13. Use Explore Similar to generate concepts and 5 recommendations.
14. Use Alchemy with 2+ shows, select concepts, generate 6 recommendations, and chain.
15. Open cast/crew person and navigate back through a credit.
16. Export My Data and inspect JSON contents.
17. Clear browser storage and confirm collection remains after reload.
18. Run namespace reset and confirm only that namespace is cleared.

## 27. Key Risks And Mitigations

### Risk: Overlay inconsistency across surfaces

Mitigation:

- Enforce `composeDisplayShow` as the only display projection.
- Add tests for Home, Search, Detail, recommendations, and AI mentions.

### Risk: Namespace/user leakage

Mitigation:

- Require context in all repository methods.
- Add database composite keys and indexes using namespace and user.
- Add integration tests that create cross-namespace and cross-user fixtures.

### Risk: AI hallucinated recommendations

Mitigation:

- Require structured recommendation outputs.
- Resolve through catalog before making items clickable.
- Non-interactive fallback for unresolved titles.
- Quality checks require real-show integrity.

### Risk: Prompt drift loses product voice

Mitigation:

- Centralize prompt builders.
- Test outputs against the AI voice and quality bar.
- Keep Search voice-free.
- Preserve spoiler-safe, opinionated, vibe-first rules.

### Risk: Local storage becomes accidental source of truth

Mitigation:

- Persist all user-owned data server-side.
- Treat local state as cache only.
- Add smoke test that clears browser storage.

### Risk: Public catalog refresh overwrites useful data

Mitigation:

- Implement and test `selectFirstNonEmpty`.
- Preserve user fields in overlay table.
- Update `details_update_date` without resetting `creation_date`.

### Risk: Destructive tests affect shared data

Mitigation:

- Namespace all test data.
- Reset script refuses missing namespace.
- Optional production guard.

## 28. Acceptance Criteria Trace

The implementation is complete when:

- Users can build and maintain a collection with statuses, interests, tags, ratings, and AI Scoop.
- Saved shows display the user-overlaid version everywhere.
- Search, Ask, Alchemy, Explore Similar, Show Detail, Person Detail, Home, and Settings are implemented.
- AI surfaces are taste-aware, spoiler-safe, opinionated, and resolve recs to real catalog items where possible.
- AI Scoop is cached for 4 hours and persists only for saved shows.
- Alchemy uses at least 2 input shows, up to 8 selected concepts, and returns 6 recs.
- Explore Similar generates 8 concepts and returns 5 recs.
- Ask uses session context, starter prompts, mention extraction, and short-term summarization.
- Export My Data produces a zip with saved shows and My Data.
- Supabase stores user data server-side with namespace and user partitioning.
- `.env.example`, migrations, one-command dev/test/reset scripts, and no-secret git hygiene are present.
- Clearing local storage does not lose user-owned data.
- Tests verify core business rules, data isolation, and destructive reset behavior.
