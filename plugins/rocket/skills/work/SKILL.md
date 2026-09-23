---
name: work
description: Implement one GitHub issue end-to-end (branch → build → verify → PR), creating the issue first for unplanned work. Use for "work on #12", "next issue", or ad-hoc tasks worth tracking.
---

# Work

One issue per session. State lives in GitHub, so `/clear` between issues costs nothing.

`rk` = `../../scripts/rk` relative to this skill's base directory.

Branch flow (`rk` handles it; never bypass): `master ← dev ← epic/<name> ← feat/epic-<name>-<phase>`. Standalone issues: `feat|fix|chore/<n>-<slug> → dev`. `dev → master` only via `rk release`.

1. **Pick.** Epic or "next" given → `rk next <epic>`. Issue given → use it. Unplanned work with no issue → `rk issue "<title>" "<what + 1–3 criteria>"` (no questions unless the goal is unclear), then use that number. Already coded without an issue → same, then skip to 6.
2. **Start.** `rk start <n>` (standalone bug/chore: `rk start <n> fix|chore`): assigns you, checks out the flow branch, prints the issue. That output is the spec; don't fetch it again.
3. **Locate.** Open only the files the issue names or grep finds. Unknown territory → one `rocket:scout` agent with a precise question. Read functions, not whole files.
4. **Build.** Logic → `rocket:tdd`. Config, copy, styling and wiring skip tests (say so).
5. **Stay in scope.** Do exactly the acceptance criteria. Found something else → `rk add <epic> "<title>" "<one line>"` and move on.
6. **Verify.** `rocket:verify` against every acceptance criterion.
7. **Ship.** Conventional commit(s), then `rk pr <n>`.
   - Phase of an epic: verify green → `rk merge <n>` (squash into the epic branch, closes the issue). Last phase → `rk finish <epic>` (PR epic → dev).
   - PRs into dev (epic or standalone): stop and ask the user to validate. Only after an explicit OK in chat → `rk merge <n|epic> --approved`.
   Reply with the PR URL/result and one line. Stop; don't start the next issue unless asked.

**Dev moved** (`rk start` warns the epic is behind, or a phase needs something from dev) → `rk sync <epic>`. On conflict it leaves you on the epic branch: resolve, `git add`, `rk sync <epic> --continue` (no PR needed).
**Release** only when the user asks: `rk release` opens `dev → master`; after explicit approval, `rk release merge --approved`.

Blocked (missing decision, failing unrelated test, unclear criterion) → ask one precise question instead of guessing.

## Subagents
Default is no subagent. When one pays off (independent parallel tasks, wide search), pick the cheapest capable model: `rocket:scout` (haiku) for finding things, `rocket:builder` (sonnet) for a well-specified implementation. Never spawn frontier models (Opus, Fable) for delegated work. Give each subagent a self-contained prompt and ask for a short answer.
