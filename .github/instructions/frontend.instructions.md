---
description: "Use when editing or adding React components, routes, TanStack Query hooks, API client methods, or frontend Vitest tests under frontend/."
applyTo: "frontend/**/*.{ts,tsx}"
---

# Frontend Instructions (`@tsm/frontend`)

React 18 + Vite + TypeScript, React Router v6, TanStack Query v5.

## Data fetching

- All HTTP goes through `src/api/client.ts`. Add a new method on the `api` object rather than calling `fetch` from components.
- Reads use `useQuery({ queryKey: [...], queryFn: api.xxx })`. Writes use `useMutation` and invalidate the relevant `queryKey` in `onSuccess`.
- Use stable, namespaced query keys matching the resource: `['skills']`, `['engineers', id]`, `['heatmap', teamId]`.

## Components & routes

- Page components live in `src/routes/*.tsx`, registered in `App.tsx`. Reusable UI lives in `src/components/`.
- Components are function components with an explicit return type of `JSX.Element`.
- Prefer co-located `useState` + `useMemo` for view-local state. No Redux, no Zustand.
- Styling via classes in `theme.css` (`card`, `stack`, `toolbar`, `subtitle`, etc.). Don't introduce a new CSS framework.

## Types

- Import domain types from `@tsm/shared` (`Skill`, `Engineer`, `CompetencyLevel`, `COMPETENCY_LEVELS`, …). Don't redeclare them locally.
- Use `Omit<T, 'id'>` for create payloads and `Partial<Omit<T, 'id'>>` for patches, matching the API client.

## Imports

- ESM module resolution: local imports include the `.js` extension (e.g. `from '../api/client.js'`). Shared package is `from '@tsm/shared'`.

## Tests

- Vitest + Testing Library specs live in `frontend/test/`. Global setup is `test/setup.ts`.
- When changing a component's rendered output or a helper in `src/lib/`, add or update the corresponding test before finishing.
- Run `npm run test -w frontend`. Avoid hitting the real backend in tests — mock `api` or `fetch`.
