# AGENTS Guidelines for This Repository

This repository contains a Next.js application located in the root of this repository. When
working on the project interactively with an agent (e.g. the Codex CLI) please follow
the guidelines below so that the development experience – in particular Hot Module
Replacement (HMR) – continues to work smoothly.

## 1. Use the Development Server, **not** `npm run build`

* **Always use `npm run dev` (or `pnpm dev`, `yarn dev`, etc.)** while iterating on the
  application.  This starts Next.js in development mode with hot-reload enabled.
* **Do _not_ run `npm run build` inside the agent session.**  Running the production
  build command switches the `.next` folder to production assets which disables hot
  reload and can leave the development server in an inconsistent state.  If a
  production build is required, do it outside of the interactive agent workflow.

## 2. Keep Dependencies in Sync

If you add or update dependencies remember to:

1. Update the appropriate lockfile (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`).
2. Re-start the development server so that Next.js picks up the changes.

## 3. Coding Conventions

* Prefer TypeScript (`.tsx`/`.ts`) for new components and utilities.
* Co-locate component-specific styles in the same folder as the component when
  practical.

## 4. Useful Commands Recap

| Command            | Purpose                                            |
| ------------------ | -------------------------------------------------- |
| `npm run dev`      | Start the Next.js dev server with HMR.             |
| `npm run lint`     | Run ESLint checks.                                 |
| `npm run test`     | Execute the test suite (if present).               |
| `npm run build`    | **Production build – _do not run during agent sessions_** |

---

Following these practices ensures that the agent-assisted development workflow stays
fast and dependable.  When in doubt, restart the dev server rather than running the
production build.

## Cursor Cloud specific instructions

This multi-repo environment installs dependencies for this site and a few sibling apps. Rerun install only when a lockfile changes.

- This site uses `corepack pnpm install --frozen-lockfile` (pnpm 9.15.1) and `pnpm dev` (Next.js on port 3000). Typecheck with `pnpm exec tsc --noEmit`. `pnpm lint` runs `next lint`, which Next.js 16 treats as a directory path and fails. There is no `test` script.
- `WorkBuddyGuide`: `npm ci`, `npm test`, and `npm run dev` (VitePress on port 5173).
- `TrendRadar`: `uv sync`, then `uv run python -m mcp_server.server --transport http --host 0.0.0.0 --port 3333` (`/mcp`). The pinned fastmcp 2.12 imports `pydantic._internal._typing_extra.eval_type_backport`, which current pydantic removed. The environment install aliases that private name to `eval_type` inside the project virtualenv so the server can import.
- `awesome-seedance-2-prompts`: `corepack pnpm install --frozen-lockfile`. `pnpm generate` needs `CMS_HOST` and `CMS_API_KEY`.
- QwenPaw is checked out but not installed. Its source install builds the console and pulls a large Python stack, and chatting needs model credentials.

On boot, tmux sessions `agents_md`, `workbuddy`, and `trendradar` start those three servers when the sessions are absent. Logs are `/tmp/cursor-dev-logs/<session>.log`.
