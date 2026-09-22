---
name: verify
description: Evidence before completion. Use before saying work is done, fixed or passing, and before committing or opening a PR.
---

# Verify

Claim only what you ran and saw after your last edit.

Run what the project has (commands in CLAUDE.md, package.json, Makefile, etc.):
1. Tests for the touched area, then the full suite if it's fast.
2. Typecheck and lint.
3. Build, if build config or entry points changed.
4. Each acceptance criterion of the issue, with its evidence.

Report one line per check:
```
✅ tests: 42 passed (pnpm test src/auth)
✅ lint
❌ typecheck: 2 errors → fixing
```

Never write "should work" or "probably", and never reuse a pass from before your last edit. If something can't be verified here, say so plainly.
