---
name: container-setup
description: Use when starting work on a project, when tempted to skip containers for "simple" changes, or when container-use is not yet configured
---

# Container Setup

## The Iron Law

```
ALL WORK HAPPENS IN CONTAINERS. NO EXCEPTIONS.
```

Not "risky work." Not "complex work." **All work.**

The 2-line change. The typo fix. The "quick" edit. Everything.

## Why No Exceptions

Containers aren't risk mitigation for dangerous changes. They're the **standard operating mode**.

| Rationalization | Reality |
|-----------------|---------|
| "It's just 2 lines, overkill" | 2-line changes break production constantly. Containers prevent regret. |
| "Unnecessary friction" | 10 seconds to create environment. Cheaper than explaining why you broke things. |
| "Easily reversible" | If you're thinking about reverting, you should've used isolation. |
| "User wants it done quickly" | User wants it done right. Containers are how. |
| "Hazmat suit for houseplant" | No. Seatbelt for every drive, even to the corner store. |

## Prerequisites

- Docker (running)
- Git
- Container Use CLI: `container-use version`

## Installation

```bash
# macOS
brew install dagger/tap/container-use

# Linux/Other
curl -fsSL https://raw.githubusercontent.com/dagger/container-use/main/install.sh | bash
```

## Claude Code Integration

```bash
cd /path/to/repository
claude mcp add container-use -- container-use stdio
```

## Basic Workflow

1. User gives task
2. Work in container (always)
3. Container tracks changes on branch
4. User reviews: `container-use diff {id}`
5. User accepts: `container-use merge {id}`

## How It Works

**For the agent (you):** Container-use MCP tools provide sandboxed file operations and command execution. You work normally — the isolation happens automatically.

**For the user:** CLI commands to review and accept your work:

| CLI Command | Purpose |
|-------------|---------|
| `list` | Show all environments |
| `diff {id}` | Show changes vs base |
| `merge {id}` | Integrate changes |
| `apply {id}` | Stage changes |
| `delete {id}` | Remove environment |
| `watch {id}` | Observe agent in real-time |

When you complete work, tell the user so they can review with `container-use diff` and accept with `container-use merge`.

## Red Flags - STOP

If you're thinking:
- "This is too simple for containers"
- "I'll just edit directly this once"
- "Containers add unnecessary overhead"
- "The user wants speed, not process"

**STOP.** Use containers. No exceptions.
