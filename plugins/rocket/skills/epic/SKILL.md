---
name: epic
description: Plan a feature as a GitHub epic with ordered sub-issues. Use when the user wants to plan, spec or break down a feature before building ("plan X", "create an epic", "break this into issues").
---

# Epic

Idea → agreed plan → epic issue + ordered sub-issues. GitHub is the memory: the plan lives in issues, not in the context window.

`rk` = `../../scripts/rk` relative to this skill's base directory.

## 1. Clarify (cheap)
- Read only what the feature touches: CLAUDE.md/README + the relevant files. For a wide search, spawn one `rocket:scout` agent with a precise question instead of reading yourself.
- Never ask what the code can answer.
- Ask every open question in ONE batch (AskUserQuestion, ≤4, recommended option first). Skip if the request is already clear.

## 2. Propose
Reply with an outline of ≤15 lines: goal, approach, out of scope, numbered task titles. Wait for approval. Adjust the outline on feedback; don't restart.

## 3. Write the spec
Write `spec.md` in the scratchpad:

```markdown
# <Epic title>
<Goal, approach, out of scope. Short.>

## Task: <imperative title>
<What changes and why, 1–3 lines.>
- [ ] <acceptance criterion>
Files: <key paths, if known>

## Task: <next title>
...
```

Task rules:
- One task = one PR, independently mergeable. Order = execution order (dependencies first).
- 3–10 tasks. More than that → split into two epics.
- No code, no restating the epic. Criteria must be checkable (a test, a command, a visible behavior).
- Epic body headings use `###`; only `## Task:` starts a sub-issue.

## 4. Create
Run `rk create <spec.md> <name>` once, with `<name>` a 1–3 word kebab-case name: it creates `epic/<name>` from dev, and phases become `feat/epic-<name>-<n>`. Reply with its output and the next step: `/rocket:work` on the first issue, ideally in a fresh session (`/clear`).
