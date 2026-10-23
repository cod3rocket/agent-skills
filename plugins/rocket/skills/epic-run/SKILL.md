---
name: epic-run
description: Execute an epic phase by phase, each phase in its own sub-session. Use for "run epic #12", "execute the epic", "do the next phase".
---

# Epic run

This session orchestrates; each phase is built in a sub-session. Don't implement phases here.

`rk` = `../../scripts/rk` relative to this skill's base directory.

1. **Name.** `rk status <epic>` → rename this session to `<epic title> #<epic>` (`set_session_title`, `self`).
2. **Pick.** `rk next <epic>` → `#<n> <phase title>`. `none` → step 5.
3. **Delegate.** `start_session` with:
   - `initiation` "user_asked", `context` "fresh", `use_worktree` true, `wake_when_done` true
   - `title`: `<phase title> #<n>`
   - `prompt`: `/rocket:work #<n>` — phase of epic #<epic>; ship it with `rk pr <n>` then `rk merge <n>`; reply with the result in one line and stop.
   - `background`: epic title, branch `epic/<name>`, phase position, and that `rk merge` only merges with green CI and no conflicts.
   One phase at a time: phases depend on each other. Then end your turn; the app wakes you when it finishes.
4. **Check.** On wake, `rk status <epic>`. Phase closed → step 2. Still open → read the sub-session's report; relay its question to the user, or send a follow-up to the same session (`SendMessage` to its id). Never redo its work here.
5. **Finish.** All closed → `rk finish <epic>`, reply with the PR URL, and ask the user to validate. Only after an explicit OK → `rk merge <epic> --approved`.

No sub-session tool (plain CLI) → skip the rename; for each phase tell the user to run `/rocket:work #<n>` in a fresh session, and resume at step 4 when they return.

Archive finished phase sessions only if the user asks.
