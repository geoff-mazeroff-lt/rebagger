# Design

**Generated:** 2026-04-22

## Architecture Overview

Rebagger is a single Next.js application — no separate backend service. The browser handles all state; the server does nothing beyond serving the Next.js app. CSV parsing runs entirely client-side using Papa Parse (no upload endpoint needed for MVP).

```
Browser
┌─────────────────────────────────────────────────┐
│  React (App Router)                             │
│                                                 │
│  InventoryContext (session state)               │
│  ┌──────────┐  ┌───────────┐  ┌─────────────┐  │
│  │  Import  │→ │ Inventory │→ │   Report    │  │
│  │  screen  │  │  screen   │  │   screen    │  │
│  └──────────┘  └───────────┘  └─────────────┘  │
│       ↑                              ↑          │
│  Papa Parse                    computeMismatches│
│  (CSV → Piece[])               (pure function)  │
└─────────────────────────────────────────────────┘

Next.js dev server (local only)
└── Serves the React app; no API routes for MVP
```

**Data flow:**
1. User selects a CSV file on the Import screen.
2. Papa Parse reads it in the browser and returns a `Piece[]`.
3. `InventoryContext` stores the piece list for the session.
4. Inventory screen reads context; user edits `actual` counts in-place.
5. Report screen calls `computeMismatches()` against context state and renders results.

## Project Structure

```
rebagger/
├── app/
│   ├── layout.tsx              ← Root layout; mounts InventoryProvider
│   ├── globals.css             ← Tailwind base styles
│   ├── page.tsx                ← Import screen (route: /)
│   ├── inventory/
│   │   └── page.tsx            ← Inventory + quantity entry (route: /inventory)
│   └── report/
│       └── page.tsx            ← Mismatch report (route: /report)
├── components/
│   ├── ui/                     ← shadcn/ui generated components (do not edit)
│   ├── import-form.tsx         ← File picker + CSV parse trigger
│   ├── inventory-table.tsx     ← Piece list with editable actual-qty inputs
│   └── report-table.tsx        ← Filtered mismatch list with diff badges
├── lib/
│   ├── types.ts                ← Shared TypeScript types (Piece, Mismatch, etc.)
│   ├── csv.ts                  ← Papa Parse wrapper; CSV → Piece[]
│   ├── inventory-context.tsx   ← React Context + InventoryProvider
│   └── report.ts               ← computeMismatches() pure function
├── __tests__/
│   ├── csv.test.ts             ← Unit tests for CSV parsing + validation
│   └── report.test.ts          ← Unit tests for mismatch computation
├── public/
│   └── sample.csv              ← Example CSV for manual testing
├── biome.json
├── next.config.ts
├── package.json
├── tsconfig.json
├── vitest.config.ts
└── CLAUDE.md
```

## Key Design Decisions

### Decision 1: Client-side CSV parsing (no API route)

- **Options:** Parse in browser (Papa Parse) vs. upload to a Next.js API route and parse server-side.
- **Choice:** Client-side only.
- **Reasoning:** For a local, session-only tool with no persistence, a server round-trip adds complexity with no benefit. Papa Parse is well-tested in the browser. An API route can be introduced later when server-side validation or DB storage is needed.

### Decision 2: React Context for session state

- **Options:** React Context, Zustand, URL state, or prop-drilling from a single root component.
- **Choice:** React Context (`InventoryContext`) mounted in the root layout.
- **Reasoning:** The app has exactly one shared state object (the current set's inventory) that three pages all read. Context is the right tool at this scale — no external library needed. If state complexity grows (multi-bag, multi-set), Zustand is a natural next step.

### Decision 3: Report as a derived computation, not stored state

- **Options:** Store report results in context vs. compute on-render from context state.
- **Choice:** Compute on-render via `computeMismatches(pieces)` pure function.
- **Reasoning:** The report is always a pure function of the piece list. Storing it would create a derived-state synchronization problem. A pure function is trivially testable and always consistent with the current inventory.

### Decision 4: Guard redirects for empty state

- **Options:** Allow direct navigation to `/inventory` or `/report` with empty state vs. redirect to `/` if no inventory is loaded.
- **Choice:** Redirect to `/` when inventory is empty.
- **Reasoning:** Without a CSV import, the inventory and report screens have nothing to show. A redirect is cleaner than rendering an empty/broken state.

## Conventions & Patterns

- **File naming:** kebab-case for all files (`inventory-table.tsx`, `csv.ts`).
- **Components:** Named exports only — no default exports (aids refactoring and tree-shaking).
- **Types:** All domain types defined in `lib/types.ts`; imported from there everywhere.
- **Error handling:** Inline error messages in the UI (e.g., below the file picker). No toast system for MVP.
- **Configuration:** `.env.local` pattern available; no environment variables needed for MVP.
- **Logging:** `console.error` for unexpected errors in development; no logging infrastructure for MVP.

## Data Model

```typescript
// lib/types.ts

type Piece = {
  partNumber: string;  // Lego part number (e.g., "3001")
  name: string;        // Human-readable name (e.g., "Brick 2 x 4")
  color: string;       // Color name (e.g., "Red")
  expected: number;    // Factory-specified quantity
  actual: number;      // User-entered quantity (initialized to 0 on import)
};

type InventoryState = {
  setName: string;     // Derived from CSV filename or a user-provided label
  pieces: Piece[];
};

type Mismatch = {
  piece: Piece;
  difference: number;  // actual - expected; negative = missing, positive = extra
};
```

**Expected CSV format:**

| Column | Description | Example |
|---|---|---|
| `part_number` | Lego part ID | `3001` |
| `name` | Part name | `Brick 2 x 4` |
| `color` | Color name | `Red` |
| `quantity` | Expected count | `4` |

A `sample.csv` demonstrating this format will be included in `public/`.

## API / Interface Design

No API routes for MVP. All logic is client-side.

**Three screens:**

**1. Import (`/`)**
- File picker (click or drag-and-drop) targeting `.csv` files.
- "Import" button parses the file via Papa Parse.
- On success: stores result in `InventoryContext` and navigates to `/inventory`.
- On error: displays an inline validation message (malformed CSV, missing columns).

**2. Inventory (`/inventory`)**
- Table columns: Part Number | Name | Color | Expected | Actual | Status.
- `Actual` column: numeric `<Input>` per row, pre-filled with `0`.
- `Status` column: `Match` (green badge) or `Mismatch` (red badge), updates live as user types.
- "View Report" button → navigates to `/report`.
- "Start Over" link → clears context and returns to `/`.

**3. Report (`/report`)**
- Summary line: "X of Y pieces matched."
- Table of mismatches only: Part Number | Name | Color | Expected | Actual | Difference.
- `Difference` badge: red for missing (negative), amber for extra (positive).
- "Back to Inventory" button.
- "Start Over" link.
