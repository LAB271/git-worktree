# Git Worktree Management Toolkit

A streamlined collection of templates and utilities for managing Git worktrees efficiently across your Python development workflow with included virtualenv handling.

### Quick Start Example

Here's a complete example showing how to create a worktree and set up your development environment:

```bash
# 1. Start in your main repository
cd ~/code/project_a
git branch
# * main

# 2. Create a new worktree for a feature (using our gwc alias)
gwc feat/user-authentication
# 🌳 Creating worktree: ../project_a-worktree/feat-user-authentication
# 📋 Branch: feat/user-authentication
# ✅ Worktree created!
# 📂 Changed to: /Users/dev/code/project_a-worktree/feat-user-authentication
# 🔧 Running 'make env'...
# virtualenv .project_a
# Installing dependencies...
# ✅ Environment setup complete!
# ✅ Virtual environment activated!

# 3. You're now in the new worktree with environment ready
pwd
# /Users/dev/code/project_a-worktree/feat-user-authentication

git branch --show-current
# feat/user-authentication

which python
# /Users/dev/code/project_a-worktree/feat-user-authentication/.project_a/bin/python

# 4. Start coding immediately
# ... write your code ...

# 5. Check all your worktrees
lw
# Git Worktrees:
# ==============
# /Users/dev/code/project_a
#    Branch: main [✅ .project_a]
#
# 👉 /Users/dev/code/project_a-worktree/feat-user-authentication
#    Branch: feat/user-authentication [✅ .project_a]

# 6. Return to main when needed
bw
# 📂 Back to main: /Users/dev/code/project_a
# 🌳 Branch: main
```

**What just happened?**

1. **`gwc feat/user-authentication`** did all of this automatically:
   - Created directory: `../project_a-worktree/feat-user-authentication`
   - Created new branch: `feat/user-authentication`
   - Changed to the new directory
   - Ran `make env` to create virtual environment (`.project_a/`)
   - Installed all dependencies from `requirements.txt`
   - Activated the virtual environment

2. **Directory structure** created:
   ```
   ~/code/
   ├── project_a/                                      # Original repo (main branch)
   │   ├── .git/                                       # Git database (shared)
   │   ├── .project_a/                                 # Main's virtualenv
   │   └── ...
   └── project_a-worktree/                             # Worktree container
       └── feat-user-authentication/                   # Your new worktree
           ├── .git -> ../../project_a/.git/worktrees/ # Links to shared Git
           ├── .project_a/                             # Isolated virtualenv
           └── ...                                     # All project files
   ```

3. **Each worktree is independent**:
   - Separate working directory
   - Own virtual environment
   - Own installed packages
   - Own IDE settings
   - But shares Git history (efficient!)

4. **Virtual environment detection**:
   - The `lw` command shows `[✅ .project_a]` indicating the virtualenv exists
   - Works with any hidden directory: `.venv`, `.project_a`, `.env`, etc.
   - The `venv` alias (`source .*/bin/activate`) activates it in any worktree


## What Are Git Worktrees?

Git worktrees enable you to have multiple working directories attached to a single repository, each checked out to different branches. Instead of constantly switching branches and stashing changes, you can work on multiple tasks simultaneously in separate directories that all share the same Git history.

## Why Use This Toolkit?

This repository provides:

- **Bash Aliases**: Streamlined commands for worktree operations with smart defaults
- **Bash Completion**: Provides tab completion for worktree names
- **Project Templates**: Ready-to-use Makefile for Python projects with virtual environment management
- **Virtual Environment Integration**: Automatic detection and management of Python venvs
- **Standardized Workflows**: Consistent setup across all worktrees
- **Best Practices**: Proven patterns for worktree management

## Getting Started

### Installation

All script extensions are available as shell extensions.

```bash
# Add aliases and completion to your ~/.bashrc or ~/.zshrc
source /path/to/git-worktree/bash_aliases
source /path/to/git-worktree/bash_completion

# Or copy the contents directly into your shell config
cat /path/to/git-worktree/bash_aliases >> ~/.bashrc
cat /path/to/git-worktree/bash_completion >> ~/.bashrc
h_aliases >> ~/.bashrc
```

### Essential Git Worktree Commands

For manual worktree management (our aliases simplify these):

```bash
# View all worktrees
git worktree list

# Create worktree from existing branch
git worktree add <path> <existing-branch>

# Create worktree with new branch
git worktree add -b <new-branch> <path> <base-branch>

# Delete a worktree
git worktree remove <path>

# Clean up stale worktree references
git worktree prune
```

## Bash Aliases - Streamlined Worktree Management

This toolkit includes a comprehensive set of bash aliases that simplify common worktree operations. These aliases follow a consistent naming pattern and integrate seamlessly with Python virtual environments.

### Command Reference

| Command | Description |
|---------|-------------|
| `gw <branch>`  | Create a new worktree |
| `gwc <branch>` | Create worktree + auto-setup environment `make env`|
| `wm <name>`    | Move to a specific worktree |
| `lw`           | List all worktrees with status |
| `bw`           | Back to main worktree |
| `rw <name>`    | Remove a worktree (with confirmation) |
| `pw`           | Print current worktree information |
| `cw`           | Cleanup pruned worktrees |
| `gww`          | Show quick reference help |

### How It Works

#### Directory Structure Convention

The aliases use a predictable directory structure based on your repository name:

```
~/code/
├── project_a/                     # Main repository (discovered automatically)
└── project_a-worktree/            # Worktree container (auto-created)
    ├── feat-authentication/       # Worktree for feat/authentication
    ├── fix-bug-123/               # Worktree for fix/bug-123
    └── spike-redis/               # Worktree for spike/redis
```

**Key behaviors:**
- **Root folder detection**: Automatically discovers your main repository name from `git worktree list`
- **Branch name transformation**: Converts `/` to `-` (e.g., `feat/auth` → `feat-auth`)
- **Sibling directory pattern**: Places all worktrees in a `<repo-name>-worktree/` folder next to your main repo
- **Consistent paths**: All commands understand this structure automatically

#### Virtual Environment Detection

Each command automatically detects and reports on Python virtual environments:

- **Pattern matching**: Looks for any hidden directory (`.venv`, `.project_a`, etc.) with `bin/python`
- **Status indicators**: Shows ✅ or ❌ to indicate environment presence
- **Integration**: Works with the `venv` alias (`source .*/bin/activate`)

### Detailed Command Examples

#### 1. `gw` - Create Worktree

Creates a new worktree with a new branch:

```bash
$ gw feat/authentication
Creating worktree: ../project_a-worktree/feat-authentication
Branch: feat/authentication
✅ Worktree created successfully!
To move there: wm feat-authentication
To setup:      cd ../project_a-worktree/feat-authentication && make env && venv
```

**What happens:**
1. Extracts main repository name: `project_a`
2. Creates target path: `../project_a-worktree/feat-authentication`
3. Creates new branch: `feat/authentication`
4. Creates worktree at the path

#### 2. `gwc` - Create Worktree + Setup

Creates worktree AND automatically runs environment setup:

```bash
$ gwc feat/authentication
🌳 Creating worktree: ../project_a-worktree/feat-authentication
📋 Branch: feat/authentication
✅ Worktree created!
📂 Changed to: /Users/dev/code/project_a-worktree/feat-authentication
🔧 Running 'make env'...
# ... make output ...
✅ Environment setup complete!
🎯 Ready to work! Run 'venv' to activate environment
✅ Virtual environment activated!
```

**What happens:**
1. Everything from `gw` above
2. Changes directory to the new worktree
3. Runs `make env` (creates virtualenv, installs dependencies)
4. Attempts to activate the virtual environment
5. Reports status

**Perfect for:** Starting new features where you need a clean environment immediately.

#### 3. `wm` - Move to Worktree

Navigate to an existing worktree:

```bash
$ wm feat-authentication
📂 Moved to: /Users/dev/code/project_a-worktree/feat-authentication
🌳 Branch: feat/authentication
✅ Virtual environment exists (.project_a/)
```

**Intelligence:**
- If you're already in a worktree, it navigates back first
- Auto-detects virtual environment and reports status
- Suggests creating environment if none exists

#### 4. `lw` - List Worktrees

Shows all worktrees with detailed status:

```bash
$ lw
Git Worktrees:
==============
👉 /Users/dev/code/project_a
   Branch: main [✅ .project_a]

/Users/dev/code/project_a-worktree/feat-authentication
   Branch: feat/authentication [✅ .project_a]

/Users/dev/code/project_a-worktree/fix-bug-123
   Branch: fix/bug-123 [❌ no env]

/Users/dev/code/project_a-worktree/spike-redis
   Branch: spike/redis [✅ .venv]

Use: wm <name> to move to worktree
Use: bw to return to main
```

**Indicators:**
- `👉` marks your current worktree
- `[✅ .venv]` shows virtual environment exists (with name)
- `[❌ no env]` indicates no virtual environment found

**Example interpretation:**
- You're currently in the main repository
- Three worktrees exist: feat-authentication, fix-bug-123, and spike-redis
- The `fix-bug-123` worktree needs environment setup (`make env`)
- Different worktrees can use different venv names (`.project_a`, `.venv`)

#### 5. `bw` - Back to Main

Returns to the main worktree (first one created):

```bash
$ bw
📂 Back to main: /Users/dev/code/project_a
🌳 Branch: main
✅ Virtual environment exists (.project_a/)
```

**Perfect for:** Quick return after finishing work in a feature worktree.

#### 6. `rw` - Remove Worktree

Safely removes a worktree with confirmation:

```bash
$ rw feat-authentication
⚠️  About to remove worktree:
   Path: ../project_a-worktree/feat-authentication
   Branch: feat/authentication

Continue? [y/N] y
✅ Worktree removed: ../project_a-worktree/feat-authentication
```

**Safety features:**
- Shows what will be removed before confirmation
- Requires explicit `y` confirmation
- Provides force-remove suggestion if removal fails
- Deletes both worktree directory AND the branch

#### 7. `pw` - Print Worktree Info

Displays detailed information about current worktree:

```bash
$ pw
Current Worktree:
=================
Path:   /Users/dev/code/project_a-worktree/feat-authentication
Branch: feat/authentication
Main:   no
Env:    ✅ .project_a exists
Active: ✅ project_a
```

**Shows:**
- Full path to current worktree
- Current branch name
- Whether this is the main worktree
- Virtual environment status (exists + activated)

#### 8. `cw` - Cleanup Worktrees

Removes stale worktree references:

```bash
$ cw
Pruning removed worktrees...
✅ Cleanup complete
```

**Use when:** You've manually deleted worktree directories and git still references them.

### Complete Workflow Example

Here's a typical development workflow using these aliases:

```bash
# 1. Check current worktrees
$ lw
Git Worktrees:
==============
👉 /Users/dev/code/project_a
   Branch: main [✅ .project_a]

# 2. Create new feature with automatic setup
$ gwc feat/user-dashboard
🌳 Creating worktree: ../project_a-worktree/feat-user-dashboard
# ... setup happens ...
✅ Virtual environment activated!

# 3. Do your work
$ git add .
$ git commit -m "Add user dashboard"
$ git push origin feat/user-dashboard

# 4. Return to main while feature is in review
$ bw
📂 Back to main: /Users/dev/code/project_a

# 5. Start hotfix in parallel
$ gwc fix/security-patch
# ... work on hotfix ...

# 6. Check all active worktrees
$ lw
Git Worktrees:
==============
/Users/dev/code/project_a
   Branch: main [✅ .project_a]

/Users/dev/code/project_a-worktree/feat-user-dashboard
   Branch: feat/user-dashboard [✅ .project_a]

👉 /Users/dev/code/project_a-worktree/fix-security-patch
   Branch: fix/security-patch [✅ .project_a]

# 7. After feature merges, clean up
$ rw feat-user-dashboard
⚠️  About to remove worktree...
Continue? [y/N] y
✅ Worktree removed

# 8. Cleanup any stale references
$ cw
```

### Integration with Virtual Environments

The aliases integrate with the `venv` alias for quick activation:

```bash
# Defined in bash_aliases
alias venv='source .*/bin/activate'

# Usage in any worktree
$ wm feat-authentication
$ venv  # Activates whatever .* virtualenv exists
```

This pattern-based approach works with any naming convention:
- `.venv`
- `.project_a`
- `.myenv`
- Any hidden directory with `bin/activate`

### Tips and Tricks

**Quick environment check across all worktrees:**
```bash
$ lw  # Shows environment status for all at once
```

**Create multiple worktrees for parallel work:**
```bash
$ gwc feat/frontend
$ gwc feat/backend
$ gwc feat/api
$ lw  # See all three ready to work
```

**Fast context switching:**
```bash
$ wm frontend    # Work on frontend
$ wm backend     # Switch to backend
$ bw             # Back to main for releases
```

**Branch naming for clean organization:**
```bash
# Use consistent prefixes
$ gw feat/user-auth      → feat-user-auth/
$ gw fix/bug-123         → fix-bug-123/
$ gw spike/new-tech      → spike-new-tech/
$ gw release/v2.0        → release-v2.0/
```

## Practical Examples

### Multi-Feature Development

When working on `PROJECT_A` with several features in progress:

```bash
# Your main repository
cd ~/code/project_a

# Create separate worktrees for each feature
git worktree add ../project_a-worktree/feat-authentication feat/user-authentication
git worktree add ../project_a-worktree/feat-api feat/rest-api
git worktree add ../project_a-worktree/feat-ui feat/dashboard-ui

# Result: Four independent working directories
# ~/code/project_a                               → main branch
# ~/code/project_a-worktree/feat-authentication  → feat/user-authentication
# ~/code/project_a-worktree/feat-api             → feat/rest-api
# ~/code/project_a-worktree/feat-ui              → feat/dashboard-ui
```

Each directory has its own:
- Working files
- Virtual environment (using the Makefile template)
- Build artifacts
- IDE/editor state

But they share:
- Git history and objects
- Configuration (.git/config)
- Hooks

## Using the Makefile Template

The included `Makefile.template` provides Python project automation:

### Setup

```bash
# In your new worktree
cp /path/to/git-worktree/templates/Makefile.template ./Makefile

# Configure for your project
sed -i.bak 's/PACKAGE_NAME_HERE/project_a/' Makefile && rm Makefile.bak

# Initialize environment
make check        # Verify Python and pip available
make env          # Create virtualenv and install dependencies
```

### Available Targets

| Command | Description |
|---------|-------------|
| `make help` | Show all available commands |
| `make check` | Validate development dependencies |
| `make env` | Create virtual environment and install packages |
| `make test` | Run unit tests with coverage (requires 85%+ coverage) |
| `make clean` | Remove all build artifacts and virtualenv |
| `make freeze` | Update requirements.txt from current environment |
| `make outdated` | List packages with available updates |
| `make version` | Display Python version |

### Example Workflow

```bash
# First time setup
make env

# During development
source .project_a/bin/activate
# ... write code ...
make test

# Before committing
make test
make freeze       # Update requirements.txt if needed

# Cleanup
make clean
```

##  Directory Organization

Keep worktrees organized and predictable:

```
~/code/
├── project_a/                     # Main repository (main branch)
└── project_a-worktree/
    ├── feat-*/                    # Feature worktrees
    ├── fix-*/                     # Bugfix worktrees
    └── spike-*/                   # Experimental worktrees
```


### Documentation
- [Official Git Worktree Documentation](https://git-scm.com/docs/git-worktree)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Python Virtual Environments (venv) — Official Docs](https://docs.python.org/3/library/venv.html)
- [Real Python: Python Virtual Environments: A Primer](https://realpython.com/python-virtual-environments-a-primer/)
- [GNU Bash Completion documentation](https://github.com/scop/bash-completion)
- [Bash Programmable Completion](https://www.gnu.org/software/bash/manual/html_node/Programmable-Completion.html)

## License

Licensed under the Apache License 2.0. See [LICENSE](LICENSE) for full details.


