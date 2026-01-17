---
name: capturing-learnings
description: Use when finishing a development branch, debugging session, or feature implementation - before merging or closing work
---

# Capturing Learnings

## Overview

**Capture learnings BEFORE closing work, not after.** "I'll document later" becomes "never documented." Context evaporates within hours.

**Core principle:** The 15 minutes spent capturing now saves hours of rediscovery later. Do it while context is fresh, before the merge.

## When to Use

- Just finished debugging a non-trivial issue
- Completed a feature with unexpected discoveries
- Found patterns, gotchas, or techniques worth preserving
- About to merge a PR after significant work

**Do NOT skip because:**
- "I'll remember it" - You won't. Details fade within days.
- "The code is self-documenting" - Code shows WHAT, not WHY or the diagnostic journey.
- "I'll document tomorrow" - Tomorrow has its own priorities.
- "Just need a quick note for later" - Quick notes rot. Capture properly now.

## What to Capture

**Ask:** What would have saved me time if I'd known it at the start?

| Category | Examples | Where to Put It |
|----------|----------|-----------------|
| **Gotcha** | API quirk, silent failure mode, timing issue | CLAUDE.md or relevant skill |
| **Pattern** | Technique that worked well, testing approach | New or existing skill |
| **Debugging insight** | Root cause analysis, diagnostic steps | systematic-debugging or new skill |
| **Configuration** | Settings that solved the problem | CLAUDE.md or docs/ |

## Capture Format

Keep it structured and scannable:

```markdown
## [Short descriptive title]

**Context:** What were you doing when you learned this?

**The learning:** What's the key insight? (1-3 sentences)

**Why it matters:** What goes wrong without this knowledge?

**Example:** Code snippet or specific case (if applicable)
```

## Placement Decision

```
Is this broadly reusable across projects?
├─ YES → Create or update a skill (use superpowers:writing-skills)
└─ NO → Is it project-specific?
    ├─ YES → Add to CLAUDE.md
    └─ NO → Add to docs/ with clear filename
```

## The Discipline

**Before merging/closing:**
1. Spend 10-20 minutes capturing while context is fresh
2. Choose placement (skill, CLAUDE.md, or docs/)
3. Write in structured format
4. Commit the learning WITH the work, not separately

**Red flags - STOP and capture first:**
- "Let me just merge this and document later"
- "I'll create a TODO to document"
- "This is seared into my memory"
- "The PR description covers it"
- One-liner that "checks the box" but provides no real value

**Quality check before closing:** Would this help someone who hits the same issue in 3 months? If they'd still need to dig through code or ask questions, your capture is insufficient.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Deferring to "later" | Later never comes. Capture NOW. |
| Unstructured brain dump | Use the format. Future you needs scannable content. |
| Only capturing the fix | Capture the diagnostic journey and WHY, not just WHAT. |
| Staging without committing | Commit learnings WITH the work. Staged files get lost. |
| Checkbox capture ("API uses non-standard auth") | Include WHAT, WHERE, HOW, and WHY. One-liners don't prevent rediscovery. |

## Connection to Compound Engineering

Each captured learning makes future work easier:
- Patterns become skills that guide future implementations
- Gotchas prevent repeated debugging sessions
- Diagnostic insights accelerate future troubleshooting

This is how individual work compounds into team knowledge.
