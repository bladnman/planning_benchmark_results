# Implementation Plan: Personal TV + Movie Companion App

## 1. Executive Summary
This document outlines the comprehensive implementation plan for building a personal TV and movie companion application. The app allows users to build a personal taste profile (via statuses, interests, tags, and ratings) and leverages this profile alongside a "TV nerd friend" AI persona to drive discovery through Search, Conversational AI ("Ask"), Concept-based exploration ("Explore Similar"), and multi-show blending ("Alchemy"). 

Per the Infrastructure Rider and development guidelines, the application will be built using a **Next.js** (App Router) runtime and **Supabase** for persistence, following a **Fractal Architecture** pattern.

## 2. Architecture & Infrastructure

### 2.1 Technology Stack
*   **Runtime / Framework:** Next.js (latest stable, React)
*   **Persistence:** Supabase (hosted or local) via official client libraries.
*   **Styling:** Theme tokens, CSS Modules or Tailwind (no inline styles or magic numbers), adhering to "Humble Components" guidelines.
*   **AI Integration:** LLM provider (e.g., OpenAI, Anthropic) via API keys provided in environment variables.
*   **External Catalog Integration:** TMDB or similar provider for global show data via API.

### 2.2 Project Structure (Fractal Architecture)
The repository will follow the specified strict fractal structure, prioritizing co-location and isolation:
```text
src/
├── config/          # Global constants, env vars, catalog API configs
├── theme/           # Design tokens, color palettes
├── components/      # Shared UI primitives (buttons, layout wrappers)
├── hooks/           # Global hooks (e.g., useNamespace, useUser)
├── utils/           # Global pure functions (e.g., mergeShowData)
└── app/             # Next.js App Router (maps to Pages)
    ├── layout.tsx
    ├── page.tsx     # Home (Collection)
    └── features/
        ├── CollectionGrid/
        ├── SidebarNavigation/
        └── ...
    ├── find/
    │   ├── search/
    │   ├── ask/
    │   └── alchemy/
    ├── show/
    │   └── [id]/
    └── person/
        └── [id]/
```

### 2.3 Identity & Run Isolation (Benchmark Mode)
*   **Namespace Partitioning:** Every database table will include a `namespace_id` to strictly isolate benchmark runs and destructive testing.
*   **User Identity:** Every user-owned record will map to an opaque `user_id`.
*   **Auth Strategy:** For benchmark mode, a development identity injection will be used (e.g., a static `X-User-Id` header and `X-Namespace-Id` header interceptor in Next.js middleware, or a simple dev-only selector) to bypass full OAuth. The schema will be designed so swapping to real OAuth requires only auth wiring, no data model redesign.
*   **Destructive Testing:** Scripts (`npm run test:reset`) will be implemented to clear data *only* for the current `namespace_id`, avoiding global teardowns.

### 2.4 Data Model & Persistence
The schema will heavily utilize Supabase PostgreSQL. Key entities include:
*   **Shows Table:** `id`, `namespace_id`, `user_id`, `title`, `showType`, catalog metadata (JSONB or columns for images, facts).
*   **User Data (My Data):** `myStatus`, `myInterest`, `myTags` (array), `myScore`, `aiScoop` alongside their respective `*UpdateDate` timestamps for conflict resolution.
*   **Settings Table:** `namespace_id`, `user_id`, `userName`, `catalogApiKey`, `aiApiKey`, `autoSearch`.

*Merge Strategy:* Whenever external catalog data is refreshed for an existing show, local user data (`my*` fields) and AI data will always take precedence based on update timestamps. Non-user catalog fields will use `selectFirstNonEmpty` to prevent blanking out data.

## 3. Core Domain Behaviors

### 3.1 Status & Auto-Save Rules
The application relies on implicit saves to reduce friction:
*   **Statuses:** Active, Later, Wait, Done, Quit.
*   **Interests:** Interested, Excited (both automatically set Status to "Later").
*   **Auto-Save Triggers:** 
    *   Rating an unsaved show auto-saves as `Done`.
    *   Adding a tag to an unsaved show auto-saves as `Later` + `Interested`.
    *   Setting any explicit status/interest saves the show.
*   **Removal:** Clearing the status removes the show and *all* associated "My Data" after user confirmation.

### 3.2 AI Data Persistence & Freshness
*   **The Scoop:** Persisted to the Show record *only* if the show is saved in the collection. Cached for 4 hours; regenerates on demand after expiry.
*   **Ask / Alchemy Sessions:** Highly ephemeral. No long-term persistence in the database; context lives in session state.

## 4. Feature Implementation Breakdown

### Phase 1: Foundation & Catalog Integration
1.  **Infrastructure Setup:** Initialize Next.js, Supabase client, `.env.example`, and migration scripts.
2.  **Catalog Service:** Build service to search TMDB/external API, fetch details, and normalize into the application's `Show` schema.
3.  **Data Layer:** Implement repository functions for retrieving, saving, and merging Shows, injecting `namespace_id` and `user_id`.

### Phase 2: Library & Show Detail Pages
1.  **Show Detail Page (`/show/[id]`):** 
    *   *Features:* Header media carousel, Core Facts, "My Relationship" controls (Status chips, Rating, Tags picker).
    *   *Logic:* Wire up auto-save rules and merge logic.
2.  **Home Collection (`/`):**
    *   *Layout:* Sidebar with static filters (All Shows) and dynamic Tag filters.
    *   *Main Area:* Grid grouped by status (Active, Excited, Interested, Other).
3.  **Person Detail Page (`/person/[id]`):** Cast/crew stats, filmography mapped by year.

### Phase 3: AI Persona & Prompts System
1.  **Prompt Engineering:** Implement the "TV Nerd Friend" persona with tone sliders (Warm, specific, spoiler-safe).
2.  **AI Services:**
    *   `ScoopService`: Structured prompt to return a mini-blog review. Supports progressive streaming to the UI.
    *   `ConceptService`: Generates 1-3 word evocative ingredients (bulleted lists).
    *   `RecommendationService`: Takes concepts + library context, outputs specific recs with reasons, and attempts to resolve them to real catalog IDs.
    *   `ChatService`: Maintains short-term session history, summarizes older turns, outputs standard text and a structured `showList` payload for inline mentions.

### Phase 4: Discovery Surfaces
1.  **Find Hub - Search:** Basic text search hitting the global catalog. Identifies items already in the user's collection.
2.  **Find Hub - Ask:** Chat interface. Resolves mentioned shows to render a "mentioned shows" strip.
3.  **Explore Similar (Detail Page):** "Get Concepts" -> Select Chips -> Fetch Recs.
4.  **Alchemy:** Select 2+ shows from library/search -> "Conceptualize" (extract shared core vibes) -> Select Chips -> "Alchemize!" -> Return 6 grounded recommendations.

### Phase 5: Polish & Data Portability
1.  **Settings Page:** AI/Catalog key management, Font size preferences.
2.  **Export:** "Export My Data" function to generate a `.zip` with a JSON payload of the user's library and settings.
3.  **Review & Refine:** Ensure "Humble Components" are strictly adhered to and no styling magic numbers exist. Run `npm run test:reset` to validate destructive testing bounds.

## 5. Risk Mitigation & Edge Cases
*   **AI Hallucinations on Shows:** AI responses must be robustly parsed. The `RecommendationService` will execute a catalog lookup on the provided `externalId` or Title. If a show cannot be resolved, it will gracefully fallback to plain text or a search deep-link.
*   **Data Migrations:** AppMetadata table will track schema versions ensuring future upgrades safely carry forward "My Data" without manual intervention.
*   **State Management Context:** Ensure React state accurately reflects local overrides before Supabase mutations complete (optimistic UI updates for relationship controls).