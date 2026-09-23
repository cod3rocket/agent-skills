# cod3rocket agent-skills

Token-lean Claude Code workflow for cod3rocket projects: plan features as GitHub epics with native sub-issues, implement one issue per session, with TDD, root-cause debugging and verification.

## Install

The repo is private and installs over SSH, so it uses your existing GitHub SSH key (no token needed). You need read access to `cod3rocket/agent-skills`; check with `ssh -T git@github.com`.

In any project, add to `.claude/settings.json` (committed, so the whole team gets it):

```json
{
  "extraKnownMarketplaces": {
    "cod3rocket": {
      "source": { "source": "git", "url": "git@github.com:cod3rocket/agent-skills.git" }
    }
  },
  "enabledPlugins": { "rocket@cod3rocket": true }
}
```

Or manually:

```bash
claude plugin marketplace add git@github.com:cod3rocket/agent-skills.git
claude plugin install rocket@cod3rocket
```

Update with `claude plugin marketplace update cod3rocket`. Background auto-update also goes over SSH, so the key must load without a prompt (ssh-agent / macOS keychain).

Requires `gh` (authenticated). The guard hook needs `jq`.

## Branch flow

```
master ← dev ← epic/<name> ← feat/epic-<name>-<phase>
               └ standalone: feat|fix|chore/<issue>-<slug> → dev
dev → epic/<name>: rk sync (keeps a long-running epic up to date)
```

| Step | Who merges |
|---|---|
| phase `feat/epic-*` → `epic/<name>` | Claude, after `rocket:verify` is green (squash, closes the issue) |
| `epic/<name>` → `dev` | Claude opens the PR; merges only after you approve in chat |
| standalone → `dev` | same: PR, then merge only after your approval |
| `dev` → `epic/<name>` | Claude, `rk sync <epic>`: direct merge, no PR; conflicts resolved locally then `--continue` |
| `dev` → `master` | Claude opens the release PR (`rk release`); merges only after your approval |
| anything else → `master` | never (refused by `rk`, blocked by the hook) |

Merged branches are always deleted: `rk merge` removes remote + local; `rk clean` (run by `rk start`) removes local leftovers from merges done in the GitHub UI. Also enable *Settings → Automatically delete head branches* on each repo.

The plugin ships a `PreToolUse` hook (`hooks/guard-flow`) active in repos that have a `dev` branch. It blocks raw `gh pr create/merge` and raw merge API calls, pushes to `master/main/dev/epic/*`, and commits/merges while on those branches. It only looks at command heads, so text in heredocs, commit messages or issue bodies never trips it. It costs no tokens unless it blocks.

- To disable it, start Claude Code with the variable set: `RK_GUARD=0 claude`. Prefixing a single command (`RK_GUARD=0 git ...`) has no effect, because the hook is a separate process.
- `RK_DEV` / `RK_MAIN` override the `dev` / `master` names.

**CI:** phase PRs target `epic/<name>`, so workflows triggered on `pull_request` must include `epic/**` in their `branches`, or phases run without CI. Keep `dev` and `master` protected so no automation deletes them.

## Flow

```
/rocket:epic  "idea"   → questions (1 batch) → outline → epic/<name> + ordered sub-issues
/clear
/rocket:work  #12      → feat/epic-<name>-1 → tdd → verify → PR → merge into epic/<name>
/clear
/rocket:work  next of #10 ...                    last phase → PR epic/<name> → dev (you approve)

/rocket:work  "fix date format on invoices"   → standalone issue → fix/<n>-<slug> → PR → dev (you approve)
```

| Skill | Use |
|---|---|
| `rocket:epic` | Plan a feature into an epic issue with sub-issues |
| `rocket:work` | Implement one issue end-to-end (creates it first for unplanned work), open the PR |
| `rocket:tdd` | Red-green-refactor loop |
| `rocket:debug` | Root cause before any fix |
| `rocket:verify` | Evidence before claiming done |

Agents: `rocket:scout` (haiku, read-only search), `rocket:builder` (sonnet, implementation).

## Why it's cheap

- GitHub issues are the memory: each issue is worked in a fresh context, not a growing session.
- Deterministic work (creating/linking issues, branches, PR bodies) runs in `rk`, one call, terse output.
- Short skills, no session-start injection; only 5 short descriptions are always loaded.
- Delegated work runs on haiku/sonnet, never frontier models.
- Targeted tests and trimmed output instead of full logs.

## `rk`

```
rk create <spec.md> [name]        epic + branch epic/<name> (from dev) + ordered sub-issues
rk add <epic> <title> [body]      new sub-issue
rk issue <title> [body]           standalone issue (goes to dev)
rk status <epic>                  progress + sub-issue list
rk next <epic>                    first open, unassigned sub-issue
rk start <issue> [feat|fix|chore] assign @me, checkout the flow branch, print issue
rk pr <issue>                     push + PR to the flow base (phase -> epic, standalone -> dev)
rk finish <epic>                  all phases closed -> PR epic/<name> -> dev
rk merge <issue> [--approved]     merge the PR, close the issue, delete the branch; dev needs --approved
rk clean                          delete local branches whose PR was merged + close their issue (auto on rk start)
rk sync <epic> [--continue|--abort]  merge dev into epic/<name> (no PR); conflicts resolved locally
rk release [title]                PR dev -> master
rk release merge --approved       merge the release PR (keeps dev)
```
