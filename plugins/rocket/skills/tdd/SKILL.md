---
name: tdd
description: Red-green-refactor loop. Use when implementing logic or fixing a bug that can be reproduced in a test.
---

# TDD

Per behavior:
1. **Red.** Write one failing test for the next behavior. Run only that test; confirm it fails for the expected reason (not a typo or import error).
2. **Green.** Write the minimum code to pass. Run the test.
3. **Refactor** if it helps, keeping it green.

When the behaviors are done, run the affected suite once.

Rules:
- Test behavior through the public API, not internals. Mock only at I/O boundaries.
- Run targeted tests (file or name filter). Full suite only at the end.
- Keep runner output small: quiet/dot reporters, failures only, `| tail -40`.
- Bug fix → first a test that reproduces it.
- Code written before its test → delete it and restart from red.
- Skip TDD for config, copy, styling and pure wiring, and say so.
