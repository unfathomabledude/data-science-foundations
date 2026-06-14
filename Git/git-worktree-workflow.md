# Git Worktree — Team Workflow

## Overview

Git Worktree lets you check out multiple branches simultaneously, each in its own folder.
Instead of stashing work and switching branches, you open a new terminal tab and work in a separate folder — all sharing the same `.git` history.

---

## Prerequisites

The following branches already exist on remote:

| Branch | Purpose |
|--------|---------|
| `main` | Stable base |
| `prod` | Production |
| `dev` | Active development |

---

## One-Time Setup

Run this once from your main repo terminal to create dedicated worktrees for `prod` and `dev`.

```bash
# Syntax
git checkout <branch>
git pull

# Your example
git checkout prod
git pull

git checkout dev
git pull

git checkout main   # switch to main so prod and dev are free for worktrees
```

```bash
# Syntax
git worktree add <path>/<repo-name>/<branch> <branch>

# Your example
git worktree add ../WrkTrs/git_learning_mastery/prod prod
git worktree add ../WrkTrs/git_learning_mastery/dev dev
```

Your folder structure now looks like this:

```
../
  Learning_Mastery/                       ← main repo
  WrkTrs/
    git_learning_mastery/
      prod/                               ← always tracks prod
      dev/                                ← always tracks dev
```

---

## Starting a New Feature

Follow these steps every time a new feature request comes in.

### Step 1 — Pull latest changes from dev

Open a new terminal tab and go to the dev worktree:

```bash
# Syntax
cd <path>/<repo-name>/dev
git pull

# Your example
cd ../WrkTrs/git_learning_mastery/dev
git pull
```

### Step 2 — Create a new feature worktree from dev

```bash
# Syntax
git worktree add -b <feature-branch-name> <path>/<repo-name>/<feature-name>

# Your example
git worktree add -b ftr/alpha_v1 ../WrkTrs/git_learning_mastery/ftr-alpha-v1
```

> Since you are already inside the `dev` worktree, Git uses `dev` as the starting point automatically.

### Step 3 — Start working on the feature

Open another new terminal tab and go to your feature worktree:

```bash
# Syntax
cd <path>/<repo-name>/<feature-name>

# Your example
cd ../WrkTrs/git_learning_mastery/ftr-alpha-v1
```

Start coding here. Your `dev` and `prod` worktrees remain completely untouched.

---

## Final Folder Structure

```
../
  git-learning-mastery/                         ← main repo
  WrkTrs/
    git_learning_mastery/
      prod/                                 ← always tracks prod
      dev/                                  ← always tracks dev
      ftr-alpha-v1/                         ← feature worktree (ftr/alpha_v1 branch)
    another_project/
      prod/
      dev/
      ftr-login/                            ← another repo's feature worktree
```

---

## Useful Commands

```bash
# See all active worktrees
git worktree list

# Remove a feature worktree when done
git worktree remove ../WrkTrs/git_learning_mastery/ftr-alpha-v1

# Clean up stale worktree references
git worktree prune
```

---

## Key Rules

- A branch can only be checked out in **one worktree at a time**
- Always branch new features off **dev**, not `prod` or `main`
- Always `git pull` inside the dev worktree **before** creating a new feature branch
- Use `../` paths to keep all worktrees as sibling folders alongside your repo
- Never add worktree folders inside the repo without adding them to `.gitignore`
