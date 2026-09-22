---
name: debug
description: Root-cause debugging. Use for bugs, failing tests, errors or unexpected behavior, before proposing any fix.
---

# Debug

No fix without a root cause.

1. **Reproduce.** Exact command, observed vs expected. Can't reproduce → gather evidence (logs, inputs, env) first.
2. **Read the error.** Full message; first stack frame in our code.
3. **Localize.** Check recent changes (`git log -S`, `git diff`), narrow inputs, bisect commits. One hypothesis at a time: state it in one line, test it with one check.
4. **Fix the cause**, not the symptom. Add a regression test (`rocket:tdd`).
5. **Verify** with `rocket:verify`.

Stop rules:
- 3 failed hypotheses → stop. Summarize the evidence and ask the user, or question the design.
- No shotgun edits. Revert experiments that didn't help.

Keep it cheap: grep for the error string, read the failing function rather than the file, trim logs with `grep`/`tail`.
