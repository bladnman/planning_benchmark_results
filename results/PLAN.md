# Implementation Plan: Personal TV + Movie Companion

This document outlines the comprehensive plan to build the personal TV and movie companion app as defined in the product requirements documents (PRDs).

## 1. Architecture & Tech Stack

Following the Infrastructure & Execution Rider PRD, the baseline stack is:

*   **Framework:** Next.js (App Router recommended for optimal server/client separation).
*   **Database/Backend:** Supabase (PostgreSQL, Auth, Edge Functions if needed).
*   **Styling:** Tailwind CSS (or standard CSS modules, per project preference, ensuring clean and responsive UI).
*   **External APIs:**
    *   **Catalog:** TMDB (The Movie Database) or similar provider for global show data.
    *   **AI:** OpenAI (or similar) for conversational discovery, concepts, and "The Scoop".

## 2. Infrastructure & Isolation Strategy

To ensure repeatable benchmark runs and prevent data collisions:

*   **Namespace Isolation:** Every database table containing user data will include a `namespace_id` column. All queries will be scoped to this namespace.
*   **User Identity:** Every user-owned record will have a `user_id`. For benchmark mode, this will be injected via a header (`X-User-Id`), a local dev selector, or a fixed default user. Full OAuth will not be implemented initially but the schema will support it.
*   **Database Setup:** We will provide Supabase migrations to define the schema deterministically.
*   **Environment Variables:** A `.env.example` will be provided requiring `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`, `CATALOG_API_KEY`, and `AI_API_KEY`.
*   **Scripts:** Package.json will include scripts for `dev`, `test`, and `test:reset` (to clear data for a specific namespace).

## 3. Database Schema (Supabase PostgreSQL)

Based on `storage-schema.ts` and the isolation requirements:

**Table: `shows`**
*   `id` (uuid, primary key)
*   `namespace_id` (string, required for isolation)
*   `user_id` (string, required)
*   `catalog_id` (string, external ID)
*   `title`, `show_type`
*   `my_status`, `my_interest`, `my_rating`, `my_tags` (jsonb array)
*   Update timestamps for all "my_" fields (`my_status_update_date`, etc.)
*   `ai_scoop`, `ai_scoop_update_date`
*   Cached catalog data (overview, poster_url, etc. - refreshed but not overwriting user data)
*   *Composite Unique Constraint:* `(namespace_id, user_id, catalog_id)`

**Table: `settings` (Cloud Settings)**
*   `namespace_id`, `user_id`
*   `auto_search`, `font_size`, `theme`, etc.

*Merge Strategy:* When fetching a show, we pull the public catalog data. If the user has saved the show in the `shows` table, the user's data (`my_status`, etc.) overlays and takes precedence over the public data. Conflicts resolve by taking the most recently updated field.

## 4. Core Features Implementation

### 4.1. Show Representation & Detail Page
*   **Component:** `ShowDetail`
*   **Layout Hierarchy:** Header Media -> Core Facts (Year, Score) -> User Relationship Controls (Status Chips, Rating, Tags) -> Overview -> The Scoop (AI) -> AI Ask CTA -> Recommendations -> Explore Similar (Concepts) -> Cast/Crew.
*   **Behavior:**
    *   Setting status, rating, or adding a tag triggers an auto-save to the database.
    *   First rating sets status to `Done`.
    *   First tag sets status to `Later`, interest to `Interested`.
    *   Clearing status removes the show and clears all user data (with confirmation).

### 4.2. Collection Home (The Library)
*   **Component:** `LibraryView`
*   **Filtering:** Sidebar with tags, media type (All/Movies/TV), and data filters (genre, decade, score).
*   **Grouping:** Results grouped by status: Active -> Excited (Later + Excited) -> Interested (Later + Interested) -> Other (Wait, Quit, Done).
*   **Indicators:** Show tiles display badges for "in-collection" and "user-rated".

### 4.3. Discovery Hub (Find)

#### 4.3.1. Search
*   Live query against external catalog API.
*   Marks results already in the user's collection.

#### 4.3.2. Ask (AI Chat)
*   **Context:** Sends user's library context and conversation history to the AI.
*   **Output Parsing:** Parses structured output (`Title::externalId::mediaType;;...`) to render clickable show mentions inline.
*   **Voice:** Fun, chatty, spoiler-safe, opinionated TV nerd.

#### 4.3.3. Alchemy (Structured Discovery)
*   **Flow:** User selects 2+ shows -> Fetch shared concepts via AI -> User selects up to 8 concepts -> Fetch recommendations based on concepts.
*   **State Management:** Ephemeral session state. Cleared on exit. Chainable.

#### 4.3.4. Explore Similar (Per-Show Concepts)
*   Available on the Detail Page.
*   Fetches concepts for the single show -> User selects concepts -> Returns 5 recommendations.

### 4.4. AI Integrations (The "Soul")
*   **The Scoop:** Triggered from Detail Page. Cached for 4 hours. Progressive streaming UI. Evaluates "fit" and provides a personal take.
*   **Prompting:** We will adhere strictly to the "AI Prompting Context" and "Voice Personality" guides, ensuring the AI focuses on vibe, structure, and emotional palette rather than generic plot summaries.

## 5. Development Phases

**Phase 1: Setup & Infrastructure**
*   Initialize Next.js project.
*   Setup Supabase local environment and define migrations (schema with namespace/user isolation).
*   Implement database access layer and identity injection middleware.
*   Create `.env.example` and package scripts.

**Phase 2: Data Models & External APIs**
*   Implement TMDB client for searching, fetching details, and mapping to the internal `Show` model.
*   Implement OpenAI client for chat, concepts, and Scoop generation.

**Phase 3: Core UI & Library**
*   Build basic layout (Sidebar + Main Content).
*   Implement Show Tile component.
*   Build the Home Collection view (fetching from Supabase, applying grouping/filters).

**Phase 4: Detail Page & User Actions**
*   Build the comprehensive Detail Page.
*   Implement the save/update/remove logic for Status, Interest, Rating, and Tags (ensuring auto-save rules and merge conflict logic are respected).

**Phase 5: Discovery (Search & AI)**
*   Implement standard Search.
*   Implement "Ask" chat interface, including structured output parsing for mentioned shows.
*   Implement "Explore Similar" (Concepts -> Recs).
*   Implement "Alchemy" (Multi-show concept blending).

**Phase 6: Polish & Validation**
*   Implement data export functionality.
*   Refine AI prompts to match the Quality Bar guidelines.
*   Write destructive testing scripts to validate namespace isolation.
*   Final review against all PRD rules (auto-saves, fallbacks, UI hierarchy).
