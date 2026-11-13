# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) backed by an in-memory store, used as the starter project for the Claude Code course.

## Commands

Requires Node 18.11+ (CI uses Node 22, the recommended version; also verified on Node 26). `node --watch` and `--test-name-pattern` were added in 18.11 and fail on older versions.

- `npm run dev` — start the API with `node --watch` on http://localhost:3000 (`PORT` overrides)
- `npm test` — run all tests with Node's built-in runner (`node --test`)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="POST /users"` — run tests whose name matches
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint then tests on Node 22

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Write tests with `node:test` + `node:assert` + `supertest`, not Jest or Mocha. Import `app` from `../server` and call `request(app)`; never start a real server in tests.
- Route handlers access data only through functions exported from `db/store.js`, never by touching the arrays directly. Add new data operations to the store.
- Return errors as JSON `{ error: "<message>" }` with the matching status code (400 for invalid input, 404 for missing records).
- Use double quotes and keep the `// METHOD /path — description` comment above each route handler.

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), which is what lets tests import it.
- `routes/` holds one `express.Router()` file per resource. A new resource means a new file here plus an `app.use(...)` line in `server.js`.
- `db/store.js` is module-level in-memory state that resets on restart. Because it is shared across all tests in the same process, tests that create users change what later tests see. Don't rely on exact counts or IDs.
- `req.params.id` arrives as a string; routes convert it with `Number()` before calling the store.
