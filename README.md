# dev

dev enviroment using bash

## Bootstrapping a new machine

Install Homebrew first (macOS) and follow its "Next steps" output, then:

```bash
git clone --recursive https://github.com/lhorsl/dev.git ~/repos/dev
cd ~/repos/dev
./run --platform mac       # install tools
./dev-env --platform mac   # install config
exec zsh -l                # pick up PATH and aliases
```

Set `GIT_USER_NAME` and `GIT_USER_EMAIL` before `./run` to configure git identity
non-interactively, otherwise it prints the commands to run by hand.

### Manual steps

Not scriptable, do these after the install:

- Grant Accessibility permission to aerospace and raycast (System Settings ->
  Privacy & Security -> Accessibility)
- Launch Docker Desktop once so it installs its CLI shims
- Allow Notifications for Ghostty, otherwise the agent-signal alerts are dropped
- Set the Ghostty font to `Hack Nerd Font Mono` if the config did not take
- `gh auth login`, or add an SSH key to GitHub

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

## Using tmux sessionizer

