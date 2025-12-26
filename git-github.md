
# 1. What is Version Control?

A **Version Control System (VCS)** tracks changes to files over time so you can:

* Track who changed what and when
* Roll back to previous versions
* Collaborate safely in teams
* Experiment without fear of breaking code

## Types of Version Control

#### 1. Centralized VCS (CVCS)

* Single central server (e.g., SVN)
* Requires internet to commit
* Server failure can block development

#### 2. Distributed VCS (DVCS)

* Every developer has a full copy of the repository
* Works offline
* Faster and more reliable
* **Git is a DVCS**

---

# 2. Introduction to Git

* Created by **Linus Torvalds (2005)** for Linux kernel development
* Designed to be **fast, distributed, and secure**

### Git vs GitHub

| Git                    | GitHub                |
| ---------------------- | --------------------- |
| Version control system | Code hosting platform |
| Works locally          | Works online          |
| Tracks history         | Enables collaboration |

Alternatives: GitLab, Bitbucket

---

# 3. Installation & Configuration

### Installation

* **Windows:** git-scm.com
* **macOS:** `brew install git`
* **Linux:** `sudo apt install git`

### Initial Setup (Required)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check configuration:

```bash
git config --list
```

---

# 4. Git Architecture (Very Important)

Git works in **three areas** (official Git concept):

1. **Working Directory** – where files are edited
2. **Staging Area (Index)** – selected changes prepared for commit
3. **Repository (.git)** – permanent history (snapshots)

```
Working Directory → Staging Area → Repository
```

👉 Official Git emphasizes that the *staging area* gives precise control over what goes into a commit.

---

# 5. Basic Git Workflow (Official Flow)

This section explains the **day-to-day Git workflow** recommended in official Git documentation. Git works by creating snapshots of your project, not storing file differences like older VCS tools.

### Create or Copy a Repository

* `git init` creates a **new empty Git repository** in the current folder by adding a `.git` directory.
* `git clone <url>` creates a **full copy** of an existing repository, including its entire history.

```bash
git init
git clone <url>
```

### Track & Save Changes

This is the most common workflow used every day:

1. `git status` shows the current state of files (modified, staged, untracked).
2. `git add` moves changes to the **staging area**, preparing them for commit.
3. `git commit` permanently saves a snapshot into the repository.

```bash
git status
git add <file>
git add .
git commit -m "message"
```

### View History

Git stores history as a **directed graph of commits**.

* `git log` shows detailed commit history.
* `--oneline --graph --all` gives a compact visual overview of branches.

```bash
git log
git log --oneline --graph --all
```

---

# 6. Viewing & Comparing Changes

Git allows you to inspect differences at multiple stages.

* `git diff` → changes in working directory (not staged)
* `git diff --staged` → changes staged for next commit
* `git diff commit1 commit2` → compare two commits

This helps review code before committing or during debugging.

```bash
git diff
git diff --staged
git diff commit1 commit2
```

---

# 7. Undoing & Fixing Mistakes (Officially Recommended)

Git provides **safe and unsafe undo mechanisms**. Official docs strongly recommend choosing commands based on whether commits are already shared.

### Restore Files

* `git restore <file>` discards local changes (unstaged).
* `git restore --staged <file>` removes a file from staging but keeps changes.

```bash
git restore <file>
git restore --staged <file>
```

### Amend Last Commit

Used to:

* Fix commit message
* Add forgotten files to the last commit

```bash
git commit --amend
```

### Reset (⚠ Dangerous)

`git reset` moves the branch pointer backward.

* `--soft` → keeps changes staged
* `--mixed` → keeps changes unstaged (default)
* `--hard` → deletes changes completely

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

### Safe Undo (Public Commits)

When commits are already pushed, **never use reset**.
Use `git revert`, which creates a new commit that undoes changes.

```bash
git revert <commit>
```

---

# 8. Ignoring & Removing Files

Git should not track generated, secret, or temporary files.

### .gitignore

The `.gitignore` file tells Git which files or folders to ignore.

```
node_modules/
.env
*.log
```

### Remove Files

* `git rm <file>` removes file from project and Git.
* `git rm --cached <file>` removes file from Git but keeps it locally.

```bash
git rm <file>
git rm --cached <file>
```

---

# 9. Branching (Core Git Feature)

Branches are **lightweight pointers to commits**, making branching extremely fast.
Official Git encourages frequent branching for:

* New features
* Bug fixes
* Experiments

### Branch Commands

```bash
git branch
git branch <name>
git switch <name>
git switch -c <name>
```

---

# 10. Merging & Conflicts

Merging combines changes from one branch into another.

### Merge Branch

```bash
git merge <branch>
```

Types of merges:

* **Fast-forward** → no divergence
* **Three-way** → branches diverged

### Conflict Markers

When Git cannot auto-merge, it marks conflicts:

```
<<<<<<< HEAD
=======
>>>>>>> branch
```

Manually resolve → `git add` → `git commit`

---

# 11. Remote Repositories

Remote repositories enable collaboration and backups.

### Connect Remote

```bash
git remote add origin <url>
git remote -v
```

### Push & Pull

* `git push` uploads commits
* `git pull` fetches + merges
* `git fetch` downloads without merging

```bash
git push -u origin main
git pull
git fetch
```

---

# 12. Git Stash (Temporary Storage)

Stash temporarily saves uncommitted changes so you can switch branches safely.

```bash
git stash
git stash list
git stash apply
git stash pop
git stash drop
```

---

# 13. Tags (Releases)

Tags mark **specific commits**, usually for releases.

```bash
git tag v1.0
git tag
git push origin v1.0
```

---

# 14. Rebase (Advanced)

Rebase reapplies commits on top of another base commit, creating a **linear history**.

```bash
git rebase main
git rebase -i main
```

⚠ Never rebase shared branches

---

# 15. Git Aliases (Productivity)

Aliases create shortcuts for long commands, improving speed.

```bash
git config --global alias.st status
git config --global alias.cm commit
git config --global alias.co checkout
```

---

# 16. Git Cheat Sheet

Quick reference for daily usage.

### Daily

```bash
git status
git add .
git commit -m "msg"
git pull
git push
```

### Branching

```bash
git switch -c feature
git merge feature
```

### Undo

```bash
git restore <file>
git reset --hard
```

---

# 17. Best Practices (Official Recommendations)

Following these practices avoids common Git problems:

* Commit small, logical changes
* Write meaningful commit messages
* Pull before pushing
* Never commit secrets
* Use branches for features

---

# 18. Complete Practical Example (Official-Style Workflow)

This example demonstrates a **real-world Git workflow** combining branching, hotfixes, and merging.

### Scenario: Feature + Hotfix

```bash
git init mysite
cd mysite
echo "Home" > index.html
git add index.html
git commit -m "Initial commit"
```

Create feature branch:

```bash
git switch -c feature/contact
echo "Contact" > contact.html
git add contact.html
git commit -m "Add contact page"
```

Hotfix on main:

```bash
git switch main
git switch -c hotfix/404
# fix bug
git commit -am "Fix 404"
```

Merge hotfix and feature safely:

```bash
git switch main
git merge hotfix/404
git switch feature/contact
git merge main
```

This ensures **production stability** while feature work continues independently.
