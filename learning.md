# BuggyBoard Learning Guide

## 1. What is the tech stack? (full stack)

BuggyBoard is a full-stack TypeScript web app built around a Node.js runtime.

Core stack:
- Frontend: React
- Styling: Tailwind CSS
- Backend: Express
- Database: SQLite
- Language: TypeScript
- Package manager/workspace setup: npm workspaces
- Build/dev tooling: Vite for the frontend and concurrently for running frontend + backend together
- Code quality tooling: ESLint and Prettier
- Test tooling: Playwright is included in the root dependencies for the course, even though the app itself does not ship with automated tests by default

Project layout:
- `frontend/` — React app and UI
- `backend/` — Express API and SQLite database access
- `users.json` — project-level user credential source
- `backend/data/buggyboard.db` — SQLite database file created at runtime

The root `package.json` runs the app with:
- `npm install`
- `npm run dev`

This starts the frontend and backend together.

---

## 2. How are user credentials configured?

User credentials are stored in the root file `users.json`.

Current file contents:
- `buggy` / `1970beetle`
- `vanny` / `1979bus`

The backend loads users from `users.json` through `backend/src/users.ts`.

Validation logic is in `backend/src/authService.ts`:
- username and password are checked for empty values
- whitespace around the username is trimmed before validation
- login only succeeds if the username and password match an entry exactly in `users.json`

So this app is not using a database table for users; it is using a simple JSON file as the source of truth for valid accounts.

---

## 3. What are the main behaviors of the app?

BuggyBoard is a simple bug tracker used as a learning app for testing web apps. Its main behaviors are:

- User login with username and password
- Redirect to the board page after successful login
- Protect route access so unauthenticated users are sent to `/login`
- View a list of bugs on the board
- Create new bugs from a modal form
- Edit existing bugs from a modal form
- Delete bugs
- Filter bugs by Open vs Closed
- Sort board columns by ID, severity, title, or owner
- Search bugs by title text
- Use severity color coding for HIGH, MID, and LOW
- Log out from the title bar

The app stores bug records in SQLite with fields such as:
- id
- title
- severity
- owner
- description
- state

Bug states are stored as OPEN or CLOSED, and severity is stored as HIGH, MID, or LOW.

---

## 4. How do I perform the main user workflows?

### Run the app
1. From the project root, run:
   - `npm install`
   - `npm run dev`
2. The frontend is served locally, and the backend runs on its own port.
3. Open the app in the browser and go to the login page.

### Log in
1. Enter a valid username and password from `users.json`.
2. Click the Login button or press Enter.
3. If valid, the app redirects to the board page.
4. If invalid, a generic login failure message appears.

### Create a bug
1. On the board page, click the New Bug button.
2. Fill in the required fields:
   - title
   - severity
   - owner
   - description
3. Save the bug.
4. The board refreshes and the new bug appears.

### Edit a bug
1. Click a bug row on the board.
2. Update fields in the modal.
3. Save the changes.
4. The board shows the updated record.

### Delete a bug
1. Open a bug for editing.
2. Click the Delete button.
3. Confirm the action by using the UI flow in the app.
4. The bug is removed and the board refreshes.

### Search and sort
1. Use the search box in the title bar to filter bugs by title.
2. Click column headers to sort by ID, severity, title, or owner.
3. The board keeps the current search/sort behavior while you work.

### Filter by status
1. Use the Open/Closed buttons above the board.
2. This shows only bugs in the selected state.

### Log out
1. Use the logout control in the title bar.
2. The app returns the user to the login flow.

---

## 5. How do I reset data for this app?

There are two main kinds of reset:

### Reset bug data
The app stores bugs in SQLite at:
- `backend/data/buggyboard.db`

If you want a fresh bug board, delete that database file and restart the backend. The app creates the database again automatically if it is missing.

Typical reset command:
- `rm backend/data/buggyboard.db`
- then run `npm run dev`

Because the database is created automatically, the app will recreate the `bugs` table on startup.

### Reset credential data
Credentials are defined in `users.json` at the project root.
- Edit this file to change usernames or passwords.
- The app reads it directly each time the backend validates a login.

### Reset the entire app state
If you want to start from a clean local state:
1. Delete `backend/data/buggyboard.db`
2. Review or change `users.json` as needed
3. Restart the app with `npm run dev`

There is no migration system here, so this is the intended reset approach for a learning app.

---

## 6. What documentation does this project contain?

This project contains a substantial spec-and-process documentation set under `specs/`.

Main documentation files:
- `README.md` — project overview and quickstart
- `CLAUDE.md` — instructions for Claude coding agents
- `.github/copilot-instructions.md` — instructions for GitHub Copilot
- `specs/constitution.md` — the governing project constitution
- `specs/PROGRESS.md` — feature-by-feature status tracker

Product documentation:
- `specs/product/vision.md` — app purpose and product direction
- `specs/product/glossary.md` — domain terminology
- `specs/product/braindump.md` — product notes and brainstorming

Design documentation:
- `specs/design/README.md` — design overview
- `specs/design/theme.md` — styling and theme guidance

Engineering documentation:
- `specs/engineering/README.md` — engineering overview
- `specs/engineering/tech-stack.md` — stack and constraints
- `specs/engineering/coding-standards.md` — code conventions and architecture rules
- `specs/engineering/development-process.md` — spec-first workflow and process
- `specs/engineering/gherkin-standards.md` — scenario-writing guidance
- `specs/engineering/test-automation-patterns.md` — Playwright testing patterns
- `specs/engineering/api-conventions.md` — API conventions
- `specs/engineering/pipelines.md` — CI/CD conventions

Feature specs:
- `specs/features/01-favicon.md`
- `specs/features/02-user-accounts.md`
- `specs/features/03-login.md`
- `specs/features/04-title-bar.md`
- `specs/features/05-logout.md`
- `specs/features/06-create-bug.md`
- `specs/features/07-bug-board.md`
- `specs/features/08-board-severity.md`
- `specs/features/09-edit-bug.md`
- `specs/features/10-sort-board-columns.md`
- `specs/features/11-search-board.md`
- `specs/features/12-delete-bug.md`
- `specs/features/13-bug-status.md`

The project is intentionally documentation-heavy because it is designed to teach spec-driven development and context engineering for AI-assisted coding.

---

## Quick summary

This app is a small, intentionally simple bug tracker with:
- a React frontend
- an Express backend
- SQLite persistence
- user credentials in `users.json`
- feature specs and engineering docs under `specs/`

It is designed for learning, experimentation, and later Playwright-based test automation exercises.
