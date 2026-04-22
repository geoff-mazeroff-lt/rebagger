# Refined Problem Statement

**Generated:** 2026-04-22

## Problem & Purpose

**Problem:** When a Lego builder acquires a previously-owned set, they have no easy way to verify whether all pieces are present or whether pieces are sorted into the correct numbered bags. This makes re-bagging sets for proper construction error-prone and tedious.

**Users:** A single hobbyist builder (personal use, no authentication needed). Multi-user support is a future consideration.

**Core workflow:**
1. User launches the app locally and opens it in a browser.
2. User imports a CSV file containing the factory-specified inventory for a Lego set.
3. App displays all expected pieces with their quantities.
4. User records how many of each piece they actually have in their bag(s).
5. User triggers a report that shows any pieces with mismatched quantities (missing or extra).

**Success criteria:** A user can import a set's CSV inventory, record the quantity of each piece they found, and see a clear on-screen list of missing or extra pieces.

## Scope

**In scope for initial build:**
- Local web app (Node.js server, browser UI — not deployed to cloud)
- CSV import: user specifies a local CSV file containing a set's piece inventory
- Inventory view: display all pieces from the imported CSV with expected quantities
- Quantity entry: user sets the actual count found for each piece
- Mismatch report: on-screen view listing pieces where actual ≠ expected
- Single bag per set (MVP simplification)

**Explicitly out of scope (for now):**
- Multi-bag support (pieces sorted into numbered bags)
- Persistence between sessions (DB or local file storage — re-import CSV each run)
- Web scraping to auto-fetch inventory from external sites
- Downloadable/exportable report (CSV, PDF)
- Multi-user support or authentication
- Cloud deployment

## Constraints

- Runs locally only (not deployed)
- Session-only: all state is lost when the app is closed; CSV must be re-imported each session
- Single bag per set for MVP
- No authentication required (single user)

## Data & Integrations

- **Primary data source:** User-provided CSV file containing a Lego set's factory inventory (piece ID, name, quantity, etc.)
- **No external API integrations for MVP** (web scraping of inventory sites is a future enhancement)
- **No data privacy or compliance concerns** — personal hobbyist use, local only

## User Experience

- **App type:** Local web app — user runs a server process and accesses the UI via browser
- **Platform:** Desktop browser (no mobile-specific requirements for MVP)
- **No accessibility requirements specified for MVP**
- **No existing design language to follow** — clean, functional UI

## Operational Context

- **Maintained by:** Solo developer
- **Existing codebase:** Greenfield — no existing infrastructure
- **Hosting:** Local only for MVP; architecture should not preclude future deployment
- **Scale:** Single user, small data sets (hundreds of piece types per Lego set)

## Open Questions Resolved

| Question | Answer |
|---|---|
| Web app, desktop app, or CLI? | Local web app (Node.js + browser) |
| Session-only or persistent between runs? | Session-only for MVP |
| Report format: on-screen or downloadable? | On-screen view only |
| Single user or multi-user? | Single user; no auth needed for MVP |
| Success criteria? | User imports CSV, records quantities, sees mismatch report |
