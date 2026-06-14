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
  git-learning-mastery/                       ← main repo
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

**Worktree Locking**

- **A branch can only be checked out in one worktree at a time**
  If `prod` is checked out somewhere — anywhere — Git won't let you check it out again elsewhere. One branch, one location, at any given time.

- **Checked out branch cannot have its own worktree**
  If you are sitting on `prod` in your main repo and run `git worktree add ../WrkTrs/prod prod` — it fails. Because `prod` is already checked out right where you are standing. You can't create a worktree for something that's already active.
  ```bash
  git worktree add ../WrkTrs/prod prod
  # fatal: 'prod' is already checked out
  ```

- **Once a worktree is created for a branch, that branch is locked to that worktree** — `git checkout` from anywhere else will fail
- The only way to work on a locked branch is to **`cd` into its worktree folder**, not `git checkout`
- To free a locked branch: `git worktree remove <path>` first, then `git checkout` works again

**Daily Workflow**

- **Keep your main repo always on `main`**
  Your main repo has to sit on *some* branch. Whatever branch that is becomes unavailable for worktrees. So park it on `main`, which you rarely need a worktree for — that way `prod` and `dev` are always free to be assigned their own worktrees without hitting the `fatal: already checked out` error.
  > Git locks a branch to one location → your main repo occupies whatever branch it sits on → so keep main repo on `main` → so `prod` and `dev` stay free for worktrees

- Always branch new features off **dev**, not `prod` or `main`
- Always `git pull` inside the dev worktree **before** creating a new feature branch
- You never `git checkout` between branches anymore — just **`cd` between worktree folders**

**Folder Structure**
- Use `WrkTrs/<repo-name>/<branch>` structure to avoid conflicts across multiple repos
- Use `../` paths to keep all worktrees as sibling folders alongside your repo
- Never add worktree folders inside the repo without adding them to `.gitignore`
