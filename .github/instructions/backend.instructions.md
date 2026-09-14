---
description: "Use when editing or adding Express routes, Lowdb data access, zod schemas, analytics, or backend Vitest tests under backend/."
applyTo: "backend/**/*.ts"
---

# Backend Instructions (`@tsm/backend`)

Express 4 + Lowdb + zod, ESM, Node >= 20.

## Routes

- Register routes in `src/server.ts`; each resource lives in `src/routes/<name>.ts` and exports a factory `xxxRouter(db: DB): Router`.
- Validate every request body and query with `zod`. On failure return `res.status(400).json({ error: parsed.error.flatten() })`.
- Status codes: `201` on create, `200` on read/update, `204` on delete, `404` for missing entities, `400` for validation errors.
- New entities: generate IDs as `` `<prefix>_${nanoid(8)}` `` and `await db.write()` after every mutation.
- When deleting an entity, also clean up dependent rows (see `skills.ts` removing assessments).

## Data layer

- `db.ts` owns the Lowdb instance and the `DB` type. Don't read or write JSON files directly from routes.
- All persisted shapes come from `@tsm/shared`; add new fields there first, then to `seed.json` if needed.
- The dev DB lives at `backend/data/db.json` and is re-seeded from `src/seed.json` when missing. Never edit `db.json` by hand in code changes.

## Analytics

- Pure functions in `src/analytics.ts` operate on plain arrays — keep them framework-free and unit-testable.
- Route handlers in `routes/analytics.ts` are thin adapters: parse params, load from `db.data`, delegate to analytics functions, return JSON.

## Imports

- ESM with `"type": "module"`. Local imports must use the `.js` extension (`from '../db.js'`), shared package as `from '@tsm/shared'`.

## Tests

- Vitest specs go in `backend/test/*.test.ts`. Use `supertest` against the Express app (see existing patterns) rather than starting a real server.
- After changing analytics or routes, run `npm run test -w backend`. Use `npm run test:coverage -w backend` to check coverage; analyze with `node scripts/analyze-coverage.mjs`.
