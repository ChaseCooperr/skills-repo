---
name: plan-decomp
description: Create comprehensive implementation plans decomposed into atomic tasks with full beads integration (epic → feature → task hierarchy)
---

# Plan Decomposition with Beads Integration

## Overview

Create comprehensive implementation plans with automatic beads issue creation. This skill combines the rigor of `writing-plans` with structured beads hierarchy creation, producing both:
1. A detailed implementation spec document
2. A complete beads issue tree (Epic → Feature → Task)

**Announce at start:** "I'm using the plan-decomp skill to create an implementation plan with beads integration."

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Beads Hierarchy

```
Epic (plan-level)
├── Feature (phase-level)
│   ├── Task (atomic implementation step)
│   ├── Task
│   └── Task
├── Feature
│   ├── Task
│   └── Task
└── Feature
    └── Task
```

## Phase 1: Research & Context Gathering

Before writing the plan:

1. **Understand the problem space** - Read relevant files, understand current state
2. **Identify affected systems** - Map which files/modules will be touched
3. **Note existing patterns** - Match project conventions
4. **Identify test strategy** - Determine how each change will be verified

## Phase 2: Plan Document Creation

### Document Header (REQUIRED)

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---

## Preconditions (do these once before starting implementation)

**Step 1: Create a worktree**

Run:
```bash
git worktree add -b <branch-name> ../<branch-name>
cd ../<branch-name>
```

**Step 2: Sync beads**

Run:
```bash
bd sync
bd ready
```

---
```

### Task Structure (per phase)

Each task follows TDD with atomic steps (2-5 minutes each):

```markdown
## Task N: [Component Name]

**Why:** [1-2 sentences explaining the need]

**Files:**
- Modify: `exact/path/to/file.ext`
- Test: `tests/exact/path/to/test.ext`

### Step 1: Write the failing test

[Exact test code]

### Step 2: Run test to verify it fails

Run: `[exact command]`
Expected: FAIL with "[expected error]"

### Step 3: Implement minimal code

[Exact implementation code]

### Step 4: Run test to verify it passes

Run: `[exact command]`
Expected: PASS

### Step 5: Commit

Run:
```bash
git add [files]
git commit -m "[conventional commit message]"
```

---
```

## Phase 3: Beads Issue Creation

After saving the plan document, create the beads hierarchy.

### Sprint Path Derivation

Before creating the epic, analyze the plan to derive the optimal sprint path:

1. **Identify dependencies** - Which tasks must complete before others can start?
2. **Find the critical path** - The longest chain of dependent tasks (determines minimum duration)
3. **Identify parallel tracks** - Tasks with no mutual dependencies that can run concurrently
4. **Group into sprints** - Batch tasks by:
   - Foundation/setup work first (infrastructure, schemas, interfaces)
   - Core implementation second (main business logic)
   - Integration/polish last (wiring, UI, cleanup)
5. **Populate task IDs after creation** - Update the sprint path with actual beads IDs once tasks are created

### Epic Creation (Plan Level)

Create one epic for the entire plan:

```bash
bd create \
  --type epic \
  --priority 0 \
  --title "[Plan Title]" \
  --description "$(cat <<'EOF'
## Problem
[Clear problem statement from plan Goal]

## Approach
[Architecture summary from plan]

## Scope
[List of phases/features covered]

## Plan Reference
`docs/plans/YYYY-MM-DD-<feature-name>.md`

## Success Criteria
- [High-level criteria derived from plan]

## Sprint Path
The optimal implementation sequence for this epic:

### Critical Path
1. `<task-id>` - [task title] (blocks: <dependent-ids>)
2. `<task-id>` - [task title] (blocks: <dependent-ids>)
3. ...

### Parallel Tracks
Track A (can run concurrently with Track B):
- `<task-id>` - [task title]
- `<task-id>` - [task title]

Track B:
- `<task-id>` - [task title]

### Recommended Order
| Sprint | Tasks | Focus |
|--------|-------|-------|
| 1 | `<task-ids>` | [Foundation/Setup] |
| 2 | `<task-ids>` | [Core Implementation] |
| 3 | `<task-ids>` | [Integration/Polish] |
EOF
)" \
  --label "[relevant-labels]"
```

### Feature Creation (Phase Level)

For each major section/phase in the plan, create a feature:

```bash
bd create \
  --type feature \
  --priority 1 \
  --title "[Phase Title]" \
  --description "$(cat <<'EOF'
## Problem
[Why this phase exists]

## Backend/Frontend References
- [File paths affected]

## Design Notes
[Short-term vs long-term considerations if applicable]

## Implementation Tasks (atomic order)
1. [Task 1 summary]
2. [Task 2 summary]
3. [Task 3 summary]

## Plan Reference
`docs/plans/YYYY-MM-DD-<feature-name>.md` (Task N)

## Acceptance Criteria
- [Specific, testable criteria]
- [Derived from plan steps]
EOF
)" \
  --label "[relevant-labels]"

# Link to epic
bd dep add <feature-id> --depends-on <epic-id>
```

### Task Creation (Atomic Step Level)

For each atomic task within a phase:

```bash
bd create \
  --type task \
  --priority 2 \
  --title "[Atomic task description]" \
  --description "$(cat <<'EOF'
## What
[One sentence describing the task]

## Files
- Modify: `exact/path/to/file.ext`
- Test: `tests/exact/path/to/test.ext`

## Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Verification
Run: `[exact test command]`
Expected: PASS

## Plan Reference
`docs/plans/YYYY-MM-DD-<feature-name>.md` (Task N, Step M)
EOF
)" \
  --label "[relevant-labels]"

# Link to feature
bd dep add <task-id> --depends-on <feature-id>
```

## Issue Quality Standard

Every beads issue MUST include:

| Section | Required For | Content |
|---------|--------------|---------|
| Problem/What | All | Clear statement of what needs doing |
| Backend/File References | Features, Tasks | Exact file paths affected |
| Design Notes | Features | Short-term vs long-term considerations |
| Implementation Tasks | Features | Ordered atomic steps |
| Steps | Tasks | Numbered action list |
| Verification | Tasks | Exact command + expected output |
| Plan Reference | All | Link to plan doc + section |
| Acceptance Criteria | Epic, Features | Testable success conditions |
| Sprint Path | Epic | Critical path, parallel tracks, recommended order |

## Phase 4: Completion

After creating all beads:

```bash
# Sync and show ready issues
bd sync
bd ready
```

### Update Epic Sprint Path

Now that all task IDs exist, update the epic's Sprint Path with actual IDs:

```bash
bd update <epic-id> --description "$(cat <<'EOF'
[... keep existing epic description ...]

## Sprint Path
The optimal implementation sequence for this epic:

### Critical Path
1. `{project-name}-abc` - Create interface (blocks: {project-name}-def, {project-name}-ghi)
2. `{project-name}-def` - Implement service (blocks: {project-name}-jkl)
3. `{project-name}-jkl` - Wire to API

### Parallel Tracks
Track A (Backend):
- `{project-name}-mno` - Add repository method
- `{project-name}-pqr` - Add validation

Track B (Frontend - can run with Track A):
- `{project-name}-stu` - Create component
- `{project-name}-vwx` - Add API client

### Recommended Order
| Sprint | Tasks | Focus |
|--------|-------|-------|
| 1 | `{project-name}-abc` | Foundation |
| 2 | `{project-name}-def`, `{project-name}-mno`, `{project-name}-stu` | Core (parallel) |
| 3 | `{project-name}-jkl`, `{project-name}-pqr`, `{project-name}-vwx` | Integration |
EOF
)"
```

Display the resulting decomposed/open beads to the user.

## Execution Handoff

After completing plan + beads creation:

**"Plan complete and saved to `docs/plans/<filename>.md`.**

**Beads hierarchy created:**
- Epic: `<epic-id>` - [title]
- Features: [count] issues
- Tasks: [count] issues

**Ready issues:**

[output of bd ready]

**First sprint:**

[display task sequence for sprint 1]

**Starting Prompt (copy this in fresh context):**
```
/beads:update <EPIC_ID> - Sprint 1 : <FEATURE_ID> - begin: <FIRST_TASK_ID>
```

**Proceed now (within current context):**

Ready to place first task `<FIRST_TASK_ID>` in_progress? (y/n)

## Remember

- **Exact file paths** - No vague references
- **Complete code** - Not "add validation" but actual code
- **Exact commands** - With expected output
- **TDD discipline** - Test first, always
- **Atomic steps** - 2-5 minutes each
- **Comprehensive beads** - Match {project-name}-9xm quality
- **Proper hierarchy** - Epic → Feature → Task with dependencies
- **Sprint path in epic** - Critical path, parallel tracks, recommended order
- **DRY, YAGNI** - No over-engineering
- **Frequent commits** - One per logical change
