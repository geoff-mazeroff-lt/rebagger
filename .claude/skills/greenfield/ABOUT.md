# Greenfield — Claude Code Skill

An orchestrator skill that walks you through scaffolding a new project from a
problem statement to a locally running application. Each step produces a
markdown artifact and pauses for human verification before proceeding.

## The 6 Steps

| #  | Step                  | Goal                                                  |
|----|-----------------------|-------------------------------------------------------|
| 1  | Refine Problem        | Ensure the problem statement is complete               |
| 2  | Tech Stack            | Select technologies with guided trade-off discussion  |
| 3  | Design                | Architecture, structure, conventions, patterns         |
| 4  | Scaffolding Checklist | Confirm every scaffold decision before generating code |
| 5  | Scaffold              | Generate code, verify it builds and runs locally       |
| 6  | Deployment Readiness  | Tailored checklist for future production deployment    |

## Installation

Copy the `greenfield` folder into your project's `.claude/skills/` directory:

```bash
# From your project root (or an empty repo)
mkdir -p .claude/skills
cp -r path/to/greenfield .claude/skills/greenfield
```

Your project structure should look like:

```
your-project/
├── .claude/
│   └── skills/
│       └── greenfield/
│           ├── SKILL.md
│           ├── ABOUT.md
│           └── templates/
│               ├── problem-statement.md
│               └── step-outputs.md
└── ...
```

Alternatively, install as a personal skill (available in all projects):

```bash
cp -r path/to/greenfield ~/.claude/skills/greenfield
```

## Usage

### Start a new project

```
/greenfield expense-tracker
```

You'll be prompted to provide your problem statement, then guided through
each step.

### Navigate the workflow

| Command    | Action                                               |
|------------|------------------------------------------------------|
| `next`     | Proceed to the next step                             |
| `redo`     | Re-run the current step                              |
| `back`     | Return to the previous step                          |
| `status`   | Show progress across all steps                       |

### Resume a workflow

Run `/greenfield expense-tracker` again. The skill detects existing artifact
files and resumes where you left off.

## Artifacts

All outputs are saved to `docs/greenfield/<project-slug>/`:

```
docs/greenfield/<project-slug>/
├── 00-problem-statement.md       ← Original input (never modified)
├── 01-refined-problem.md         ← Refined after template comparison
├── 02-tech-stack.md              ← Technology decisions with trade-offs
├── 03-design.md                  ← Architecture, structure, conventions
├── 04-scaffolding-checklist.md   ← Confirmed choices for scaffold
├── 05-scaffold-log.md            ← What was generated and verification
└── 06-deployment-readiness.md    ← Tailored checklist for production
```

## Customization

### Custom scaffolding checklists

The skill generates checklists dynamically based on the chosen tech stack.
For teams with established standards, you can add curated checklist files
to the `templates/` directory (e.g., `templates/checklist-react.md`,
`templates/checklist-dotnet.md`) and reference them from SKILL.md to
override the dynamic generation for specific stacks.

### Problem statement template

Edit `templates/problem-statement.md` to add or remove sections based on
your team's needs. For example, a healthcare team might add a dedicated
"Regulatory Requirements" section.
