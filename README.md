# Git Linear Hook

A Git hook that automatically adds Linear ticket IDs and descriptions to your commit messages based on branch names.

## Features

- **Extracts Linear ticket IDs** from branch names (e.g., `ABC-601`, `DEV-123`)
- **Adds descriptive context** from branch names to commit messages
- **Smart text processing** - removes prefixes, cleans up separators, capitalizes appropriately
- **zsh optimized** - uses native zsh features for better performance
- **Works with all commit types** - `git commit`, `git commit -m`, `git commit -a -m`
- **No API dependencies** - works offline using only branch name information

## Examples

**Branch:** `feature/abc-601-user-authentication-system`  
**Your commit:** `add password validation`  
**Result:**
```
[ABC-601] User authentication system

add password validation
```

**Branch:** `bugfix/dev-456-fix-null-pointer-exception`  
**Your commit:** `add null check`  
**Result:**
```
[DEV-456] Fix null pointer exception

add null check
```

## Installation

### Quick Install

1. **Download the hook:**
   ```bash
   curl -o .git/hooks/prepare-commit-msg https://raw.githubusercontent.com/yourusername/git-linear-hook/main/prepare-commit-msg
   ```

2. **Make it executable:**
   ```bash
   chmod +x .git/hooks/prepare-commit-msg
   ```

### Manual Install

1. **Copy the script** to `.git/hooks/prepare-commit-msg` in your repository
2. **Make it executable:**
   ```bash
   chmod +x .git/hooks/prepare-commit-msg
   ```

### Global Install (All Repositories)

To use this hook across all your repositories:

1. **Create a global hooks directory:**
   ```bash
   mkdir -p ~/.git-hooks
   ```

2. **Copy the hook:**
   ```bash
   cp prepare-commit-msg ~/.git-hooks/
   chmod +x ~/.git-hooks/prepare-commit-msg
   ```

3. **Configure Git to use global hooks:**
   ```bash
   git config --global core.hooksPath ~/.git-hooks
   ```

## Supported Branch Naming Conventions

The hook automatically handles common prefixes:

- `feature/abc-601-description` → `[ABC-601] Description`
- `feat/dev-123-bug-fix` → `[DEV-123] Bug fix`
- `bugfix/xyz-456-urgent-patch` → `[XYZ-456] Urgent patch`
- `hotfix/lin-789-security-fix` → `[LIN-789] Security fix`
- `chore/team-111-update-deps` → `[TEAM-111] Update deps`

### Linear Ticket ID Pattern

The hook recognizes Linear ticket IDs with the pattern:
- **2+ letters** (uppercase or lowercase)
- **Dash (`-`)**
- **1+ numbers**

Examples: `ABC-601`, `dev-123`, `TEAM-4567`

## How It Works

1. **Extracts the Linear ticket ID** from your current branch name
2. **Removes common prefixes** (`feature/`, `bugfix/`, etc.)
3. **Cleans up the description** by removing the ticket ID and separators
4. **Formats the text** by converting dashes/underscores to spaces
5. **Capitalizes appropriately** (first word only)
6. **Prepends to your commit message** with clean formatting

## Configuration

### Skip Certain Branches

The hook automatically skips processing for merge commits and amends. If you want to skip additional branches, you can modify the script or checkout those branches without the hook.

### Customize the Format

You can modify the script to change the output format. Look for this line:

```bash
if [ -n "$DESCRIPTION" ]; then
    PREFIX="[$TICKET_ID] $DESCRIPTION"
else
    PREFIX="[$TICKET_ID]"
fi
```

## Requirements

- **zsh** (the hook is optimized for zsh)
- **Git** (any recent version)
- **grep** (standard on Unix/Linux/macOS)

## Troubleshooting

### Hook Not Running

1. **Check permissions:**
   ```bash
   ls -la .git/hooks/prepare-commit-msg
   # Should show: -rwxr-xr-x (executable)
   ```

2. **Verify the shebang:**
   ```bash
   head -1 .git/hooks/prepare-commit-msg
   # Should show: #!/bin/zsh
   ```

### No Ticket ID Found

The hook will silently exit if:
- Branch name doesn't contain a Linear ticket ID pattern
- You're on a merge commit or amend
- Ticket ID is already in the commit message

### Testing the Hook

Test the ticket ID extraction manually:
```bash
# Check what ticket ID would be extracted
BRANCH_NAME=$(git symbolic-ref --short HEAD)
echo "Branch: $BRANCH_NAME"
echo "Ticket ID: $(echo "$BRANCH_NAME" | grep -oE '[A-Za-z]{2,}-[0-9]+' | head -n1)"
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Related

- [Linear](https://linear.app) - The issue tracking tool this hook is designed for
- [Git Hooks Documentation](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
- [Linear GitHub Integration](https://linear.app/docs/github) - Official Linear + Git integration

## Changelog

### v1.0.0
- Initial release
- Support for Linear ticket ID extraction
- Smart branch name description parsing
- zsh optimization
- Clean commit message formatting