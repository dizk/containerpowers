---
name: finishing-a-development-branch
description: Use when implementation is complete in a container environment, when tempted to skip diff review, or when ready to integrate container work
---

# Finishing a Development Branch

## Overview

Guide completion of container work by presenting clear options and handling chosen workflow.

**Core principle:** Verify tests → Review diff → Present options → Execute choice.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this work."

## The Iron Law

```
ALWAYS REVIEW DIFF BEFORE PRESENTING OPTIONS. NO EXCEPTIONS.
```

Not "if user wants to see it." Not "if time permits." **Always.**

| Rationalization | Reality |
|-----------------|---------|
| "User wants to move on quickly" | User wants to know what they're merging. Show diff. |
| "Tests passed, should be good" | Passing tests ≠ correct implementation. Review changes. |
| "Adds steps user might not want" | User DOES want to see changes before committing to them. |
| "Showing diff slows things down" | Merging wrong code slows things down more. |

## The Process

### Step 1: Verify Tests

**Before anything else, verify tests pass in the container.**

**If tests fail:** Stop. Fix tests first.

**If tests pass:** Continue to Step 2.

### Step 2: Review Diff (MANDATORY)

Tell the user to review changes:

```
I've completed the implementation. Please review the changes:

container-use diff {id}
```

**Summarize what you changed.** Describe the modifications you made so the user knows what to expect in the diff.

This step is NOT optional. Don't skip it because "user wants speed."

### Step 3: Present Options

Present exactly these 4 options:

```
Implementation complete. What would you like to do?

1. Merge changes (keeps commit history)
2. Apply changes (stages as new commit)
3. Keep the environment (I'll handle it later)
4. Discard this work

Which option?
```

### Step 4: Execute Choice

Tell the user which command to run based on their choice:

#### Option 1: Merge

```
To merge with commit history preserved:
container-use merge {id}
```

#### Option 2: Apply

```
To stage changes for a new commit:
container-use apply {id}
```

#### Option 3: Keep As-Is

Report: "Keeping environment {id}. Use `container-use list` to see it later."

#### Option 4: Discard

**Confirm first:**
```
This will permanently delete environment {id} and all changes.
Type 'discard' to confirm, then run:
container-use delete {id}
```

Wait for typed confirmation before providing the delete command.

## Quick Reference

| Option | Command | Keeps History | Environment |
|--------|---------|---------------|-------------|
| 1. Merge | `merge {id}` | Yes | Deleted |
| 2. Apply | `apply {id}` | No (new commit) | Deleted |
| 3. Keep | None | N/A | Preserved |
| 4. Discard | `delete {id}` | N/A | Deleted |

## Red Flags - STOP

If you're thinking:
- "User wants to wrap up, skip the diff"
- "Tests passed so changes are fine"
- "Showing diff adds unnecessary steps"
- "I'll just merge directly"

**STOP.** Review the diff. Always.

## Common Mistakes

**Skipping diff review**
- **Problem:** User merges changes they didn't actually review
- **Fix:** ALWAYS run and summarize `container-use diff` before options

**Skipping test verification**
- **Problem:** Merge broken code
- **Fix:** Always verify tests before reviewing diff

**No confirmation for discard**
- **Problem:** Accidentally delete work
- **Fix:** Require typed "discard" confirmation

## Integration

**Called by:**
- **subagent-driven-development** (Step 7) - After all tasks complete
- **executing-plans** (Step 5) - After all batches complete
- **dispatching-parallel-agents** - After containers complete

**Pairs with:**
- **monitoring-container-agents** - Review work before finishing
- **verification-before-completion** - Ensure tests actually pass
