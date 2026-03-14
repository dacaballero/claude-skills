    # specify-to-beads

A Claude Code skill that converts a [the-startup](https://github.com/rsmdt/the-startup) `/specify` output into a [Beads](https://github.com/steveyegge/beads) epic and task graph — making the task history the source of truth instead of the spec file.

## Why

Specifications are accurate when written. By the time you've iterated with the codebase and the customer, they're not. The code is up to date; the spec isn't.

This skill bridges the two tools: after `/specify` produces a structured plan, `/specify-to-beads` loads those phases into Beads as a dependency-aware, git-backed task graph. From that point on, Beads is the living record — tracking what was planned, what changed, what was discovered, and what was superseded. The spec file can eventually be deleted.

```
/specify     →  .start/specs/001-auth/plan/phase-*.md   (point-in-time)
                          ↓
/specify-to-beads  →  .beads/ epic + tasks              (living record)
                          ↓
/implement   →  agents work off bd ready --json
                          ↓
Customer feedback  →  discovered-from / supersedes tasks added
                          ↓
rm -rf .start/specs/001-auth/   →  Beads is the truth
```

## Prerequisites

- [Claude Code](https://claude.ai/code) with the [the-startup plugin](https://github.com/rsmdt/the-startup) installed
- [Beads CLI](https://github.com/steveyegge/beads) installed and `bd init` run in your project

```bash
# Install Beads
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash

# Initialize in your project
cd your-project && bd init
```

## Installation

```bash
# Add this marketplace
/plugin marketplace add <your-github-handle>/specify-to-beads

# Install the skill
/plugin install specify-to-beads
```

## Usage

After running `/specify` in the-startup:

```bash
/specify-to-beads 001
```

This will:
1. Read `.start/specs/001-*/plan/phase-*.md`
2. Create one Beads epic for the spec
3. Create one task per phase, with sequential `blocks` dependencies
4. Annotate everything with source file paths
5. Print the dependency tree
6. Remind you to commit `.beads/`

Then work off Beads instead of the spec:

```bash
bd ready --json          # what's unblocked right now
bd update <id> --claim   # atomically claim a task
bd close <id> --reason "Done"
```

## CLAUDE.md Integration

Add the snippet from [`CLAUDE.md-snippet.md`](specify-to-beads/CLAUDE.md-snippet.md) to your project's `CLAUDE.md`. This tells Claude to suggest running `/specify-to-beads` automatically after every `/specify` completes — before moving to `/implement`.

## Spec Drift

When customer reality diverges from the original spec, don't update the spec — update the task graph:

```bash
# New work discovered during implementation
bd create "Add refresh token rotation (discovered)" -t task -p 1 --json | \
  jq -r '.id' | xargs -I{} bd dep add {} <phase-id> --type discovered-from

# Customer changed the approach
bd create "Replace JWT with sessions (customer request)" -t task -p 1 --json
bd dep add <new-id> <old-id> --type supersedes
bd close <old-id> --reason "Superseded — customer changed auth approach"
```

This preserves the full audit trail of how the project evolved, which is better context for future agents than a stale spec.

## Repo Structure

```
specify-to-beads/
├── SKILL.md                   # Skill entrypoint and execution steps
├── references/
│   └── workflow.md            # Full philosophy, edge cases, Beads types
└── CLAUDE.md-snippet.md       # Paste into your project's CLAUDE.md
```

## License

MIT