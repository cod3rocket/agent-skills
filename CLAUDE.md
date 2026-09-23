# cod3rocket agent-skills

Claude Code plugin marketplace (`cod3rocket`) with the `rocket` plugin, used across all cod3rocket projects.

- `.claude-plugin/marketplace.json` — marketplace manifest
- `plugins/rocket/skills/<name>/SKILL.md` — skills
- `plugins/rocket/agents/*.md` — subagents (cheap models only)
- `plugins/rocket/scripts/rk` — GitHub epic/issue/branch-flow CLI used by the skills
- `plugins/rocket/hooks/` — `guard-flow` PreToolUse hook enforcing the branch flow

## Authoring rules (token budget is the priority)

Every skill description is loaded in every session; bodies load on use. Optimize for weekly usage limits.

- Description ≤ 200 chars: what + when. No marketing.
- Body ≤ 40 lines, English, imperative. No examples unless a format must be exact.
- Move deterministic work (gh calls, parsing, formatting) into `rk`, not prose. Script output must be terse; it lands in context.
- No SessionStart hooks or always-on injected text. Guard hooks must be silent unless they block.
- Subagents: `haiku` for search/mechanical work, `sonnet` for implementation. Never Opus/Fable or other frontier models.
- Skills reference each other by name (`rocket:tdd`), never inline each other's content.
- Bump `version` in `plugins/rocket/.claude-plugin/plugin.json` on every release.

Branch flow (enforced by `rk` and the hook, keep both in sync): `master ← dev ← epic/<name> ← feat/epic-<name>-<phase>`; standalone `feat|fix|chore/<n>-<slug> → dev`. Phases merge into the epic after verify; `rk sync` merges dev into an epic directly; anything into dev or master only with the user's approval; master only via `rk release`.

Check scripts with `shellcheck plugins/rocket/scripts/rk plugins/rocket/hooks/guard-flow`.
