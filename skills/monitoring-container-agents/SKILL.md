---
name: monitoring-container-agents
description: Use when you have dispatched work to containers and the user needs to check progress, detect stuck agents, or verify work before merging
---

# Monitoring Container Agents

## Overview

When agents work in containers, the user has full visibility into their actions. Container logs show what actually happened, not what agents claim happened.

**Core principle:** Trust but verify. Review container output before accepting work.

**Note:** These are CLI commands for the **user** to run from their terminal, not MCP actions for agents.

## Monitoring Commands (for user)

| CLI Command | Purpose | When to Use |
|-------------|---------|-------------|
| `container-use list` | Show all environments | See what's running |
| `container-use watch {id}` | Real-time observation | See work in progress |
| `container-use log {id}` | Command history | Understand what was done |
| `container-use diff {id}` | Code changes vs base | Review before merge |
| `container-use terminal {id}` | Interactive shell | Diagnose stuck agents |

## When to Monitor

Tell the user which command to run:

**Progress check:**
```
To see all running environments:
container-use list
```

**Real-time observation:**
```
To watch work in progress:
container-use watch {id}
```

**Pre-merge review:**
```
To review changes before merging:
container-use diff {id}
container-use log {id}
```

**Stuck detection:**
```
To diagnose a stuck agent:
container-use terminal {id}
```

## Detecting Stuck Agents

Signs an agent may be stuck:
- No new commits in `log` output
- `watch` shows repetitive behavior
- Same error appearing repeatedly

**When stuck:**
1. `container-use terminal {id}` — inspect state directly
2. Check for common issues:
   - Missing dependencies
   - Test failures blocking progress
   - Waiting on external resource
3. Either fix directly or `container-use delete {id}` and retry

## Pre-Merge Verification

**Before accepting work with `merge` or `apply`:**

1. Review changes: `container-use diff {id}`
2. Check history: `container-use log {id}`
3. Verify tests: Check log for test output
4. Spot check: For critical changes, use `terminal` to inspect

## Integration

**Called by:**
- **dispatching-parallel-agents** — Monitor multiple containers
- **verification-before-completion** — Verify before claiming success

**Pairs with:**
- **container-setup** — Understand the environment
- **finishing-a-development-branch** — After verification, decide merge/discard
