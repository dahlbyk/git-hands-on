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

## 4. Clone a Remote Repository

```powershell
cd ..
git clone https://github.com/dahlbyk/git-hands-on.git katas

# Let's see what we have
cd katas
ls .git
open .

# What does Git think it has?
git status

    # <internals>
    git cat-file -p $(git rev-parse HEAD)
    git cat-file -p "HEAD^{tree}"
    git cat-file -p 176a458f94e0ea5272ce
    # </internals>

# What local branches do we have?
git branch

# What is 'origin/gh-pages'?
git remote -v
git branch -r

# What does it mean by "up-to-date"?
# Local matches remote "upstream" or "tracked" branch.
git config branch.gh-pages.remote
git config branch.gh-pages.merge

# Big picture?
gitk
```

## 5. Push to a Remote Repository

```powershell
# Edit this line: my favorite color is red
git commit -am "Fix favorite number"

git status                      # Branch is ahead

gitk
git log --oneline --graph --decorate

# <aside>
# Git has a few push modes, but the best is:
git config --global push.default upstream
# </asid

# Let's share our favorite number
git push
```

Oops! That didn't work because you don't have permission to push.

1. [Create a GitHub account](https://github.com/join) and
   [sign in](https://github.com/login?return_to=/dahlbyk/git-hands-on)

2. From [this project on GitHub](https://github.com/dahlbyk/git-hands-on),
   click the Fork button

3. Once forking is complete, click the **Clone or download** button
   and copy the HTTPS URL (e.g. `https://github.com/coridrew/git-hands-on.git`)

```powershell
# Now let's point origin at your fork
git remote set-url origin https://github.com/coridrew/git-hands-on.git

# And try pushing again
git push
```

## 6. Working Locally with Branches

```powershell
# Start work on feature 1
git branch feature1             # Create branch from here
git checkout feature1           # Switch to different branch

    # <internals>
    cat .git/HEAD
    cat .git/refs/heads/feature1
    # <internals>

git status
touch feature1.md
git add .
git commit -m "Add feature 1"

    # <internals>
    cat .git/HEAD
    cat .git/refs/heads/feature1
    # <internals>

# Start work on feature 2
touch feature2.md
git add .
git status

# Oops, forgot to switch to new branch
git checkout gh-pages -q
git checkout -b feature2
git commit -m "Add feature 2"

gitk --branches

# Start work on feature 3
git checkout gh-pages -b feature3
touch feature3.md
# Get distracted by feature 4
touch feature4.md

# Oops, forgot to commit feature 3!
git add .
git status

# Don't want to commit everything...
git reset feature4.md           # reset path is opposite of add
git status
git commit -m "Add feature 3"

git checkout gh-pages -b feature4
git add .
git commit -m "Add feature 4"

gitk --branches
```

## 7. Working Remotely with Branches

```powershell
# First, let's add a remote for the parent repository...
git remote add upstream https://github.com/dahlbyk/git-hands-on.git

    # <internals>
    ls .git/refs/remotes/
    # </internals>

# ...and get latest from GitHub
git fetch --all

    # <internals>
    ls .git/refs/remotes/upstream/
    cat .git/refs/remotes/upstream/gh-pages
    # </internals>

# Now let's share some code!
git push origin feature1

# We can also push the current branch
git push origin HEAD

# But notice we're missing "up-to-date"
git status

# And plain `git push` fails
git push

# Add -u to set the local branch's upstream
git push -u origin HEAD
git status

# Now plain `git push` works
git push

# Also, typing is overrated (pc = push current)
git config --global alias.pc "push -u origin HEAD"
git pc

# Now let's pretend someone else is working
# On your fork on GitHub:
# 1. Use **Branch** dropdown to create `feature5`
# 2. Use **Branch** dropdown to switch to feature1
# 3. Click on `feature1.md`, then the pencil to edit
# 4. Change something and commit changes directly to `feature1`
# 5. Use Branches list to delete `feature4`

# Nothing changes until we explicitly fetch:
git branch -r
git fetch --all

# Why doesn't feature4 show as deleted?
git remote prune origin
git fetch --prune --all

# Always want to prune?
# git config fetch.prune = true
# git config remote.<name>.prune = true

# But we're still on feature4...interesting
git status                      # upstream is gone!
git branch -d feature4
git branch -D feature4

# We should also bring in changes from feature1
git checkout feature1
git status                      # behind!
git pull                        # get latest & merge

# Finally, let's work on feature5
git checkout --track origin/feature5

# Too much typing...let's try again
git checkout master
git branch -D feature5

# If a branch only exists on one remote,
# Git will do the right thing
git checkout feature5

# Now let's add a file and push
touch feature5
git add .
git commit -m "Add feature5"
git push
# Note that `git push` Just Works™
```

## 8. Merging

```powershell
# When we pulled from `feature1` we saw "Fast-forward"
# What does that mean? Let's try again.
git checkout feature1

# reset can also move HEAD (i.e. your branch) around
# In this case, we move HEAD back one, to its parent
# (See `git help revisions` for more tricks like ~)
git reset --hard HEAD~

# Now let's look at the commit graph
gitk feature1 origin/feature1

# Because local feature1 has nothing new, Git can fast-forward
# But you can force it to create a merge commit
git merge --no-ff origin/feature1

# We can also merge local branches
git merge feature2

# And even multiple branches at once ("octopus merge")
git reset --hard HEAD~2
git merge origin/feature1 feature2 feature3
```

## 9. Katas

`katas` branch has its own readme!

```powershell
git checkout katas
```
