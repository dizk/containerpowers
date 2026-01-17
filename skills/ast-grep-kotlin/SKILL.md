---
name: ast-grep-kotlin
description: Use when refactoring Kotlin code, renaming methods, migrating APIs, or editing code in container-use environments where Edit tool may be unavailable
---

# ast-grep for Kotlin

## Overview

ast-grep performs structural code search and rewrite using Abstract Syntax Trees. Unlike text-based find/replace, it matches code semantically.

**Core insight:** ast-grep provides reliable code modification via bash, independent of editor tooling. Essential in container-use environments where Edit capabilities may be limited.

## When to Use

**Use ast-grep when:**
- Working in container-use environment (Edit tool unavailable/limited)
- Refactoring across multiple files
- Pattern-based changes (all calls to X become Y)
- Want to preview changes before applying

**Use Edit tool when (local filesystem only):**
- Change requires context-specific logic per file
- One-off fix in single file
- Edit tool is available and reliable

## Workflow

```bash
# 1. Check installation
command -v ast-grep || echo "Install: brew install ast-grep"

# 2. Search - verify pattern matches what you expect
ast-grep -p '$X.getUserName()' -l kotlin

# 3. Preview - see diff without modifying
ast-grep -p '$X.getUserName()' -r '$X.fetchName()' -l kotlin

# 4. Apply - only after reviewing preview
ast-grep -p '$X.getUserName()' -r '$X.fetchName()' -l kotlin -U
```

## Pattern Syntax

| Syntax | Matches | Example |
|--------|---------|---------|
| `$VAR` | Single AST node | `$OBJ.method()` matches `user.getName()` |
| `$_` | Non-capturing wildcard | `$_.$M()` matches any receiver |
| `$$$ARGS` | Zero or more nodes | `fun($$$P)` matches any parameter count |

**Key:** `$A == $A` matches `x == x` but NOT `x == y` (same metavar = same value)

## Common Patterns

### Complete Method Rename (definition + call sites)

```bash
# 1. Rename method DEFINITION
ast-grep -p 'fun getUserName()' -r 'fun fetchName()' -l kotlin -U

# 2. Rename CALL SITES with receiver
ast-grep -p '$X.getUserName()' -r '$X.fetchName()' -l kotlin -U

# 3. Rename SELF-CALLS (no receiver)
ast-grep -p 'getUserName()' -r 'fetchName()' -l kotlin -U
```

**All three steps needed for complete rename.**

### Other Patterns

```bash
# Add parameter
ast-grep -p 'doWork($$$E)' -r 'doWork($$$E, ctx)' -l kotlin -U

# Null safety
ast-grep -p '$X.call()' -r '$X?.call()' -l kotlin -U

# Expression body conversion
ast-grep -p 'fun $N($$$P): $T { return $E }' -r 'fun $N($$$P): $T = $E' -l kotlin
```

## Why AST-Aware Matters

Text-based replace with `getUserName` would wrongly match:
- String literals: `"getUserName"`
- Similar methods: `getUserNameFromDb()`
- Comments: `// calls getUserName`

ast-grep pattern `$X.getUserName()` only matches actual method calls.

## Quick Reference

| Task | Command |
|------|---------|
| Search | `ast-grep -p 'PAT' -l kotlin` |
| Preview | `ast-grep -p 'PAT' -r 'REPL' -l kotlin` |
| Apply | `ast-grep -p 'PAT' -r 'REPL' -l kotlin -U` |
| Interactive | `ast-grep -p 'PAT' -r 'REPL' -l kotlin --interactive` |
| Directory | `ast-grep -p 'PAT' -l kotlin src/main/kotlin` |

## Gotchas

**No output = no matches.** If search returns nothing, pattern is wrong. Test with simpler pattern first.

**Forgot method definition.** `$X.method()` only matches calls. Use `fun method()` pattern for definitions.

**Not installed.** Always run `command -v ast-grep` first. In containers: `npm install -g @ast-grep/cli`

**Pattern too specific.** If `fun $N(): String` doesn't match, try `fun $N()` - return type annotation is optional in Kotlin.

## Installation

```bash
# Container (npm usually available)
npm install -g @ast-grep/cli

# macOS
brew install ast-grep

# Cargo
cargo install ast-grep
```
