# dev

dev enviroment using bash

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

