# Git & GitHub Cheat Sheet

## Initial Setup

```bash
# Configure git with your details
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Check your configuration
git config --list
```

## SSH Key Setup (for private repos)

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Display public key (copy this to GitHub)
cat ~/.ssh/id_ed25519.pub

# Test connection
ssh -T git@github.com
```

**Add key to GitHub:** Settings → SSH and GPG keys → New SSH key

## Repository Basics

```bash
# Initialize a new repository
git init

# Clone a repository (HTTPS)
git clone https://github.com/username/repo.git

# Clone a repository (SSH - for private repos)
git clone git@github.com:username/repo.git

# Check repository status
git status

# View commit history
git log
git log --oneline  # Condensed view
```

## Basic Workflow

```bash
# Stage files for commit
git add filename.js           # Add specific file
git add .                     # Add all changes
git add *.js                  # Add all JS files

# Commit changes
git commit -m "Description of changes"

# Push to GitHub
git push origin main
git push origin branch-name   # Push specific branch

# Pull latest changes
git pull origin main
```

## Branching

```bash
# View branches
git branch                    # Local branches
git branch -a                 # All branches (local + remote)

# Create new branch
git branch feature-name

# Switch to branch
git checkout feature-name

# Create and switch in one command
git checkout -b feature-name

# Switch back to main
git checkout main
```

## Merging Branches

```bash
# Standard merge workflow
git checkout main             # Switch to main
git pull origin main          # Update main (optional but recommended)
git merge feature-name        # Merge feature into main
git push origin main          # Push merged changes

# Delete branch after merging
git branch -d feature-name              # Delete local
git push origin --delete feature-name   # Delete remote
```

## Undoing Changes

```bash
# Discard unstaged changes in a file
git checkout -- filename.js

# Unstage a file (keep changes)
git reset HEAD filename.js

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1
```

## Viewing Changes

```bash
# See what changed (unstaged)
git diff

# See what changed (staged)
git diff --staged

# See changes in specific file
git diff filename.js
```

## Remote Repository

```bash
# View remote connections
git remote -v

# Add remote repository
git remote add origin https://github.com/username/repo.git

# Change remote URL
git remote set-url origin git@github.com:username/repo.git
```

## Common Workflows

### Creating a new repository from scratch

```bash
# On your computer
mkdir my-project
cd my-project
git init
echo "# My Project" > README.md
git add .
git commit -m "initial commit"

# Create repo on GitHub, then:
git remote add origin git@github.com:username/my-project.git
git push -u origin main
```

### Feature branch workflow

```bash
# Create feature branch
git checkout -b new-feature

# Make changes and commit
git add .
git commit -m "added new feature"

# Push feature branch
git push origin new-feature

# Merge when ready
git checkout main
git pull origin main
git merge new-feature
git push origin main

# Cleanup
git branch -d new-feature
git push origin --delete new-feature
```

### Syncing with remote changes

```bash
# Get latest from GitHub
git fetch origin

# Update current branch
git pull origin main

# Update all branches
git pull --all
```

## Useful Tips

- Always commit with clear, descriptive messages
- Pull before you push to avoid conflicts
- Use branches for new features or experiments
- The branch with `*` in `git branch` is your current branch
- SSH URLs start with `git@github.com:`
- HTTPS URLs start with `https://github.com/`

## Quick Reference

| Command | What it does |
|---------|-------------|
| `git status` | Check what's changed |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Save changes |
| `git push` | Upload to GitHub |
| `git pull` | Download from GitHub |
| `git branch` | List branches |
| `git checkout name` | Switch branch |
| `git merge name` | Merge branch into current |
| `git log` | View history |

## Common Issues

**"Permission denied" when pushing:**
- Check SSH key is added to GitHub
- Verify with `ssh -T git@github.com`

**Merge conflicts:**
- Open conflicted files
- Look for `<<<<<<<`, `=======`, `>>>>>>>` markers
- Edit to resolve, then `git add` and `git commit`

**Wrong branch:**
- Check current branch: `git branch`
- Switch: `git checkout correct-branch`
