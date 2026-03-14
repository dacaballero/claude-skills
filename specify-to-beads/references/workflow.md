# Specify-to-Beads: Workflow Reference

## Why This Exists

Specifications created by `/specify` are point-in-time artifacts. They are accurate when written, but as implementation begins and customer reality diverges from assumptions, the spec drifts. The code stays up to date; the spec doesn't.

Beads solves this by replacing the spec as the source of truth — not through a document, but through a **living task graph** in source control. Every task carries an audit trail: what was planned, what changed, what was discovered during implementation, and what was superseded. This is better context than a stale spec.

**The lifecycle:**
1. `/specify` → spec is 100% accurate
2. `/specify-to-beads` → tasks loaded into Beads, spec becomes reference-only
3. Implementation begins → tasks evolve, discoveries get added with `--type discovered-from`
4. Customer feedback → tasks updated or superseded with `bd dep add --type supersedes`
5. Spec deleted → `.beads/` is the truth, git history shows the full evolution

## The-Startup Spec Structure

```
.start/specs/
└── 001-auth-system/
    ├── requirements.md     ← WHAT and WHY
    ├── solution.md         ← HOW (technical design)
    └── plan/
        ├── README.md       ← Phase manifest and ordering
        ├── phase-1.md      ← Setup / Foundation
        ├── phase-2.md      ← Core implementation
        ├── phase-3.md      ← Integration
        └── phase-4.md      ← Testing & Polish
```

Each `phase-N.md` typically contains:
- A `#` title heading
- A summary paragraph
- A checklist of tasks as `- [ ] ...` items
- Optionally: acceptance criteria, notes, dependencies

## Beads Structure After Import

```
Epic: bd-xxxx  "001-auth-system: Add OAuth authentication"
│   label: spec-001
│   comment: "Source spec: .start/specs/001-auth-system/"
│
├── bd-xxxx.1  "Phase 1: Setup & Foundation"
│   label: spec-001, phase-1
│   blocks: (nothing — first phase)
│
├── bd-xxxx.2  "Phase 2: Core Implementation"
│   label: spec-001, phase-2
│   blocks: bd-xxxx.1
│
├── bd-xxxx.3  "Phase 3: Integration"
│   label: spec-001, phase-3
│   blocks: bd-xxxx.2
│
└── bd-xxxx.4  "Phase 4: Testing & Polish"
    label: spec-001, phase-4
    blocks: bd-xxxx.3
```

## Dependency Types in Beads

Use these `bd dep add` types appropriately during and after import:

| Type | When to use |
|---|---|
| `parent-child` | Phase task → Epic |
| `blocks` | Phase N+1 blocks Phase N (sequential phases) |
| `discovered-from` | New task discovered during implementation of another |
| `supersedes` | New approach replaces old task (spec drift) |
| `relates-to` | Cross-spec or cross-feature relationship |

## Handling Spec Drift After Import

When the customer changes requirements or implementation reveals surprises, agents should:

1. **Add discovered work** — never modify closed tasks:
   ```bash
   bd create "Refactor token storage (discovered during phase 2)" \
     -t task -p 1 \
     --json | jq -r '.id' | xargs -I{} bd dep add {} <phase-2-id> --type discovered-from
   ```

2. **Supersede outdated tasks**:
   ```bash
   bd create "Replace JWT with session cookies (customer request)" -t task -p 1 --json
   bd dep add <new-id> <old-id> --type supersedes
   bd close <old-id> --reason "Superseded — customer changed auth approach"
   ```

3. **Never update the spec file** — update the task description in Beads:
   ```bash
   bd update <id> --description "Updated: <new approach>"
   bd comment <id> "Customer feedback 2026-03-15: changed from X to Y"
   ```

## Checking for Duplicate Imports

Before creating anything, check:
```bash
bd list --label spec-<NNN> --json
```

If results are returned, the spec has already been imported. Warn the user and stop. Do not create duplicates.

## Committing Beads Changes

Always remind the user to commit after import:
```bash
git add .beads/
git commit -m "chore: import spec <NNN> into beads"
```

The `.beads/issues.jsonl` file is the portable, git-friendly format that travels with the code. Without committing it, the task graph won't survive context resets or machine changes.

## Spec Retirement

Once Beads is the source of truth, the spec can be retired:

```bash
# Option A: Archive (recommended — keeps history)
mkdir -p .start/specs/archive
mv .start/specs/001-auth-system .start/specs/archive/

# Option B: Delete (clean, commits show bd-xxxx as the living record)
rm -rf .start/specs/001-auth-system
git add -A
git commit -m "chore: retire spec 001 — beads is source of truth"
```

Always leave this decision to the user. Never delete spec files automatically.