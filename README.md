# dev

dev enviroment using bash

## Bootstrapping a new machine

Install Homebrew first (macOS), then:

```bash
git clone --recursive https://github.com/lhorsl/dev.git ~/repos/dev
cd ~/repos/dev
./run --platform mac       # install tools
./dev-env --platform mac   # install config
exec zsh -l                # pick up PATH and aliases
```

Set `GIT_USER_NAME` and `GIT_USER_EMAIL` before `./run` to configure git identity
non-interactively, otherwise it prints the commands to run by hand.

`dev-env` owns a marked block in `~/.zshrc` and `~/.zprofile` (brew shellenv,
PATH, aliases, shell integrations). It rewrites that block in place, so re-running
it is safe and repairs the config after Oh My Zsh replaces `~/.zshrc`.

### Manual steps

Not scriptable, do these after the install:

- Grant Accessibility permission to aerospace and raycast (System Settings ->
  Privacy & Security -> Accessibility)
- Launch Docker Desktop once so it installs its CLI shims
- Allow Notifications for Ghostty, otherwise the agent-signal alerts are dropped
- Set the Ghostty font to `Hack Nerd Font Mono` if the config did not take
- `gh auth login`, or add an SSH key to GitHub
- `atuin import auto` to pull existing shell history in, then `atuin register` (or
  `atuin login` on a second machine) if you want history synced across machines

## Runs and installs

`--platform` is required. Use `mac` or `linux`. Use `--dry` to preview what will be installed. An optional filter argument scopes to a single tool.

### Example usage

```bash
# Install all tools on macOS
./run --platform mac

# Install all tools on Linux (Ubuntu)
./run --platform linux

# Preview Linux installs without executing
./run --dry --platform linux

# Install a single tool
./run --platform mac neovim
./run --platform linux tmux
```

## Dev-env

Dev env sets up config using:

```bash
./dev-env --platform mac
./dev-env --platform linux

# Preview without executing
./dev-env --dry --platform linux
```

## Claude Code

`dev-env` copies `.claude/` to `~/.claude/`, so the instructions, rules, agents and
skills below apply in every project. Re-run `./dev-env` after editing them, then
restart Claude Code. The copy only adds and overwrites; delete a removed file from
`~/.claude/` by hand.

| Path | What |
|------|------|
| `.claude/CLAUDE.md` | Global instructions |
| `.claude/rules/` | Per-language rules, loaded for matching files (`go.md`, `grpc.md`) |
| `.claude/agents/` | Subagents: `go-reviewer`, `plan-reviewer` |
| `.claude/skills/` | Skills: `plan-ticket` |

### Workflow

ticket -> `/plan-ticket` -> approve -> implement -> `go-reviewer` -> commit

```text
/plan-ticket 42                                 # GitHub issue number or URL
/plan-ticket docs/tickets/export.md             # ticket file
/plan-ticket add CSV export to the reports API  # pasted text
```

`/plan-ticket` reads the ticket and the code, asks the questions that change the
design, then writes `docs/plans/<ticket-id>-<slug>.md`. It writes ADRs to
`docs/adr/NNNN-<slug>.md` only for hard-to-reverse decisions, runs `plan-reviewer`
on the plan, and stops. Nothing is implemented until you approve.

Plans and ADRs are local only: the skill adds `docs/plans/` and `docs/adr/` to
`.git/info/exclude`, so they are never committed. Because the plan is a file, you
can `/clear` after approving and continue with `implement docs/plans/<file>.md`.

### Agents

Agents run in their own context and only report back; neither edits files. Invoke
them by name:

```text
use go-reviewer                          # uncommitted diff, or branch vs main if clean
use go-reviewer on internal/export/      # specific path
use plan-reviewer on docs/plans/42-csv-export.md
```

- `go-reviewer` (sonnet): runs build, vet, golangci-lint, govulncheck, `go test -race`
  and `buf` checks, then reviews changed lines for security, error handling,
  concurrency and convention issues. Ends with BLOCK, CHANGES or APPROVE.
  `govulncheck` needs `go install golang.org/x/vuln/cmd/govulncheck@latest`.
- `plan-reviewer` (opus): checks a plan against its ticket and the actual code:
  missing requirements, unsafe proto or migration changes, no rollback path,
  over-engineering. Ends with REVISE or READY. `/plan-ticket` runs it automatically.

Both report only findings with a concrete failure and high confidence; zero
findings is a normal result. Use the built-in `/code-review` for a general bug
hunt and `go-reviewer` for checks against these rules.

## Using tmux sessionizer

