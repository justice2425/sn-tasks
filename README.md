# Tasks — a Todoist-style plugin for Standard Notes

A self-contained Standard Notes **editor plugin** that turns any note into a full task manager. It's a single `index.html` file — no build step, no dependencies, and it makes **zero network requests**. All task data is stored as JSON inside the note, so it's covered by Standard Notes' end-to-end encryption just like any other note.

**One board per note:** each note you open with this plugin is its own independent project/board.

Current version: **1.9.0**

---

## Features

### Views
- **List** — tasks grouped by list/column, or flat when sorted.
- **Board** — kanban columns with drag-and-drop between lists.
- **Agenda** — grouped by due window: Overdue, Today, Tomorrow, This week, Later, No date.
- **Matrix** — an Eisenhower 2×2 grid (urgent × important). Urgent = due within 2 days; important = Medium/High priority.

### Tasks
- **Due dates** with an **optional time** (e.g. "Tomorrow 3:00 PM"); overdue/today highlighting.
- **Priorities** (Low / Medium / High) with a colored edge stripe.
- **Tags** with custom colors, plus filtering and search.
- **Notes** with a **Markdown preview** (headings, bold/italic, lists, checkboxes, code, links).
- **Subtasks** (checklist with progress) and **links** (safe — only http/https/mailto are clickable).
- **Start ("hide until") dates** — a task stays out of List/Agenda until the date arrives (still visible on the Board with a ⏳ badge).
- **Dependencies** — "Blocked by" other tasks; shows a ⛔ badge until blockers are done.
- **Recurring tasks** — Daily / Weekday / Weekly / Monthly, with an "every N" interval and a choice of repeating from the **due date** or from **completion**. Completing a recurring task spawns the next occurrence automatically.

### Organizing
- **Sort** (top bar) applies to both List and Board — order tasks **within each column** by Due date, Priority, Created, or A–Z. "Manual order" keeps your drag arrangement.
- **Smart-view chips** — one-click All / Today / Overdue / Next 7 days / High priority.
- **Column color-coding** — a color swatch per board list (colored top stripe + a matching dot in List view).
- **Rename / reorder / color / delete** lists from the column header.
- **Archive** tasks (and "Archive all completed" from the Stats panel); **hide a task from the List view** while keeping it on the Board.

### Productivity
- **Natural-language quick-add** — type into a column's add box and it parses the rest:
  - Dates: `today`, `tomorrow`, weekday names, `next monday`, `in 3 days`, `2026-07-01`
  - Times: `at 3pm`, `9:30am`, `15:00`
  - Priority: `!high` / `!med` / `!low` (or `!1` / `!2` / `!3`)
  - Tags: `#finance #home`
  - Example: `Pay rent tomorrow at 9am !high #finance`
- **Bulk actions** — "Select" mode to multi-select, then Complete / Move / Archive / Delete at once.
- **Undo** — ↶ button or Ctrl/Cmd+Z reverts the last change (view/filter switches don't clutter the history).
- **Keyboard shortcuts** — `n` new task, `/` focus search, `1`/`2`/`3`/`4` switch List/Board/Agenda/Matrix.
- **Stats panel** — completion rate, active/completed/overdue/due-today counts, done-in-last-7-days, and breakdowns by priority and tag.
- **Due-today banner** — on opening a board, a dismissible summary of overdue + due-today items.

### Name-based automations
Two behaviors trigger off a column's **name** (case-insensitive), the same way:
- **A list named "Done"** — completing a task auto-moves it there (and un-completing sends it back to where it came from).
- **A list named "Work"** — completing a task there asks to confirm, then **permanently deletes** it (recurring Work tasks still spawn their next occurrence first). See "Deletion & recoverability" below.

Rename these lists and the corresponding behavior simply turns off until a list has that name again.

---

## What's in this folder

| File | Purpose |
|------|---------|
| `index.html` | The plugin itself (the whole app). This is what Standard Notes loads. |
| `ext.json` | The plugin **descriptor**. Its URL is the install link you paste into Standard Notes. |
| `package.json` | Optional metadata. Not needed for self-hosting. |
| `test-harness.html` | A local simulator of the Standard Notes host, for trying the plugin in a browser before installing. |
| `README.md` | This file. |

---

## Install (GitHub Pages)

Standard Notes installs a plugin from a URL, so the files need to be hosted over HTTPS. GitHub Pages is free and works.

1. Create a **public** repo (e.g. `sn-tasks`) and upload `index.html` and `ext.json`.
2. The `ext.json` is already filled in for the repo `https://justice2425.github.io/sn-tasks/`. If you use a different username/repo, update the three URLs and the `identifier`.
3. Repo **Settings → Pages → Deploy from a branch → `main` / `(root)`** → Save.
4. In Standard Notes: **Preferences → Plugins → Install Custom Plugin**, paste your `ext.json` URL (e.g. `https://justice2425.github.io/sn-tasks/ext.json`), Install.
5. Create a note, pick **Tasks** as its editor.

**Updating:** edit `index.html`, bump `"version"` in `ext.json`, commit. GitHub Pages caches for ~10 minutes; then reload Standard Notes (or remove/reinstall the plugin) to pick up the new version. Your task data lives in your notes, so updates never touch it.

### Local-only alternative
Run `python3 -m http.server 8080` in this folder, point the `ext.json` URLs at `http://localhost:8080/...`, and install that. Works while the server runs, mainly on desktop/web.

---

## Try it without installing
Run a local server (`python3 -m http.server`) and open `test-harness.html`. It simulates the Standard Notes host so you can use the plugin in a browser; the side panel shows exactly what would be saved into the note.

---

## Data, security & privacy
- Task data is JSON stored in the note's text, so it's **end-to-end encrypted** by Standard Notes like any note, and included in your normal backups.
- The plugin runs in a **sandboxed iframe**, loads **no external scripts/fonts**, and makes **no network calls** — it can't send your data anywhere. You can read the whole `index.html`.
- The main trust you take on is the integrity of wherever you host the files (your GitHub account). **Enable 2FA on GitHub.** Third-party plugins are not reviewed by Standard Notes.
- If you switch the note back to the Plain Text editor you'll see the raw JSON — that's expected; switch back to Tasks for the UI.

## Deletion & recoverability
- Deleting a task removes it from the note's JSON and saves the note without it. There is no in-app trash.
- **Undo** holds snapshots in memory for the session. Normal deletes are undoable until you reload. **"Work" deletions are hard**: they're scrubbed from the in-session undo history and cannot be brought back with Undo.
- **Important caveat:** Standard Notes keeps its own **note revision history** (server-side, retention depends on your plan). The plugin cannot see or purge that, so an old revision of the note could still contain a "deleted" task. For a truly unrecoverable deletion you'd also need to clear that note's revision history in Standard Notes and remove it from any backups.

---

## Known limitations
- **No reminders / notifications.** The plugin can't run in the background or make network calls, so it can't alert you when a task is due. Due times are for display and sorting only; overdue is computed by date.
- **No cross-board search.** Each board is one note; the plugin only sees the note you have open.
- **No true file embedding.** Links/URLs work; embedding binary files (PDF, image) is not available to editor plugins — that's Standard Notes' separate Files system, which only its own "Super" editor can reach.

---

## Compatibility note
This targets Standard Notes' documented editor-component protocol (`stream-context-item` / `save-items`). The bridge that handles it is the clearly marked "PART 1 — Standard Notes component bridge" section at the top of `index.html`, which is where any future protocol adjustment would go.
