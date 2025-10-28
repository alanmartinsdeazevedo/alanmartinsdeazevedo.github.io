---
layout: article
title: Essential Git Workflows
description: Common Git workflows and best practices for version control
category: Tools & CLI
date: 2025-10-28
---

## Overview

Git is the standard version control system for modern software development. This guide covers essential workflows and best practices.

## Basic Workflow

### Initialize a Repository

```bash
# Start a new repository
git init

# Clone an existing repository
git clone https://github.com/user/repo.git
```

### Daily Workflow

```bash
# Check status
git status

# Add files to staging
git add file.txt
git add .  # Add all files

# Commit changes
git commit -m "Add feature X"

# Push to remote
git push origin main
```

## Branching Strategy

### Creating and Switching Branches

```bash
# Create a new branch
git branch feature-name

# Switch to branch
git checkout feature-name

# Create and switch in one command
git checkout -b feature-name

# Modern syntax (Git 2.23+)
git switch -c feature-name
```

### Feature Branch Workflow

```bash
# Start new feature
git checkout -b feature/user-authentication

# Work on feature
git add .
git commit -m "Implement user login"

# Push to remote
git push origin feature/user-authentication

# After code review, merge to main
git checkout main
git merge feature/user-authentication
git push origin main

# Delete feature branch
git branch -d feature/user-authentication
git push origin --delete feature/user-authentication
```

## Keeping Your Branch Updated

### Rebase vs Merge

**Using merge:**
```bash
git checkout feature-branch
git merge main
```

**Using rebase (cleaner history):**
```bash
git checkout feature-branch
git rebase main
```

### Pull with Rebase

```bash
git pull --rebase origin main
```

## Undoing Changes

### Unstage Files

```bash
# Unstage specific file
git reset HEAD file.txt

# Unstage all files
git reset HEAD
```

### Discard Local Changes

```bash
# Discard changes in working directory
git checkout -- file.txt

# Discard all local changes
git checkout -- .

# Modern syntax
git restore file.txt
```

### Undo Last Commit

```bash
# Keep changes in working directory
git reset --soft HEAD~1

# Discard changes completely
git reset --hard HEAD~1
```

### Revert a Commit

```bash
# Create a new commit that undoes changes
git revert commit-hash
```

## Stashing Changes

Save work in progress without committing:

```bash
# Stash current changes
git stash

# Stash with message
git stash save "Work in progress on feature X"

# List stashes
git stash list

# Apply most recent stash
git stash apply

# Apply and remove stash
git stash pop

# Apply specific stash
git stash apply stash@{2}

# Delete stash
git stash drop stash@{0}
```

## Viewing History

```bash
# View commit history
git log

# One line per commit
git log --oneline

# Show last 5 commits
git log -5

# Show commits with changes
git log -p

# Show commits by author
git log --author="Name"

# Graphical view of branches
git log --oneline --graph --all
```

## Collaborating

### Fetch vs Pull

```bash
# Download changes without merging
git fetch origin

# Download and merge changes
git pull origin main
```

### Pull Requests (PRs)

1. Create feature branch
2. Push branch to remote
3. Open PR on GitHub/GitLab
4. Code review
5. Merge PR
6. Delete feature branch

## Resolving Merge Conflicts

When conflicts occur:

```bash
# See conflicted files
git status

# Edit conflicted files (marked with <<<<<<<, =======, >>>>>>>)

# After resolving
git add resolved-file.txt
git commit -m "Resolve merge conflict"
```

## Useful Aliases

Add to `~/.gitconfig`:

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = log --oneline --graph --all
```

## Best Practices

### Commit Messages

```bash
# Good commit message format
git commit -m "Add user authentication module

- Implement login endpoint
- Add JWT token generation
- Create user session management"
```

### Commit Often

- Make small, focused commits
- Each commit should represent one logical change
- Easier to review and revert if needed

### Keep History Clean

- Use `git rebase -i` to squash commits before merging
- Don't commit commented-out code or TODO comments
- Remove debug code before committing

## .gitignore Examples

Create `.gitignore` in project root:

```bash
# Node.js
node_modules/
npm-debug.log
.env

# Java
*.class
*.jar
target/

# Python
__pycache__/
*.py[cod]
venv/

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db
```

## Troubleshooting

### Accidentally Committed Sensitive Data

```bash
# Remove file from history
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/sensitive-file" \
  --prune-empty --tag-name-filter cat -- --all

# Force push (use with caution!)
git push origin --force --all
```

### Recover Deleted Branch

```bash
# Find deleted branch commit
git reflog

# Recreate branch
git checkout -b recovered-branch commit-hash
```

## Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Pro Git Book (Free)](https://git-scm.com/book/en/v2)
- [GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Interactive Git Learning](https://learngitbranching.js.org/)
