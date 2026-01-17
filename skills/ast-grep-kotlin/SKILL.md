---
name: ast-grep-kotlin
description: Use when editing Kotlin code in container-use environments where the Edit tool is unavailable
---

# ast-grep for Kotlin

## Overview

ast-grep performs structural code search and rewrite using Abstract Syntax Trees via bash commands.

**Why this matters:** In container-use environments, the Edit tool may not be available. ast-grep gives you powerful code modification through bash alone.

## When to Use

**Use ast-grep when:**
- Edit tool is unavailable (container-use environments)
- Need to preview all changes before applying
- Want AST-aware matching (won't hit strings/comments)

**Use Edit tool when available:**
- Edit tool works fine for refactoring with `replace_all`
- ast-grep is not about "efficiency" - it's about working without Edit

## Workflow

```bash
# 1. Check/install (container-use)
command -v ast-grep || npm install -g @ast-grep/cli

# 2. Search - verify pattern matches
ast-grep -p '$X.getUserName()' -l kotlin

# 3. Preview - see diff without modifying
ast-grep -p '$X.getUserName()' -r '$X.fetchName()' -l kotlin

# 4. Apply
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

### Method Rename (handles any parameter count)

Use `$$$PARAMS` to match methods regardless of parameter count:

```bash
# Interface/class method definitions (any arity)
ast-grep -p 'fun query($$$PARAMS)' -r 'fun fetch($$$PARAMS)' -l kotlin -U

# Override methods (implementations)
ast-grep -p 'override fun query($$$PARAMS)' -r 'override fun fetch($$$PARAMS)' -l kotlin -U

# Call sites (any argument count)
ast-grep -p '$X.query($$$ARGS)' -r '$X.fetch($$$ARGS)' -l kotlin -U
```

This single pattern handles `query()`, `query(a)`, `query(a, b)`, etc.

### Simple Method Rename (no parameters)

```bash
# 1. Definition
ast-grep -p 'fun getName()' -r 'fun fetchName()' -l kotlin -U

# 2. Calls with receiver
ast-grep -p '$X.getName()' -r '$X.fetchName()' -l kotlin -U

# 3. Self-calls (no receiver)
ast-grep -p 'getName()' -r 'fetchName()' -l kotlin -U
```

### Other Patterns

```bash
# Add parameter to all calls
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

**Forgot method definition.** `$X.method()` only matches calls. Use `fun method($$$P)` pattern for definitions.

**Forgot override methods.** `fun method()` doesn't match `override fun method()`. Run both patterns.

**Not installed.** Always run `command -v ast-grep` first. In containers: `npm install -g @ast-grep/cli`

**Pattern too specific.** If `fun $N(): String` doesn't match, try `fun $N()` - return type annotation is optional in Kotlin.

**Similar method names.** Pattern `$X.find()` won't accidentally match `$X.findById()` - AST matching is precise.

## Installation

```bash
# Container (npm usually available)
npm install -g @ast-grep/cli

# macOS
brew install ast-grep

# Cargo
cargo install ast-grep
```
