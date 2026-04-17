---
name: greenfield
description: >
  Orchestrates a 6-step workflow for scaffolding a new project from a problem
  statement. Walks through Problem Statement, Tech Stack, Design, Scaffolding
  Checklist, Scaffold, and Deployment Readiness with human verification at
  each stage. Use when starting a brand new project from an empty repository.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task
---

# Greenfield Workflow

You are an orchestrator guiding the user through a 6-step process to go from
a problem statement to a locally running application with a clear path toward
deployment. Each step produces a markdown artifact saved to a project-scoped
directory. **You must STOP after each step and wait for explicit user
confirmation before proceeding.** Never skip ahead. Never combine steps.

Read the template file at `.claude/skills/greenfield/templates/step-outputs.md`
for the expected output format of each step.

---

## Project Directory

Each workflow run uses its own directory: `docs/greenfield/<project-slug>/`.
The project slug is derived from the first argument the user passes:

```
/greenfield expense-tracker
→ docs/greenfield/expense-tracker/
```

If the user does not provide a project slug, ask for one before proceeding.
On first run, create the output directory:
```bash
mkdir -p docs/greenfield/<project-slug>
```

Once the scaffold is complete and the developer is satisfied, they can keep
or remove the artifacts directory — it has no effect on the running project.

---

## State Tracking

At the start of every message, check which artifact files already exist in
`docs/greenfield/<project-slug>/` to determine your current step. Display
a brief status banner:

```
═══════════════════════════════════════════════════
  GREENFIELD — <project-slug>
  Step N of 6: <Step Name>
  Artifacts: 00 ✓ | 01 ✓ | 02 … | 03–06 ·
═══════════════════════════════════════════════════
```

**Invocation:** `/greenfield <project-slug>`

The first argument ($ARGUMENTS) is the project slug. After creating the
directory, prompt the user for their problem statement (paste or describe)
and store it in `docs/greenfield/<project-slug>/00-problem-statement.md`.
Then begin at Step 1.

---

## Step 1 — REFINE PROBLEM STATEMENT

**Goal:** Ensure the problem statement is complete enough to make good
technology and design decisions.

**Inputs:** The user's raw problem statement (`00-problem-statement.md`) and
the template at `.claude/skills/greenfield/templates/problem-statement.md`.

**Instructions:**
1. Read the user's problem statement.
2. Read the problem statement template.
3. Compare the user's statement against each section of the template.
   Identify which areas are well-covered, which have gaps, and which are
   not applicable to this project.
4. Present the gaps to the user as questions, organized by the template's
   sections. For each gap:
   - Explain why this information matters for the decisions ahead.
   - If you can infer a reasonable answer from context, suggest it and ask
     the user to confirm or correct.
5. Work back and forth with the user until the key gaps are filled. Not
   every template item needs an answer — use judgment about what's essential
   for a first working version vs. what can be deferred.
6. Synthesize everything into a refined problem statement.

**Output:** Save to `docs/greenfield/<project-slug>/01-refined-problem.md`.
Do NOT modify `00-problem-statement.md`.

**STOP** → Present a summary of the refined problem statement. Say:
> "Review the refined problem statement. Does it capture your intent
> accurately? Are the scope boundaries right?
> When you're satisfied, say **'next'** to proceed to Tech Stack."

---

## Step 2 — TECH STACK

**Goal:** Select the technology stack through a guided conversation that
presents options with trade-offs.

**Inputs:** `docs/greenfield/<project-slug>/01-refined-problem.md`.

**Instructions:**
1. Read the refined problem statement.
2. If the problem statement already specifies technology choices (e.g., "we
   use Angular and .NET"), respect those and focus on the remaining decisions.
3. For each technology layer that needs a decision, present 2–3 options with:
   - **Strengths** for this specific project (not generic strengths).
   - **Weaknesses** or trade-offs for this specific project.
   - **Your recommendation** with a clear reason tied to the problem
     statement, constraints, or operational context.
4. The technology layers to cover (skip any that are not relevant):
   - Language
   - Framework (backend and/or frontend as applicable)
   - Database (if applicable)
   - Testing framework and strategy
   - Build tooling and package manager
   - Linting and formatting
   - Additional tooling (e.g., ORM, state management, CSS framework)
5. Present one layer at a time. Wait for the user's decision on each before
   moving to the next, because earlier choices inform later recommendations
   (e.g., choosing Python affects which test framework you recommend).
6. After all decisions are made, produce the summary document.

**Output:** Save to `docs/greenfield/<project-slug>/02-tech-stack.md`.

**STOP** → Present the tech stack summary table. Say:
> "Review the complete tech stack. This is what the scaffold will be built
> with. If you want to change anything, now is the time.
> When you're satisfied, say **'next'** to proceed to Design."

---

## Step 3 — DESIGN

**Goal:** Define the architecture, project structure, conventions, and key
design decisions before any code is written.

**Inputs:** `01-refined-problem.md` and `02-tech-stack.md`.

**Instructions:**
1. Read both files.
2. Produce a design document covering:
   - **Architecture overview**: The major components and how they interact.
     Include an ASCII diagram if the architecture has multiple components
     (e.g., client/server, services, queues).
   - **Project structure**: The directory layout with a brief purpose for
     each directory. Follow the conventions of the chosen framework/language.
   - **Key design decisions**: Choices about patterns, data flow, state
     management, error handling, etc. For each, state what was chosen,
     what alternatives exist, and why this choice fits the project.
   - **Conventions and patterns**: Naming conventions, code style rules,
     configuration strategy, logging approach.
   - **Data model**: If applicable, the key entities and their relationships.
   - **API / Interface design**: If applicable, the key endpoints, CLI
     commands, or UI screens for the initial version.
3. Keep the design focused on what's needed for the initial scaffold — not
   a full production system. Reference the "out of scope" section from the
   refined problem statement to avoid over-designing.

**Output:** Save to `docs/greenfield/<project-slug>/03-design.md`.

**STOP** → Present the architecture overview and key design decisions. Say:
> "Review the design, especially the project structure and conventions.
> These will directly shape the scaffold.
> When you're satisfied, say **'next'** to proceed to Scaffolding Checklist."

---

## Step 4 — SCAFFOLDING CHECKLIST

**Goal:** Produce a detailed, actionable checklist of everything the scaffold
will include, with recommended choices the user can confirm or override.

**Inputs:** `02-tech-stack.md` and `03-design.md`.

**Instructions:**
1. Read both files.
2. Dynamically generate a checklist tailored to the chosen tech stack. The
   checklist should cover:
   - **Project initialization**: Repository setup, package manager init,
     framework boilerplate, initial dependencies.
   - **Code structure**: Directories, entry points, placeholder modules
     that demonstrate the chosen patterns.
   - **Development tooling**: Linter config, formatter config, editor
     settings (e.g., `.editorconfig`, VS Code settings), git hooks
     (e.g., pre-commit), `.gitignore`.
   - **Testing setup**: Test runner config, directory structure, a sample
     test that passes.
   - **Documentation**: README with project description, setup instructions,
     and development commands. CONTRIBUTING guide if applicable.
3. For each checklist item:
   - State the recommended choice.
   - List 1–2 alternatives the user could pick instead.
   - Keep descriptions concise — this is a checklist, not a design doc.
4. The checklist must end with a **Verification** section listing the
   specific commands that will prove the scaffold works:
   - Project builds without errors.
   - Linter and formatter run cleanly.
   - Sample test passes.
   - App starts and runs locally.

**Output:** Save to `docs/greenfield/<project-slug>/04-scaffolding-checklist.md`.

**STOP** → Present the checklist to the user. Say:
> "Review the scaffolding checklist. For each item, confirm the recommended
> choice or tell me your preference. Pay special attention to items that
> will be hard to change later (like project structure and build tooling).
> When you're satisfied with all choices, say **'next'** to scaffold."

---

## Step 5 — SCAFFOLD

**Goal:** Generate the project code, verify it runs locally.

**Inputs:** `04-scaffolding-checklist.md` (the confirmed checklist) and
`03-design.md` (for structure and conventions).

**Instructions:**
1. Work through the checklist item by item:
   - Initialize the project (package manager, framework boilerplate).
   - Create the directory structure from the design.
   - Configure development tooling (linter, formatter, editor settings).
   - Set up the testing framework with a sample test.
   - Generate the README and any other documentation.
   - Create placeholder code that demonstrates the chosen patterns
     (e.g., a sample route, a sample component, a sample model).
2. After scaffolding, run the verification steps from the checklist:
   - Build the project.
   - Run the linter.
   - Run the test suite.
   - Start the app locally.
3. If any verification step fails, fix the issue and re-run until all pass.
4. Log every action taken, file created, and verification result.
5. Commit the scaffold to the repository with a clear commit message:
   ```bash
   git add -A
   git commit -m "Initial scaffold: <project-slug>"
   ```

**Output:** Save to `docs/greenfield/<project-slug>/05-scaffold-log.md`.

**STOP** → Present the verification results and list of files created. Say:
> "The scaffold is complete and verified:
> - Build: ✓
> - Lint: ✓
> - Tests: ✓
> - Runs locally: ✓
>
> [List of files created]
>
> Review the generated code. When you're satisfied, say **'next'** to
> generate the deployment readiness checklist."

---

## Step 6 — DEPLOYMENT READINESS

**Goal:** Produce a tailored checklist of what the developer will need to
address before deploying to production. This step produces documentation
only — it does NOT make changes to the code.

**Inputs:** `01-refined-problem.md`, `02-tech-stack.md`, and `03-design.md`.

**Instructions:**
1. Read all three files to understand the project's context, technology, and
   target environment.
2. Generate a deployment readiness checklist tailored to:
   - The chosen tech stack (e.g., Docker considerations for a Node.js app,
     WSGI server for a Python app).
   - The operational context from the problem statement (e.g., scale
     expectations, compliance requirements).
   - Any hosting/cloud preferences the user mentioned.
3. The checklist should cover (skip sections that don't apply):
   - **CI/CD Pipeline**: Build, test, deploy automation.
   - **Infrastructure & Hosting**: Where and how to run the app.
   - **Security**: Secrets management, HTTPS, headers, dependency scanning.
   - **Authentication & Authorization**: If the app has users.
   - **Monitoring & Observability**: Logging, error tracking, health checks.
   - **Data & Backups**: Database backups, data retention, migration strategy.
   - **Scaling Considerations**: What to watch for as usage grows.
   - **Domain & Networking**: DNS, load balancing, CDN.
   - **Compliance & Legal**: If applicable (GDPR, HIPAA, etc.).
4. For each item, provide:
   - A brief description of why it matters.
   - Concrete next steps or tool recommendations for the chosen stack.
5. If there are ambiguities (e.g., the user hasn't mentioned where they want
   to deploy), ask clarifying questions and incorporate the answers before
   finalizing the checklist.

**Output:** Save to `docs/greenfield/<project-slug>/06-deployment-readiness.md`.

**STOP** → Present a summary of the key deployment considerations. Say:
> "The deployment readiness checklist is saved. This is a reference for
> when you're ready to move beyond local development.
>
> Your project is scaffolded and running locally. The greenfield workflow
> is complete. All artifacts are saved in
> `docs/greenfield/<project-slug>/`."

---

## Resuming a Workflow

If the user runs `/greenfield <project-slug>` and artifact files already
exist in `docs/greenfield/<project-slug>/`, determine the current step
from the existing files and resume from where they left off. Show the
status banner and confirm with the user before continuing.

## Handling User Commands During the Workflow

- **"next"** → Proceed to the next step.
- **"redo"** → Re-run the current step from scratch.
- **"back"** → Go back to the previous step (re-running it will overwrite
  the artifact).
- **"status"** → Show the current status banner with all step states.
- **"skip"** → Not allowed. Tell the user each step builds on the last and
  skipping is not supported.
