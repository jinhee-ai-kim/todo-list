# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

Open `index.html` directly in any modern browser. No build step, no server, no dependencies to install.

## Architecture

This is a **single-file** client-side Kanban/study management app. All HTML, CSS, and JavaScript live in `index.html` (~1,375 lines).

**External dependencies (CDN only):**
- Tailwind CSS v3 — styling
- SortableJS v1.15.2 — drag-and-drop

**Persistence:** Browser `localStorage` with keys prefixed `sa_` (`sa_tasks`, `sa_categories`, `sa_goals`, `sa_appTitle`, `sa_darkMode`).

## Code Structure (index.html)

| Lines | Section |
|-------|---------|
| 16–183 | CSS (Tailwind extensions, dark mode, animations) |
| 184–503 | HTML (sidebar, board columns, modals) |
| 514–591 | State (`STATE` object) + localStorage helpers (`lsLoad`, `lsSave`) |
| 593–665 | Task CRUD (`taskCreate`, `taskUpdate`, `taskDelete`, `taskToggleComplete`) |
| 667–709 | Goal CRUD (`goalCreate`, `goalDelete`, `goalsReorder`) |
| 711–747 | Category CRUD (`categoryCreate`, `categoryRename`, `categoryDelete`) |
| 749–785 | Filtering helpers (`getFilteredByStatus`, `esc`, `formatDate`, `isOverdue`) |
| 787–966 | Rendering (`buildTaskCard`, `renderBoard`, `renderAll`, etc.) |
| 968–1049 | Drag-and-drop init (SortableJS for columns and goals) |
| 1051–1251 | Modal/form logic + dark mode + title editing |
| 1254–1356 | Event listeners (keyboard shortcuts: Ctrl+K = new task, Esc = close modal) |
| 1359–1370 | `init()` — entry point called on `DOMContentLoaded` |

## Data Schemas

**Task:**
```js
{ id, title, description, category, priority: 'low'|'medium'|'high',
  status: 'todo'|'inprogress'|'done', dueDate, completed, order, createdAt }
```

**Goal:**
```js
{ id, text, order, color: 'goal-blue'|'goal-mint'|'goal-purple'|... }
```

**Category:** plain string array.

## Key Conventions

- All user-generated strings rendered into HTML must go through `esc(str)` (XSS protection).
- Tasks auto-set `completed = true` when dragged into the DONE column; `completed = false` when moved out.
- `renderAll()` is the full re-render; prefer it after any state mutation. Individual `render*` helpers exist for partial updates.
- `STATE.sortables` holds active SortableJS instances — call `initDragAndDrop()` after re-rendering columns.
