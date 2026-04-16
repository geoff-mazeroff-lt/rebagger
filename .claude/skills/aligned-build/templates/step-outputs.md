# Aligned Build — Step Output Templates

These templates define the expected format for each step's output.
The orchestrator should follow these structures when saving artifacts.

---

## 00-ticket.md

```markdown
# Ticket: [Title]

**Source:** [Link or reference]
**Date captured:** [YYYY-MM-DD]

## Description

[Full ticket text, copied verbatim]

## Acceptance Criteria

[If available from the ticket]
```

---

## 01-questions.md

```markdown
# Step 1 — Questions

**Generated:** [YYYY-MM-DD]
**Ticket:** [Title]

## Ambiguities & Unstated Assumptions

- [ ] [Question about something unclear in the ticket]
- [ ] [Question about an assumption that needs confirmation]

## Edge Cases

- [ ] [What happens when X?]
- [ ] [How should the system behave if Y?]

## Integration Points

- [ ] [How does this interact with existing system Z?]
- [ ] [Are there upstream/downstream dependencies?]

## Technical Unknowns

- [ ] [Is there an existing pattern for this?]
- [ ] [What constraints exist in the current architecture?]

## Answers

[This section is filled in by the user or during discussion]
```

---

## 02-research.md

```markdown
# Step 2 — Research

**Generated:** [YYYY-MM-DD]

## Codebase Overview

[Brief description of the relevant parts of the codebase]

## Relevant Modules & Files

### [Module/Area Name]

- **Location:** `path/to/files`
- **Purpose:** [What it does]
- **Key functions/classes:** [List them]
- **Current behavior:** [What happens today]

### [Next Module/Area]

...

## Patterns & Conventions

- [Pattern 1: description of how the codebase does X]
- [Pattern 2: description of how the codebase does Y]

## Data Flow

[How data currently moves through the relevant parts of the system]

## Test Coverage

[What tests exist for the relevant areas, how they're structured]
```

---

## 03-design.md

```markdown
# Step 3 — Design Discussion

**Generated:** [YYYY-MM-DD]
**Ticket:** [Title]

## Current State

[What exists today — summarized from research]

## Desired State

[What the ticket asks for — the end goal]

## Gap Analysis

[What needs to change to get from current to desired]

## Patterns to Follow

[Existing patterns in the codebase that this work should respect]

## Design Decisions

### Decision 1: [Short title]

- **Options:** [A vs. B vs. C]
- **Recommendation:** [Your pick]
- **Reasoning:** [Why]
- **Trade-offs:** [What you give up]

### Decision 2: [Short title]

...

## Open Questions for the User

[Anything that requires human judgment]

## Risks & Concerns

[Things that could go wrong or need extra attention]
```

---

## 04-structure.md

```markdown
# Step 4 — Structure Outline

**Generated:** [YYYY-MM-DD]

## Vertical Slices

### Slice 1: [Name]

- **Outcome:** [What's true when this is done]
- **Layers touched:** [DB / Service / API / Frontend / Tests]
- **Verification:** [How to confirm it works]

### Slice 2: [Name]

- **Outcome:** [What's true when this is done]
- **Layers touched:** [DB / Service / API / Frontend / Tests]
- **Depends on:** Slice 1
- **Verification:** [How to confirm it works]

### Slice 3: [Name]

...

## Ordering Rationale

[Why this order was chosen — what builds on what]
```

---

## 05-plan.md

```markdown
# Step 5 — Implementation Plan

**Generated:** [YYYY-MM-DD]

## Slice 1: [Name]

### Task 1.1: [Description]

- **File:** `path/to/file.ts`
- **Action:** [Create / Modify / Delete]
- **Details:** [Specific functions, classes, or changes]
- **Tests:**
  - [Test case 1: description]
  - [Test case 2: description]

### Task 1.2: [Description]

...

### Slice 1 Verification

- [ ] [Run specific test command]
- [ ] [Manual verification step]

---

## Slice 2: [Name]

...
```

---

## 06-worktree.md

```markdown
# Step 6 — Work Tree

**Generated:** [YYYY-MM-DD]

## Dependency Map

```
slice-1-name ──→ slice-3-name ──→ slice-5-name
      │
      └──→ (parallel) slice-2-name
                       slice-4-name
```

## Execution Plan

### SEQUENTIAL — Must be done in order

| Order | Branch Name          | Depends On     | Tasks     |
|-------|----------------------|----------------|-----------|
| 1     | slice-1-name         | —              | 1.1, 1.2  |
| 2     | slice-3-name         | slice-1-name   | 3.1, 3.2  |
| 3     | slice-5-name         | slice-3-name   | 5.1, 5.2  |

### PARALLEL — Can be done simultaneously

| Branch Name          | Tasks     | Can start after |
|----------------------|-----------|-----------------|
| slice-2-name         | 2.1, 2.2  | slice-1-name    |
| slice-4-name         | 4.1, 4.2  | slice-1-name    |

## Branch Details

### slice-1-name

- **Create:** `claude --worktree slice-1-name`
- **Tasks:** [List from plan]
- **Verify:** [Tests to run, behavior to check]

### slice-2-name

...
```

---

## 07-implementation-log.md

```markdown
# Step 7 — Implementation Log

**Started:** [YYYY-MM-DD]

## Branch: slice-1-name

- **Status:** ✅ Complete
- **Started:** [timestamp]
- **Completed:** [timestamp]
- **Notes:** [Any deviations from plan, issues encountered]
- **Tests:** All passing

## Branch: slice-2-name

- **Status:** 🔄 In progress
- **Started:** [timestamp]
- **Notes:** [Progress notes]

## Branch: slice-3-name

- **Status:** ⏳ Waiting (depends on slice-1-name)
```

---

## 08-pull-request.md

```markdown
# Step 8 — Pull Request

## Summary

[What was built and why. Reference the ticket.]

## Changes by Slice

### Slice 1: [Name]
- [High-level change description]
- [Files affected]

### Slice 2: [Name]
...

## Testing

- [What automated tests were added]
- [How to manually verify]

## Design Decisions

See `docs/aligned-build/03-design.md` for full context.

Key decisions:
- [Decision 1: brief summary]
- [Decision 2: brief summary]

## Checklist

- [ ] All tests passing
- [ ] No linting errors
- [ ] PR description is clear
- [ ] Human review completed (non-optional)
```
