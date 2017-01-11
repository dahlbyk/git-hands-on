# Git Hands-On Workshop

## 0. PowerShell Prep

```powershell
# Let's teach PowerShell some macOS commands

# 1. `touch <file>` creates an empty file
function touch ($Path) { Add-Content $Path $null }
# 2. `open <path>` opens Explorer/Finder
Set-Alias open start
```

## 1. Help

```powershell
git help                        # General help
git help --guide                # Common Git guides
```

## 2. Configuration

```powershell
git help config                 # See FILES section
git config --global --list      # ~/.gitconfig

# Check specific config values
git config user.name
git config user.email

git config --global user.email git@example.com

git config --global --edit      # Open config in editor
```

## 3. Git 101

```powershell
# Change to location of your choice
# ...so we can make directory for today
mkdir git-hands-on
cd git-hands-on

# Let's create a new repository
git init git101
# Can also `git init` directory that already exists

# Let's see what we have
cd git101
ls .git
open .

# Of particular note:
# - .git/objects/               (empty)
# - .git/refs/heads/            (empty)
# - .git/config                 `git config -e`
# - .git/HEAD                   ref: refs/heads/master

# What does Git think it has?
git status

# Let's commit something!
touch README.md                 # Create README.md
git status                      # Untracked files!

git add -A                      # Track all changes
git status                      # Changes!

# <internals>
ls .git/objects/
ls .git/objects/e6/
git cat-file -t e69de29bb2d1d6434b8b
# </internals>

git commit -m "Add empty README"

# <internals>
git cat-file -t f93e3a1a1525fb5b9102
git cat-file -p f93e3a1a1525fb5b9102

cat .git/HEAD
ls .git/refs/heads/
cat .git/refs/heads/master

git rev-parse HEAD
git cat-file -p $(git rev-parse HEAD)
# </internals>

git show HEAD
git log
```

### Git 101 Summary

1. Make changes
2. Track changes: `git add -A`
3. Commit changes: `git commit -m "Do something"`
