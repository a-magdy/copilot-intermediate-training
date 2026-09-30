# Team Skills Matrix — Project Instructions

Always-on guidance for contributions across this monorepo.

## Instruction files

Path-scoped rules live in `.github/instructions/`. **Before editing files in these areas, read the matching file first** — it contains conventions not repeated here:

- `backend/**/*.ts` → [backend.instructions.md](instructions/backend.instructions.md)
- `frontend/**/*.{ts,tsx}` → [frontend.instructions.md](instructions/frontend.instructions.md)

## Architecture

- npm workspaces monorepo: `shared/`, `backend/`, `frontend/`, plus `e2e/` Playwright specs. See [README.md](../README.md) for setup, scripts, and the architecture diagram.
- `@tsm/shared` is the single source of truth for domain types and enums. Add new shared types there before referencing them from `backend/` or `frontend/`.
- Backend (Express + Lowdb) exposes `/api/*` on `:47821`. Frontend (Vite + React) runs on `:51734`, consumes the API via TanStack Query, and proxies `/api` in dev.
- Data is stored in JSON via Lowdb at `backend/data/db.json` (dev) and `backend/data/db.e2e.json` (Playwright). Treat both as ephemeral.

## Conventions

- **Language**: TypeScript everywhere. ESM modules — local imports use the `.js` extension (e.g. `import { api } from '../api/client.js';`) even when the source is `.ts`.
- **Strict typing**: no `any`. Prefer `unknown` + narrowing, or extend types in `shared/src/types.ts`.
- **Validation at boundaries**: validate untrusted input (HTTP bodies, query strings) with `zod`. Do not re-validate already-typed internal calls.
- **IDs**: use `nanoid(8)` with a short prefix (`skl_`, `eng_`, `tea_`, etc.), matching existing route code.
- **Errors at HTTP boundary**: return `{ error: ... }` with the appropriate status (`400` for validation, `404` for missing, `204` for delete). Do not throw to the client.
- **No comments unless the *why* is non-obvious.** Don't restate what the code does.

## Tests

- Unit tests with Vitest, colocated under each workspace's `test/` folder.
- E2E tests with Playwright under `e2e/tests/`; they boot prod builds and an isolated DB.
- When changing analytics, routes, or shared types, update or add the corresponding test before declaring done.

## Workflow

- Prefer editing existing files over creating new ones.
- Run `npm run lint` and `npm test` before proposing a change is complete. After changing backend routes, analytics, or shared types, run the workspace-scoped suite (`npm run test -w backend`) and confirm it passes.
- Don't commit generated files (`dist/`, `coverage/`, `test-results/`) or the local `db.json`.
