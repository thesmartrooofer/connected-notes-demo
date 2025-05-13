# Connected Notes Demo App - Functional Specification & Implementation Checklist

## 1. Project Overview

**Goal:** Create a simple, single-user web application demonstrating the core concept of a connected notebook where notes are the primary focus, linked to each other and to source materials. Data will be stored locally in the browser using IndexedDB.

**Version:** Demo v1 (as of May 13, 2025)

---

## 2. Functional Specification (Demo Version)

**2.1. Core Concept:**
* The application presents a single, persistent "notebook" environment to the user.
* The user can create, view, edit, and delete notes.
* The user can add representations of "sources" (e.g., summaries or metadata of documents/URLs).
* Notes can be explicitly linked to other notes or to sources.
* All data persists locally in the browser using IndexedDB. Content (notes and source descriptions) supports Markdown.

**2.2. User Interface (High-Level):**
* **Main View:** A two-column layout.
    * **Sidebar:** Displays scrollable lists of existing notes and sources, each with "Add" buttons.
    * **Content Pane:** Displays a welcome message initially. When a note or source is selected, its details are shown here.
* **Note Editor:** A modal dialog for creating and editing notes. This supports Markdown for the content field and includes sections for linking to other notes and sources.
* **Source Adder:** A modal dialog for creating and editing source metadata (e.g., title, type, a brief description/content snippet in Markdown, and an optional location URL/reference).
* **Linking Mechanism:** An intuitive way (checkboxes) within the note editor to:
    * Select other existing notes to link to.
    * Select existing sources to link to.
* **Feedback Mechanism:** A small, temporary message area for user feedback (e.g., success or error messages).

**2.3. Key Features:**

* **Note Management:**
    * Create a new note with a title and content (Markdown).
    * View a list of all notes (sorted by last updated).
    * Open and view the content of a specific note (Markdown rendered as HTML).
    * Edit the title and content of an existing note.
    * Delete a note (with confirmation, and handles cleanup of links from other notes).
* **Source Management:**
    * Add a new source with a title, type (e.g., 'URL', 'Document'), content/description (Markdown), and an optional location.
    * View a list of all sources (sorted by last updated).
    * View the details of a specific source (Markdown rendered as HTML).
    * Delete a source (with confirmation, and handles cleanup of links from notes).
* **Linking:**
    * While creating/editing a note, provide a UI to search/select other notes or sources to establish a link.
    * Store these links as part of the note's data.
    * Display established links when viewing a note, allowing navigation to the linked item.
    * When viewing a source, display a list of notes that link *to* this source (backlinks).
* **Persistence:**
    * All notes, sources, and their relationships are saved to IndexedDB (via Dexie.js).
    * Data is loaded from IndexedDB when the application starts.
* **Markdown Support:**
    * Note content and Source content/description fields accept Markdown input.
    * Rendered Markdown is displayed in the content pane.

**2.4. Non-Goals for Demo:**
* User accounts or multi-user collaboration.
* Multiple notebooks.
* Actual file uploading, processing, or web scraping for sources.
* Advanced AI features (summarization, Q&A).
* Full WYSIWYG rich text editing (Markdown is the chosen format).
* Complex search functionality (beyond basic listing).
* Folder structures or advanced tagging systems (beyond direct linking).

---

## 3. IndexedDB Data Model

**Database Name:** `connectedNotesDB`
**Version:** `1`

**3.1. `notes` Object Store:**
* **Key Path:** `id` (auto-incrementing)
* **Fields:**
    * `id`: Number (Primary Key)
    * `title`: String
    * `content`: String (Markdown)
    * `createdAt`: String (ISO 8601 Timestamp)
    * `updatedAt`: String (ISO 8601 Timestamp)
    * `linkedNoteIds`: Array of Numbers (IDs from `notes` store)
    * `linkedSourceIds`: Array of Numbers (IDs from `sources` store)
* **Indexes:** `title`, `updatedAt`, `linkedNoteIds` (multiEntry), `linkedSourceIds` (multiEntry)

**3.2. `sources` Object Store:**
* **Key Path:** `id` (auto-incrementing)
* **Fields:**
    * `id`: Number (Primary Key)
    * `title`: String
    * `type`: String (e.g., 'URL', 'Document')
    * `content`: String (Markdown, for description/summary)
    * `location`: String (Optional URL or reference string)
    * `createdAt`: String (ISO 8601 Timestamp)
    * `updatedAt`: String (ISO 8601 Timestamp)
* **Indexes:** `title`, `type`, `updatedAt`

---

## 4. Implementation Checklist & Status

| Feature Category      | Feature Detail                                         | Status                     | Notes                                                                 |
| :-------------------- | :----------------------------------------------------- | :------------------------- | :-------------------------------------------------------------------- |
| **Core Concept** | Single, persistent notebook environment                | Implemented                | App operates as a single notebook.                                    |
|                       | User can create, view, edit, delete notes            | Implemented                | Core CRUD for notes is present.                                       |
|                       | User can add "sources" (metadata)                    | Implemented                | Core CRUD for sources is present.                                     |
|                       | Notes can be linked to other notes or sources        | Implemented                | Linking mechanism in note editor.                                     |
|                       | Data persists locally (IndexedDB)                    | Implemented                | Dexie.js used for IndexedDB.                                          |
| **User Interface** | Main View: Sidebar (lists), Content Pane (display)   | Implemented                | Sidebar lists notes/sources; main pane shows selected item/welcome.   |
|                       | Note Editor: Modal for C/U notes (Markdown)          | Implemented                | Modal for note creation/editing with Markdown support.                |
|                       | Source Adder: Modal for C/U sources (Markdown)       | Implemented                | Modal for source creation/editing.                                    |
|                       | Linking Mechanism: UI for linking in Note Editor     | Implemented                | Checkboxes in note modal for linking.                                 |
|                       | View Backlinks (notes linking *to* source)           | Implemented                | Displayed when viewing a source.                                      |
| **Note Management** | Create new note (title, content)                     | Implemented                | Via "Add Note" button and modal.                                      |
|                       | View list of all notes (sorted by last updated)      | Implemented                | Sorted by last updated in sidebar.                                    |
|                       | Open and view content of a specific note             | Implemented                | Displayed in main content pane with Markdown rendering.               |
|                       | Edit title and content of existing note              | Implemented                | Via edit button on note display.                                      |
|                       | Delete a note (with link cleanup)                    | Implemented                | With confirmation and updates to `linkedNoteIds` in other notes.      |
| **Source Management** | Add new source (title, type, content, location)      | Implemented                | Via "Add Source" button and modal.                                    |
|                       | View list of all sources (sorted by last updated)    | Implemented                | Sorted by last updated in sidebar.                                    |
|                       | View details of a specific source                    | Implemented                | Displayed in main content pane.                                       |
|                       | Delete a source (with link cleanup)                  | Implemented                | With confirmation and updates to `linkedSourceIds` in notes.          |
| **Linking** | UI to select other notes/sources for linking         | Implemented                | Checkboxes in note modal.                                             |
|                       | Store links as part of note's data                   | Implemented                | `linkedNoteIds`, `linkedSourceIds` arrays in note objects.            |
|                       | Display established links when viewing a note        | Implemented                | Clickable links in note display area.                                 |
|                       | Navigation to linked items                           | Implemented                | Links call `displayItem()`.                                           |
| **Persistence** | All data saved automatically to IndexedDB            | Implemented                | On form submission.                                                   |
|                       | Data loaded from IndexedDB on app start              | Implemented                | `initializeApp` loads lists.                                          |
| **Markdown Support** | Notes content supports Markdown                      | Implemented                | `marked.js` used for rendering.                                       |
|                       | Sources content/description supports Markdown        | Implemented                | `marked.js` used for rendering.                                       |
| **Error Handling** | Basic error messages for DB operations               | Implemented                | `showMessage` utility. Modal closing on save error.                   |
|                       | Confirmation for delete operations                   | Implemented                | `confirm()` dialog.                                                   |
| **UI/UX** | Responsive layout (basic)                            | Implemented                | Tailwind CSS provides basic responsiveness.                           |
|                       | Clear visual distinction between notes/sources       | Implemented                | Color coding (sky/blue for notes, emerald/green for sources).         |
|                       | Feedback messages for user actions                   | Implemented                | `showMessage` utility.                                                |
|                       | Modal closing (Escape, backdrop, cancel/save)      | Implemented                | Standard modal behavior, including on save error.                     |
| **Testing** | Core CRUD for Notes                                  | Needs User Testing         | Verify all C/R/U/D scenarios, including empty/edge cases.           |
|                       | Core CRUD for Sources                                | Needs User Testing         | Verify all C/R/U/D scenarios, including empty/edge cases.           |
|                       | Linking/Unlinking Notes & Sources                    | Needs User Testing         | Create multiple items, link, unlink, check display.                 |
|                       | Link navigation (Note <-> Note, Note <-> Source)     | Needs User Testing         | Verify clicking links loads the correct item.                         |
|                       | Deleting items with links (Notes & Sources)          | Needs User Testing         | Verify links are cleaned up in referring items and UI updates.        |
|                       | Markdown rendering in notes and sources              | Needs User Testing         | Test various Markdown syntaxes (headings, lists, links, bold, etc.). |
|                       | Edge cases (empty inputs, large content)             | Needs User Testing         | E.g., creating items with no title/content, very long content.      |
|                       | Browser compatibility (modern browsers)              | Needs User Testing         | Primarily developed/tested on one browser; check others (Chrome, FF, Edge). |
|                       | Data persistence across sessions/refreshes           | Needs User Testing         | Close/reopen browser/tab to ensure data is saved and reloaded.      |
|                       | Modal behavior (opening, closing, form reset)        | Implemented & Tested     | General modal flow seems okay.                                        |
| **Non-Goals (Demo)** | User accounts / multi-user                         | Not Implemented (Per Spec) | Future consideration if app scope expands.                            |
|                       | Multiple notebooks                                   | Not Implemented (Per Spec) | Future consideration.                                                 |
|                       | File uploads/processing/web scraping                 | Not Implemented (Per Spec) | Future consideration for source types.                                |
|                       | Advanced AI features                                 | Not Implemented (Per Spec) |                                                                       |
|                       | Full WYSIWYG Rich Text Editor                        | Not Implemented (Per Spec) | Markdown is used.                                                     |
|                       | Complex Search / Advanced Filtering                  | Not Implemented (Per Spec) | Basic lists by last updated. Future: full-text search.                |
|                       | Folder structures or advanced tagging                | Not Implemented (Per Spec) | Future consideration for organization.                                |
| **Future Ideas** | Data Export/Import                                   | To Be Considered           | For data backup/migration (e.g., JSON export).                        |
|                       | Enhanced Search (full-text, by links, by type)       | To Be Considered           | Improve discoverability.                                              |
|                       | Visual graph of connections                          | To Be Considered           | For visualizing relationships.                                        |
|                       | More robust & specific error handling/UI states      | To Be Considered           | E.g., distinct loading states, more detailed error popups.            |
|                       | UI Themes (Light/Dark)                               | To Be Considered           | User preference for appearance.                                       |
|                       | Keyboard shortcuts for common actions                | To Be Considered           | E.g., Ctrl+S to save in modal, `n` for new note.                    |

