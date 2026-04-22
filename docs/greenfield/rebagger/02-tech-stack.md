# Tech Stack Decisions

**Generated:** 2026-04-22

## Summary

| Layer | Choice | Runner-up |
|---|---|---|
| Language | TypeScript | — |
| Framework | Next.js (App Router) | Vite + React + Express |
| Database | None (MVP: session-only) | SQLite + Prisma (future) |
| Testing | Vitest + React Testing Library | Jest |
| Build / Package Manager | pnpm | npm |
| Linting & Formatting | Biome | ESLint + Prettier |
| CSS / UI Components | Tailwind CSS + shadcn/ui | Plain CSS modules |
| CSV Parsing | Papa Parse | csv-parse |

## Decision Details

### Language: TypeScript

Full-stack TypeScript throughout — shared types between server (Next.js API routes) and client (React components). Aligns with developer preference and eliminates a class of runtime errors in the piece-inventory data model.

### Framework: Next.js (App Router)

**Options considered:**
1. **Next.js (App Router)** — File upload API routes built in, React UI, single repo, deployable later without refactoring.
2. **Vite + React + Express** — Explicit control, but requires wiring two separate servers and more boilerplate.
3. **SvelteKit** — Lighter, but smaller ecosystem and less natural TypeScript-first fit.

**Recommendation:** Next.js — API routes handle CSV upload cleanly, React handles the interactive quantity-entry UI, and the architecture is already deployment-ready if scope expands.
**User decision:** Next.js (App Router).

### Database: None (MVP)

Session-only state: inventory lives in React state, discarded on close. When persistence is added, Next.js pairs naturally with Prisma + SQLite (local) or PostgreSQL (cloud).

### Testing: Vitest + React Testing Library

**Options considered:**
1. **Vitest** — Vite-native speed, seamless ESM/TypeScript support, modern API.
2. **Jest** — Battle-tested but slower and requires more config for ESM/TypeScript.
3. **Playwright** — E2E testing, overkill for MVP scope.

**Recommendation:** Vitest with React Testing Library for component tests.
**User decision:** Vitest + React Testing Library.

### Build / Package Manager: pnpm

**Options considered:**
1. **pnpm** — Fast installs, efficient disk usage, strict dependency resolution.
2. **npm** — Zero setup, but slower with larger `node_modules`.
3. **bun** — Fastest installs but still maturing; occasional Next.js compatibility gaps.

**Recommendation:** pnpm — de facto standard for serious TypeScript projects.
**User decision:** pnpm.

### Linting & Formatting: Biome

**Options considered:**
1. **Biome** — Single tool for lint + format, near-zero config, very fast.
2. **ESLint + Prettier** — Comprehensive rules but two tools to configure.

**Recommendation:** Biome — greenfield project means no legacy config to migrate; one tool, one config file.
**User decision:** Biome.

## Additional Tooling

- **CSS / UI Components:** Tailwind CSS + shadcn/ui — utility-first styling with polished table, input, and badge components that suit a data-entry/report app. Avoids hand-coding a sortable piece inventory table from scratch.
- **CSV Parsing:** Papa Parse — the gold standard for CSV in browser and Node; handles edge cases without custom parsing logic.
- **Version control:** Git, conventional commits, single `main` branch for MVP.
