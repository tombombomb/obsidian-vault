# Git Commands Guide

A reference guide based on learning Git through hands-on practice.

---

## Table of Contents

1. [Setting Up a Repository](#setting-up-a-repository)
2. [Basic Workflow](#basic-workflow)
3. [Working with Remotes](#working-with-remotes)
4. [Pushing to GitHub](#pushing-to-github)
5. [Cloning Repositories](#cloning-repositories)
6. [Undoing Things](#undoing-things)
7. [Common Errors and Solutions](#common-errors-and-solutions)

---

## Setting Up a Repository

### Initialize a new Git repository

```bash
git init
```

Creates a new `.git` subdirectory in your current folder, which contains all the necessary Git repository files. This is the first step to start tracking a project with Git.

**Variations:**

```bash
# Initialize in a specific directory
git init /path/to/directory

# Initialize with a specific branch name (e.g., "main" instead of "master")
git init -b main
```

### Configure your identity

```bash
git config user.name "Your Name"
git config user.email "your@email.com"
```

Sets your name and email for commits. Add `--global` flag to set for all repositories.

---

## Basic Workflow

### Check repository status

```bash
git status
```

Shows the current state of your working directory and staging area.

### Stage files for commit

```bash
# Stage all changes
git add .

# Stage a specific file
git add filename.txt
```

Adds files to the staging area, preparing them for commit.

### Commit changes

```bash
git commit -m "Your commit message"
```

Records the staged changes to the repository with a descriptive message.

**Important:** You must stage files with `git add` before committing. If you try to commit without staging:

```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
```

### View commit history

```bash
git log
```

Shows the commit history for the repository.

### View branches

```bash
git branch
```

Lists all local branches. The current branch is marked with an asterisk.

---

## Working with Remotes

### Add a remote repository

```bash
# Using HTTPS
git remote add origin https://github.com/username/repo-name.git

# Using SSH (recommended)
git remote add origin git@github.com:username/repo-name.git
```

Links your local repository to a remote repository (like GitHub).

### View configured remotes

```bash
git remote -v
```

Shows all configured remote repositories and their URLs.

### Remove a remote

```bash
git remote remove origin
```

Removes the remote named "origin". Useful when you need to change the remote URL.

### Change remote URL

```bash
git remote set-url origin git@github.com:username/new-repo.git
```

Updates the URL for an existing remote without removing and re-adding it.

---

## Pushing to GitHub

### Push to remote repository

```bash
# First push (sets upstream tracking)
git push -u origin master

# Subsequent pushes (after -u flag was used)
git push
```

The `-u` flag sets the upstream branch, so future pushes only need `git push`.

### Rename branch before pushing

```bash
# Rename current branch to "main"
git branch -M main

# Then push
git push -u origin main
```

---

## Cloning Repositories

### Clone a repository

```bash
# Clone to a new directory (named after the repo)
git clone git@github.com:username/repo-name.git

# Clone to a specific directory name
git clone git@github.com:username/repo-name.git my-folder-name

# Clone to current directory (must be empty)
git clone git@github.com:username/repo-name.git .
```

Downloads a repository and its entire history.

**Useful options:**

```bash
# Clone only the latest commit (faster, smaller download)
git clone --depth 1 https://github.com/username/repo-name.git

# Clone a specific branch
git clone -b branch-name https://github.com/username/repo-name.git
```

---

## Undoing Things

### Undo git init

```bash
rm -rf .git
```

Removes all Git tracking from a directory. **Warning:** This deletes all commits and history permanently. Your files remain, but Git metadata is gone.

### Discard changes to a file

```bash
git restore filename.txt
```

Reverts a file to its last committed state.

---

## Common Errors and Solutions

### "fatal: not a git repository"

```
fatal: not a git repository (or any of the parent directories): .git
```

**Cause:** You're trying to run Git commands in a directory that hasn't been initialized.

**Solution:** Run `git init` first.

---

### "error: remote origin already exists"

```
error: remote origin already exists.
```

**Cause:** You're trying to add a remote named "origin" but one already exists.

**Solution:** Remove the existing remote first, then add the new one:

```bash
git remote remove origin
git remote add origin git@github.com:username/repo.git
```

Or update the URL directly:

```bash
git remote set-url origin git@github.com:username/repo.git
```

---

### "error: src refspec main does not match any"

```
error: src refspec main does not match any
error: failed to push some refs to 'github.com:username/repo.git'
```

**Cause:** Either:
- No commits exist yet
- The branch name doesn't match (e.g., trying to push "main" when branch is "master")

**Solution:**

```bash
# Check your branch name
git branch

# If branch is "master", push to master
git push -u origin master

# Or rename to main first
git branch -M main
git push -u origin main
```

---

### "Repository not found" or "Could not read from remote repository"

```
ERROR: Repository not found.
fatal: Could not read from remote repository.
```

**Cause:** Either:
- The repository doesn't exist on GitHub
- The URL is incorrect (typo in username or repo name)
- You don't have access rights

**Solution:**

1. Check the remote URL: `git remote -v`
2. Verify the repo exists on GitHub
3. Check for typos in username/repo name
4. Test SSH connection: `ssh -T git@github.com`

---

### "Password authentication is not supported"

```
remote: Invalid username or token. Password authentication is not supported for Git operations.
```

**Cause:** GitHub no longer accepts passwords for HTTPS authentication.

**Solution:** Use SSH instead of HTTPS:

```bash
git remote remove origin
git remote add origin git@github.com:username/repo.git
```

Or use a Personal Access Token (PAT) instead of password.

---

### "nothing to commit, working tree clean"

```
On branch master
nothing to commit, working tree clean
```

**Cause:** No changes have been made since the last commit.

**Solution:** This isn't an error—it means everything is already committed.

---

## SSH Setup for GitHub

### Test SSH connection

```bash
ssh -T git@github.com
```

Expected success message:
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

### Generate SSH key (if needed)

```bash
ssh-keygen -t ed25519 -C "your@email.com"
```

### View your public key

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy this and add it to GitHub: Settings → SSH and GPG keys → New SSH key

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `git init` | Initialize a new repository |
| `git clone <url>` | Clone a repository |
| `git status` | Check current status |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Commit staged changes |
| `git push` | Push to remote |
| `git pull` | Pull from remote |
| `git branch` | List branches |
| `git remote -v` | View remotes |
| `git remote remove origin` | Remove remote |
| `git log` | View commit history |

---

*Generated from a hands-on Git learning session.*
