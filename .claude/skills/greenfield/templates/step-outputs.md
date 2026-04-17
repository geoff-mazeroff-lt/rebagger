# Greenfield — Step Output Templates

These templates define the expected format for each step's output.
The orchestrator should follow these structures when saving artifacts.

---

## 00-problem-statement.md

```markdown
# Problem Statement (Original)

**Captured:** [YYYY-MM-DD]

[The user's original problem statement, copied verbatim. This file is never
modified after initial capture.]
```

---

## 01-refined-problem.md

```markdown
# Refined Problem Statement

**Generated:** [YYYY-MM-DD]

## Problem & Purpose

**Problem:** [What problem this solves and who experiences it]
**Users:** [Who will use this and their roles/personas]
**Core workflow:** [What a user does from start to finish]
**Success criteria:** [How you'll know it's working]

## Scope

**In scope for initial build:**
- [Capability 1]
- [Capability 2]

**Explicitly out of scope (for now):**
- [Deferred capability 1]
- [Deferred capability 2]

## Constraints

- [Hard constraint 1]
- [Hard constraint 2]

## Data & Integrations

- [Data sources, external APIs, compliance needs]

## User Experience

- [App type, platform targets, accessibility needs]

## Operational Context

- [Who maintains it, expected scale, hosting preferences]

## Open Questions Resolved

[Questions that were surfaced during refinement and the user's answers]
```

---

## 02-tech-stack.md

```markdown
# Tech Stack Decisions

**Generated:** [YYYY-MM-DD]

## Summary

| Layer          | Choice              | Runner-up           |
|----------------|---------------------|---------------------|
| Language       | [Choice]            | [Alternative]       |
| Framework      | [Choice]            | [Alternative]       |
| Database       | [Choice]            | [Alternative]       |
| Testing        | [Choice]            | [Alternative]       |
| Build/Package  | [Choice]            | [Alternative]       |

## Decision Details

### Language: [Choice]

**Options considered:**
1. **[Option A]** — [Trade-offs, strengths, weaknesses]
2. **[Option B]** — [Trade-offs, strengths, weaknesses]
3. **[Option C]** — [Trade-offs, strengths, weaknesses]

**Recommendation:** [Option] because [reasoning tied to problem statement]
**User decision:** [What they chose and why, if different from recommendation]

### Framework: [Choice]

...

### Database: [Choice]

...

### Testing: [Choice]

...

### Build / Package Manager: [Choice]

...

## Additional Tooling

- **Linting:** [Choice and why]
- **Formatting:** [Choice and why]
- **Version control:** [Git conventions, branching strategy]
```

---

## 03-design.md

```markdown
# Design

**Generated:** [YYYY-MM-DD]

## Architecture Overview

[High-level description of the system architecture: what the major components
are and how they interact. Include an ASCII diagram if helpful.]

## Project Structure

```
project-root/
├── [directory/]        ← [purpose]
├── [directory/]        ← [purpose]
├── [config files]      ← [purpose]
└── ...
```

## Key Design Decisions

### Decision 1: [Short title]

- **Options:** [A vs. B]
- **Choice:** [What was chosen]
- **Reasoning:** [Why, tied to problem constraints]

### Decision 2: [Short title]

...

## Conventions & Patterns

- **Naming:** [File naming, variable naming conventions]
- **Error handling:** [Strategy]
- **Logging:** [Approach]
- **Configuration:** [How config is managed: env vars, config files, etc.]

## Data Model

[If applicable: key entities, their relationships, and storage approach]

## API / Interface Design

[If applicable: key endpoints, CLI commands, or UI screens]
```

---

## 04-scaffolding-checklist.md

```markdown
# Scaffolding Checklist

**Generated:** [YYYY-MM-DD]
**Tech Stack:** [Summary from 02-tech-stack.md]

## Project Initialization

- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]
- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]

## Code Structure

- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]

## Development Tooling

- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]

## Testing Setup

- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]

## Documentation

- [ ] [Item] — **Choice:** [selected option] | Alternatives: [others]

## Verification

- [ ] Project builds without errors
- [ ] All tooling runs (linter, formatter, test runner)
- [ ] App starts and runs locally
- [ ] A sample test passes

---

*Each item above includes a recommended choice. The user confirms or overrides
each before scaffolding begins.*
```

---

## 05-scaffold-log.md

```markdown
# Scaffold Log

**Generated:** [YYYY-MM-DD]

## Actions Taken

### Project Initialization
- [What was created/configured]
- [Commands run]

### Code Structure
- [Directories and files created]
- [Why this structure was chosen]

### Development Tooling
- [Tools configured]
- [Config files created]

### Testing Setup
- [Test framework configured]
- [Sample test created]

### Documentation
- [README generated]
- [Other docs created]

## Verification Results

- [ ] **Build:** [pass/fail — command and output]
- [ ] **Lint:** [pass/fail — command and output]
- [ ] **Tests:** [pass/fail — command and output]
- [ ] **Run locally:** [pass/fail — command and output]

## Files Created

[Complete list of files created during scaffolding]
```

---

## 06-deployment-readiness.md

```markdown
# Deployment Readiness Checklist

**Generated:** [YYYY-MM-DD]
**Tech Stack:** [Summary]
**Target Environment:** [If known, e.g., AWS, Vercel, self-hosted]

## CI/CD Pipeline

- [ ] [Item with tailored guidance]
- [ ] [Item with tailored guidance]

## Infrastructure & Hosting

- [ ] [Item with tailored guidance]
- [ ] [Item with tailored guidance]

## Security

- [ ] [Item with tailored guidance]
- [ ] [Item with tailored guidance]

## Authentication & Authorization

- [ ] [Item with tailored guidance]

## Monitoring & Observability

- [ ] [Item with tailored guidance]

## Data & Backups

- [ ] [Item with tailored guidance]

## Scaling Considerations

- [ ] [Item with tailored guidance]

## Domain & Networking

- [ ] [Item with tailored guidance]

## Compliance & Legal

- [ ] [Item with tailored guidance, if applicable]

---

*This checklist is not acted on during scaffolding. It is a reference for
the developer to address as they prepare for production deployment.*
```
