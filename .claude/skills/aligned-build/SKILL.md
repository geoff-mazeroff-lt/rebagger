---
name: aligned-build
description: >
  Orchestrates an 8-step aligned build workflow for implementing features from
  tickets. Walks through Question, Research, Design, Structure, Plan, Work Tree,
  Implement, and Pull Request steps with human verification at each stage.
  Use when starting a new feature, ticket, or user story from scratch.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task
---

# Aligned Build Workflow

You are an orchestrator guiding the user through an 8-step build process.
Each step produces a markdown artifact saved to a feature-scoped directory.
**You must STOP after each step and wait for explicit user confirmation before
proceeding.** Never skip ahead. Never combine steps.

Read the template file at `.claude/skills/aligned-build/templates/step-outputs.md`
for the expected output format of each step.

---

## Feature Directory

Each workflow run uses its own directory: `docs/aligned-build/<feature-slug>/`.
The feature slug is derived from the first argument the user passes:

```
/aligned-build change-address
→ docs/aligned-build/change-address/
```

If the user does not provide a feature slug, ask for one before proceeding.
On first run, create the output directory:
```bash
mkdir -p docs/aligned-build/<feature-slug>
```

Once a PR merges, the developer can delete the feature directory. Old artifacts
have no effect on future runs since each feature gets its own directory.

---

## Branching Model

This workflow uses a single feature branch with slice branches for execution:

```
main
 └── feature/<feature-slug>              ← Alignment (Steps 1–5) happens here
      ├── <feature-slug>/slice-1-name    ← worktree branch (from feature branch)
      ├── <feature-slug>/slice-2-name    ← worktree branch (parallel)
      └── <feature-slug>/slice-3-name    ← worktree branch (sequential)
```

**Key rules:**
- Steps 1–5 (Alignment) all happen on `feature/<feature-slug>`. Artifacts are
  committed and pushed to this branch as they are produced. Once an artifact
  is finalized, push immediately (`git push -u origin feature/<feature-slug>`)
  so work is not lost.
- Step 6 (Work Tree) designs the slice branches but does not create them yet.
- Step 7 (Implement) creates worktree branches FROM the feature branch
  (not from `main`). Each worktree starts with the full artifact set and plan.
- Agents in worktrees write code only. They do NOT modify artifact files in
  `docs/aligned-build/<feature-slug>/`.
- Slice branches merge back INTO `feature/<feature-slug>` (not into `main`),
  following the dependency order from Step 6.
- Step 8 (PR) creates ONE pull request from `feature/<feature-slug>` into `main`.

At the start of the workflow, create the feature branch if it doesn't exist:
```bash
git checkout -b feature/<feature-slug>
```

---

## State Tracking

At the start of every message, check which artifact files already exist in
`docs/aligned-build/<feature-slug>/` to determine your current step. Display
a brief status banner:

```
═══════════════════════════════════════════════════
  ALIGNED BUILD — <feature-slug>
  Step N of 8: <Step Name>
  Branch: feature/<feature-slug>
  Artifacts: 01 ✓ | 02 ✓ | 03 … | 04–08 ·
═══════════════════════════════════════════════════
```

**Invocation:** `/aligned-build <feature-slug>`

The first argument ($ARGUMENTS) is the feature slug. After creating the
directory and feature branch, prompt the user for the ticket text (paste or
file reference) and store it in
`docs/aligned-build/<feature-slug>/00-ticket.md`. Then begin at Step 1.

---

## Step 1 — QUESTION

**Goal:** Identify what you don't know before you can implement this ticket.

**Inputs:** The ticket (from `00-ticket.md`) and the codebase.

**Instructions:**
1. Read the ticket thoroughly.
2. Explore the codebase to understand its structure, conventions, and the areas
   the ticket will touch.
3. Produce a list of targeted questions — things you genuinely need answered
   before you can proceed. Focus on ambiguities, unstated assumptions, edge
   cases, and integration points.
4. Do NOT plan. Do NOT suggest solutions. Do NOT make recommendations.
   Only surface gaps and unknowns.

**Output:** Save to `docs/aligned-build/<feature-slug>/01-questions.md`.

**STOP** → Present the questions to the user. Say:
> "Review these questions. Edit the file directly or answer them here.
> When you're satisfied, say **'next'** to proceed to Research."

---

## Step 2 — RESEARCH

**Goal:** Produce a factual record of what the code does today, grounded in
the questions from Step 1.

**Critical constraint:** You must NOT reference the ticket or its goals during
this step. You are investigating the codebase to answer questions, not to plan
a solution.

**Instructions:**
1. Delegate this step to a subagent (using Task tool) with ONLY the contents
   of `docs/aligned-build/<feature-slug>/01-questions.md` as input. Do NOT
   pass the ticket text or `00-ticket.md` to the subagent.
2. The subagent should explore the codebase and produce a factual record:
   what exists, how it works, what patterns are used, what the current
   behavior is.
3. No planning. No recommendations. No opinions. Just facts.
4. The subagent should work interactively with the user: start by presenting
   its open questions and a proposed outline, then write the research after
   the user confirms the direction.

**Output:** Save to `docs/aligned-build/<feature-slug>/02-research.md`.

**STOP** → Present a summary of the research to the user. Say:
> "Review the research document. Does it accurately capture how the code
> works today? When you're satisfied, say **'next'** to proceed to Design."

---

## Step 3 — DESIGN DISCUSSION

**Goal:** Synthesize a design document that answers "where are we going?"

**Inputs:** `02-research.md` (what exists) and `00-ticket.md` (what we want).

**Instructions:**
1. Read both files.
2. Produce a ~200-line markdown "brain dump" covering:
   - **Current state**: What exists today (drawn from research).
   - **Desired state**: What the ticket asks for.
   - **Patterns to follow**: Existing conventions and patterns in the codebase
     that the implementation should respect.
   - **Design decisions**: Key choices to make, with your reasoning and
     trade-offs. Call out anything the user should weigh in on.
3. This is a thinking document, not a plan. Focus on the "what" and "why,"
   not the "how."

**Output:** Save to `docs/aligned-build/<feature-slug>/03-design.md`.

**STOP** → Present the key design decisions to the user. Say:
> "Review the design document, especially the design decisions section.
> Flag anything you disagree with or want to discuss.
> When you're satisfied, say **'next'** to proceed to Structure."

---

## Step 4 — STRUCTURE OUTLINE

**Goal:** Define "how we get there" by breaking the design into ordered tasks.

**Inputs:** `03-design.md`.

**Instructions:**
1. Break the work into tasks organized as **vertical slices**. Each slice
   should touch all relevant layers end-to-end (database, service, API,
   frontend, tests) — NOT horizontal layers (all DB first, then all service,
   etc.).
2. For each slice, describe:
   - What it accomplishes (user-visible or developer-visible outcome).
   - Which layers/files it touches.
   - How to verify it works (specific test or manual check).
3. Order the slices so that each one builds on the last and the system is
   in a working state after every slice.
4. This is about ordering and scope, NOT exact code to write.

**Output:** Save to `docs/aligned-build/<feature-slug>/04-structure.md`.

**STOP** → Present the task breakdown to the user. Say:
> "Review the structure. Check that the slices are vertical (not horizontal),
> the ordering makes sense, and nothing is missing.
> When you're satisfied, say **'next'** to proceed to Plan."

---

## Step 5 — PLAN

**Goal:** Expand the structure into full implementation details.

**Inputs:** `04-structure.md` and `03-design.md` (for reference).

**Instructions:**
1. For each task in the structure outline, specify:
   - Exact files to create or modify.
   - Functions, classes, or modules to add or change.
   - Specific test cases to write.
   - Any migration or configuration changes.
2. The plan should be detailed enough that an agent (or a developer) could
   implement each task without needing to make architectural decisions.
3. Keep the vertical-slice ordering from the structure.

**Output:** Save to `docs/aligned-build/<feature-slug>/05-plan.md`.

**STOP** → Present a summary of the plan to the user. Say:
> "Review the full plan. This is the last checkpoint before execution begins.
> When you're satisfied, say **'next'** to proceed to Work Tree."

---

## Step 6 — WORK TREE

**Goal:** Organize the plan into a hierarchy where each branch maps to a
testable unit of work, and clearly identify execution dependencies.

**Inputs:** `docs/aligned-build/<feature-slug>/05-plan.md`.

**Instructions:**
1. Confirm you are on the `feature/<feature-slug>` branch and all artifacts
   from Steps 1–5 are committed and pushed to the remote.
2. Group the plan's tasks into slice branches. Each branch should be:
   - A testable, independently mergeable unit of work.
   - Named with the feature slug prefix:
     `<feature-slug>/slice-1-user-model`, `<feature-slug>/slice-2-auth-api`.
3. **Explicitly categorize each branch as SEQUENTIAL or PARALLEL:**
   - **SEQUENTIAL** branches depend on work from a prior branch and must be
     implemented in order. State which branch(es) they depend on.
   - **PARALLEL** branches have no dependencies on other branches and can be
     implemented simultaneously.
4. Present a visual dependency map showing the execution order:
   ```
   feature/<feature-slug>  (base for all slice branches)
    ├── <feature-slug>/slice-1-name ──→ <feature-slug>/slice-3-name
    │
    └── (parallel after slice-1)
         <feature-slug>/slice-2-name
         <feature-slug>/slice-4-name
   ```
5. For each branch, list:
   - The tasks from the plan it includes.
   - The worktree creation command (branching FROM the feature branch):
     `claude --worktree <feature-slug>/slice-N-name`
   - The verification criteria (tests to run, behavior to check).
6. Note: Worktree branches are created FROM `feature/<feature-slug>`, so
   every worktree starts with the full artifact set and plan. Agents in
   worktrees write code only — they must NOT modify artifact files in
   `docs/aligned-build/<feature-slug>/`.

**Output:** Save to `docs/aligned-build/<feature-slug>/06-worktree.md`.

**STOP** → Present the dependency map and branch list to the user. Say:
> "Review the work tree. Check that parallel vs. sequential branches are
> correctly identified, and the verification criteria are clear.
> When you're ready to start implementation, say **'next'**."

---

## Step 7 — IMPLEMENT

**Goal:** Execute the plan by creating worktree branches from the feature
branch and implementing each slice.

**Instructions:**
1. Confirm you are on `feature/<feature-slug>` with all artifacts committed and pushed to the remote.
2. Present the execution order from Step 6, organized into waves:
   - **Wave 1:** All branches with no dependencies (can run in parallel).
   - **Wave 2:** Branches whose dependencies are satisfied after Wave 1.
   - And so on until all branches are covered.
3. For each wave, provide the user with:
   - The worktree creation commands (branching from the feature branch):
     `claude --worktree <feature-slug>/slice-N-name`
   - A summary of what to implement in each worktree session.
   - The verification steps to run when done.
   - A reminder: "Write code only. Do not modify files in
     `docs/aligned-build/<feature-slug>/`."
4. **Sequential branches** (within this lead session):
   - Create the worktree from `feature/<feature-slug>`.
   - Implement the slice: write code, write tests, run tests, fix failures.
   - Commit the working code to the slice branch.
   - Merge the slice branch back into `feature/<feature-slug>`:
     ```bash
     git checkout feature/<feature-slug>
     git merge <feature-slug>/slice-N-name
     ```
   - Then proceed to the next sequential branch (which now starts from the
     updated feature branch).
5. **Parallel branches** (user opens separate Claude Code sessions):
   - Tell the user to open new sessions for each parallel branch.
   - Parallel branches can be created and worked on simultaneously.
   - Once a parallel branch is complete, the user merges it back:
     ```bash
     git checkout feature/<feature-slug>
     git merge <feature-slug>/slice-N-name
     ```
6. After each merge, update the implementation log on the feature branch.
7. Clean up completed worktrees:
   ```bash
   git worktree remove .claude/worktrees/<feature-slug>/slice-N-name
   ```

**Output:** Maintain `docs/aligned-build/<feature-slug>/07-implementation-log.md`
on the feature branch (not in worktrees).

**After each branch completes, STOP** → Say:
> "Branch `<slice-name>` is complete, tests pass, and it has been merged
> back into `feature/<feature-slug>`.
> [Show remaining branches and their status]
> Say **'next'** to continue to the next branch, or open parallel branches
> in separate worktree sessions."

**After ALL branches are complete, STOP** → Say:
> "All slice branches are merged into `feature/<feature-slug>`.
> Run the full test suite on the feature branch to verify everything
> works together. Say **'next'** to proceed to Pull Request."

---

## Step 8 — PULL REQUEST

**Goal:** Create a single PR from the feature branch into `main` for human review.

**Instructions:**
1. Confirm you are on `feature/<feature-slug>` and all slice branches have
   been merged in.
2. Run the full test suite to verify the integrated result.
3. Draft a pull request description that includes:
   - **Summary**: What was built and why (reference the ticket).
   - **Changes**: High-level list of what changed, organized by slice.
   - **Testing**: What was tested and how to verify.
   - **Design decisions**: Key choices made (reference
     `docs/aligned-build/<feature-slug>/03-design.md`).
4. Create the PR from `feature/<feature-slug>` into `main`:
   ```bash
   gh pr create --base main --head feature/<feature-slug> \
     --title "<ticket title>" --body-file docs/aligned-build/<feature-slug>/08-pull-request.md
   ```
   If `gh` is not available, present the draft for the user to create manually.

**Output:** Save the PR description to `docs/aligned-build/<feature-slug>/08-pull-request.md`.

**STOP** → Say:
> "The PR from `feature/<feature-slug>` into `main` is ready for human
> review. This step is not optional — a human must review and approve
> the code before it merges.
>
> Once the PR is merged, you can clean up:
> ```
> rm -rf docs/aligned-build/<feature-slug>
> ```
>
> The aligned build workflow is complete."

---

## Resuming a Workflow

If the user runs `/aligned-build <feature-slug>` and artifact files already
exist in `docs/aligned-build/<feature-slug>/`, determine the current step
from the existing files and resume from where they left off. Check out
`feature/<feature-slug>` if it exists. Show the status banner and confirm
with the user before continuing.

## Handling User Commands During the Workflow

- **"next"** → Proceed to the next step.
- **"redo"** → Re-run the current step from scratch.
- **"back"** → Go back to the previous step (re-running it will overwrite
  the artifact).
- **"status"** → Show the current status banner with all step states.
- **"skip"** → Not allowed. Tell the user each step builds on the last and
  skipping is not supported.
