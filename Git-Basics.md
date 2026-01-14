# Git Basic Commands

Essential Git commands for everyday development work.

---

## Repository Setup

### Initialize a Repository
```bash
git init
```
Creates a new Git repository in the current directory (creates a hidden `.git` folder)

### Clone a Repository
```bash
git clone <repository-url>
```
Copies an existing repository to your local machine

---

## Checking Status & History

### Check Repository Status
```bash
git status
```
Shows tracked/untracked files and staged changes

### View Commit History
```bash
git log
```
Displays commit history with author, date, and message details

```bash
git log --oneline
```
Shows a compact view of commits (one line per commit)

---

## Staging & Committing Changes

### Stage Files
```bash
git add <filename>
```
Stages a specific file for the next commit

```bash
git add .
```
Stages all changes in the working directory

### Unstage Files
```bash
git reset <filename>
```
Unstages a file while preserving changes in working directory

### Commit Changes
```bash
git commit -m "Your commit message"
```
Records the current project state with a description

### Undo Last Commit
```bash
git reset --soft HEAD~1
```
Reverts the last commit but keeps changes staged

```bash
git reset --hard HEAD~1
```
Removes the last commit and all changes permanently ⚠️ **Use with caution!**

---

## Branch Management

### Create a Branch
```bash
git branch <branch-name>
```
Creates a new branch

### List All Branches
```bash
git branch
```
Shows all local branches (current branch marked with *)

### Switch Branches
```bash
git checkout <branch-name>
```
Switches to a specified branch (older method)

```bash
git switch <branch-name>
```
Modern alternative for switching branches (Git 2.23+)

### Create and Switch to New Branch
```bash
git checkout -b <branch-name>
```
Creates and switches to a new branch in one command

```bash
git switch -c <branch-name>
```
Modern alternative (Git 2.23+)

### Merge Branches
```bash
git merge <branch-name>
```
Integrates changes from another branch into your current branch

### Delete a Branch
```bash
git branch -d <branch-name>
```
Deletes a merged branch

```bash
git branch -D <branch-name>
```
Force deletes a branch (even if not merged) ⚠️ **Use with caution!**

---

## Remote Repository Operations

### Add a Remote
```bash
git remote add origin <repository-url>
```
Links your local repository to a remote repository

### View Remotes
```bash
git remote -v
```
Lists all configured remote repositories

### Push Changes
```bash
git push origin <branch-name>
```
Uploads local commits to the remote repository

```bash
git push -u origin <branch-name>
```
Pushes and sets upstream tracking for the branch

### Pull Changes
```bash
git pull origin <branch-name>
```
Downloads and merges remote changes into your current branch

### Fetch Changes
```bash
git fetch
```
Downloads changes from remote without merging

---

## Quick Reference Workflow

**Starting a new project:**
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <url>
git push -u origin main
```

**Daily workflow:**
```bash
git status                    # Check what changed
git add .                     # Stage changes
git commit -m "Description"   # Commit changes
git pull origin main          # Get latest changes
git push origin main          # Upload your changes
```

**Working with branches:**
```bash
git checkout -b feature-branch  # Create & switch to new branch
# ... make changes ...
git add .
git commit -m "Add feature"
git checkout main               # Switch back to main
git merge feature-branch        # Merge feature into main
git branch -d feature-branch    # Delete feature branch
```

---

*Last updated: 2026-01-14*
