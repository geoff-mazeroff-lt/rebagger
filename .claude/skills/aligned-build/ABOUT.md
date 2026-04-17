# Aligned Build — Claude Code Skill

An orchestrator skill that walks you through an 8-step workflow for taking a
ticket from questions to a reviewed pull request. Each step produces a markdown
artifact and pauses for human verification before proceeding.

## Acknolwedgement

The idea for this skill came from Dexter Horthy's talk where he describes the
QRSPI framework. https://www.youtube.com/watch?v=YwZR6tc7qYg

## The 8 Steps

| #  | Step       | Phase     | Goal                                           |
|----|------------|-----------|------------------------------------------------|
| 1  | Question   | Alignment | Surface what the agent doesn't know            |
| 2  | Research   | Alignment | Factual record of the codebase (no planning)   |
| 3  | Design     | Alignment | Current state → desired state, key decisions   |
| 4  | Structure  | Alignment | Vertical slices with test criteria              |
| 5  | Plan       | Alignment | Full implementation details per slice           |
| 6  | Work Tree  | Execution | Branches with parallel/sequential dependencies |
| 7  | Implement  | Execution | Agents write code, one branch at a time        |
| 8  | PR         | Execution | Human reviews (non-optional)                   |

## Installation

Copy the `aligned-build` folder into your project's `.claude/skills/` directory:

```bash
# From your project root
mkdir -p .claude/skills
cp -r path/to/aligned-build .claude/skills/aligned-build
```

Your project structure should look like:

```
your-project/
├── .claude/
│   └── skills/
│       └── aligned-build/
│           ├── SKILL.md                    # Orchestrator instructions
│           └── templates/
│               └── step-outputs.md         # Output format templates
├── src/
└── ...
```

Alternatively, install it as a personal skill (available in all projects):

```bash
cp -r path/to/aligned-build ~/.claude/skills/aligned-build
```

## Usage

### Start a new workflow

```
/aligned-build [paste your ticket or describe the feature]
```

Or start without a ticket and provide it when prompted:

```
/aligned-build
```

### Navigate the workflow

| Command    | Action                                               |
|------------|------------------------------------------------------|
| `next`     | Proceed to the next step                             |
| `redo`     | Re-run the current step                              |
| `back`     | Return to the previous step                          |
| `status`   | Show progress across all steps                       |

### Resume a workflow

Run `/aligned-build` again in the same project. The skill detects existing
artifact files in `docs/aligned-build/` and resumes where you left off.

### Start fresh

Delete the artifacts directory and run the skill again:

```bash
rm -rf docs/aligned-build
```

## Key Design Decisions

### Why separate sessions matter

Steps 1–5 are designed to be run as separate conceptual contexts. The skill
enforces this most critically in Step 2 (Research), where it delegates to a
subagent that only receives the questions — not the original ticket. This
prevents the agent from jumping to solutions instead of investigating.

For maximum context isolation, you can also run each alignment step as a
fully separate Claude Code session. The artifact files in `docs/aligned-build/`
carry all the context forward.

### Why vertical slices

Horizontal plans (all DB, then all service, then all API) are the default
mode for most LLMs. Vertical slices force each unit of work to be independently
testable and deployable, which means you catch integration issues early rather
than at the end.

### Why human checkpoints

Every step pauses for human review. The agent will not proceed until you
explicitly say "next." This keeps you in control of the design and catches
misunderstandings before they compound across steps.

### Parallel vs. sequential execution

Step 6 explicitly categorizes branches as parallel or sequential. This lets
you spin up multiple Claude Code worktree sessions for independent branches
while maintaining correct ordering for dependent work.

## Artifacts

All outputs are saved to `docs/aligned-build/` in your project:

```
docs/aligned-build/
├── 00-ticket.md
├── 01-questions.md
├── 02-research.md
├── 03-design.md
├── 04-structure.md
├── 05-plan.md
├── 06-worktree.md
├── 07-implementation-log.md
└── 08-pull-request.md
```

These serve as an audit trail of the entire decision-making process, from
initial questions through final PR.

## Extending the Skill

### Adding Agent Teams for parallel execution

If you have access to Claude Code's experimental Agent Teams feature, you can
enhance Step 7 by replacing manual worktree sessions with coordinated agent
teams. Enable the feature:

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

Then modify the Step 7 instructions in `SKILL.md` to spawn teammates for
parallel branches instead of asking the user to open separate sessions.

### Customizing output templates

Edit `templates/step-outputs.md` to match your team's documentation standards,
PR templates, or project conventions.
