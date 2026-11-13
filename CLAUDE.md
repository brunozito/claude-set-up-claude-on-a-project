# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) backed by an in-memory store, used as the starter project for the Claude Code course.

## Commands

- `npm run dev` — start the API on http://localhost:3000 (`PORT` overrides)
- `npm test` — run all tests with Node's built-in runner (`node --test`)
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint then tests

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules.
- Write tests with `node:test` + `node:assert` + `supertest`, not Jest or Mocha. Import `app` from `../server` and call `request(app)`; never start a real server in tests.
- Route handlers access data only through functions exported from `db/store.js`, never by touching the arrays directly. Add new data operations to the store.
- Return errors as JSON `{ error: "<message>" }` with the matching status code (400 for invalid input, 404 for missing records).
- Use double quotes and keep the `// METHOD /path — description` comment above each route handler.

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`.
- `routes/` holds one `express.Router()` file per resource. A new resource means a new file here plus an `app.use(...)` line in `server.js`.
- `db/store.js` is the in-memory data store; it resets on restart.
