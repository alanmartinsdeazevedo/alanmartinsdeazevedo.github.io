---
layout: article
title: "Changing Git Remote Repository URL"
description: How to update the remote origin URL of an already cloned Git repository without re-cloning
category: Tools & CLI
date: 2025-10-28
---

## Overview

When a Git repository's domain or hosting platform changes, you don't need to clone it again. You can simply update the remote URL in your local repository configuration.

## Common Scenarios

- Company domain migration
- Moving from one Git hosting service to another (GitLab → GitHub, Bitbucket → GitLab, etc.)
- Switching from HTTP to SSH authentication
- Repository transfer to different organization/user
- Self-hosted Git server domain change

## Quick Solution

### Step 1: Check Current Remote URL

Navigate to your repository folder and run:

```bash
git remote -v
```

Output example:
```
origin  https://old-domain.com/company/repository.git (fetch)
origin  https://old-domain.com/company/repository.git (push)
```

### Step 2: Update Remote URL

```bash
git remote set-url origin https://new-domain.com/company/repository.git
```

### Step 3: Verify Changes

```bash
git remote -v
```

Expected output:
```
origin  https://new-domain.com/company/repository.git (fetch)
origin  https://new-domain.com/company/repository.git (push)
```

### Step 4: Test Connection

```bash
git fetch origin
```

If successful, the remote URL is correctly configured.

## Detailed Examples

### Example 1: HTTPS URL Change

**Scenario**: Company migrated from GitLab to GitHub

```bash
# Check current URL
git remote -v
# origin  https://gitlab.oldcompany.com/dev/project.git (fetch)
# origin  https://gitlab.oldcompany.com/dev/project.git (push)

# Update to new URL
git remote set-url origin https://github.com/newcompany/project.git

# Verify
git remote -v
# origin  https://github.com/newcompany/project.git (fetch)
# origin  https://github.com/newcompany/project.git (push)
```

### Example 2: SSH URL Change

**Scenario**: Self-hosted GitLab domain changed

```bash
# Check current URL
git remote -v
# origin  git@git.oldserver.com:team/repo.git (fetch)
# origin  git@git.oldserver.com:team/repo.git (push)

# Update to new URL
git remote set-url origin git@git.newserver.com:team/repo.git

# Verify
git remote -v
# origin  git@git.newserver.com:team/repo.git (fetch)
# origin  git@git.newserver.com:team/repo.git (push)
```

### Example 3: Switching from HTTPS to SSH

**Scenario**: Want to use SSH keys instead of password authentication

```bash
# Current HTTPS URL
git remote -v
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)

# Switch to SSH
git remote set-url origin git@github.com:user/repo.git

# Verify
git remote -v
# origin  git@github.com:user/repo.git (fetch)
# origin  git@github.com:user/repo.git (push)
```

### Example 4: Switching from SSH to HTTPS

**Scenario**: SSH keys not configured on new machine

```bash
# Current SSH URL
git remote -v
# origin  git@github.com:user/repo.git (fetch)
# origin  git@github.com:user/repo.git (push)

# Switch to HTTPS
git remote set-url origin https://github.com/user/repo.git

# Verify
git remote -v
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)
```

## Advanced Usage

### Multiple Remote Repositories

If you have multiple remotes (origin, upstream, etc.):

```bash
# List all remotes
git remote -v

# Update specific remote
git remote set-url origin https://new-url.com/repo.git
git remote set-url upstream https://another-url.com/repo.git

# Add new remote
git remote add backup https://backup-server.com/repo.git

# Remove remote
git remote remove backup
```

### Update Only Push or Fetch URL

In rare cases, you might want different URLs for push and fetch:

```bash
# Set different push URL
git remote set-url --push origin https://push-server.com/repo.git

# Verify
git remote -v
# origin  https://fetch-server.com/repo.git (fetch)
# origin  https://push-server.com/repo.git (push)
```

### Rename Remote

```bash
# Rename 'origin' to 'upstream'
git remote rename origin upstream

# Verify
git remote -v
```

## Common URL Formats

### HTTPS Format

```
https://domain.com/username/repository.git
https://domain.com/organization/repository.git
```

**Common platforms:**
- GitHub: `https://github.com/user/repo.git`
- GitLab: `https://gitlab.com/user/repo.git`
- Bitbucket: `https://bitbucket.org/user/repo.git`
- Azure DevOps: `https://dev.azure.com/org/project/_git/repo`

### SSH Format

```
git@domain.com:username/repository.git
ssh://git@domain.com/username/repository.git
```

**Common platforms:**
- GitHub: `git@github.com:user/repo.git`
- GitLab: `git@gitlab.com:user/repo.git`
- Bitbucket: `git@bitbucket.org:user/repo.git`
- Azure DevOps: `git@ssh.dev.azure.com:v3/org/project/repo`

## Troubleshooting

### Error: Repository not found

If you get this error after changing URL:

```bash
# Verify URL is correct
git remote -v

# Test connection
git ls-remote origin

# Check authentication
git fetch origin
```

**Solutions:**
- Verify the new URL is correct
- Check if you have access to the repository
- Ensure credentials are updated
- For SSH, verify SSH keys are configured

### Error: Permission denied (publickey)

For SSH connections:

```bash
# Test SSH connection
ssh -T git@github.com  # For GitHub
ssh -T git@gitlab.com  # For GitLab

# Check SSH keys
ls -la ~/.ssh

# Generate new SSH key if needed
ssh-keygen -t ed25519 -C "your.email@example.com"

# Add SSH key to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Error: Authentication failed

For HTTPS connections:

```bash
# Clear cached credentials
git credential reject

# Update credentials on next operation
git fetch origin
```

On Windows, you may need to update credentials in Windows Credential Manager.

### Verify Remote Configuration

```bash
# Show remote configuration details
git config --get remote.origin.url

# Show all git configuration
git config --list | grep remote

# Edit git config manually (if needed)
git config --edit
```

## Batch Update Multiple Repositories

If you need to update multiple repositories:

### Bash Script (Linux/Mac)

```bash
#!/bin/bash

OLD_DOMAIN="old-domain.com"
NEW_DOMAIN="new-domain.com"

# Find all git repositories
find . -name ".git" -type d | while read git_dir; do
  repo_dir=$(dirname "$git_dir")
  echo "Updating: $repo_dir"

  cd "$repo_dir"

  # Get current URL
  current_url=$(git config --get remote.origin.url)

  # Replace domain
  new_url=$(echo "$current_url" | sed "s/$OLD_DOMAIN/$NEW_DOMAIN/g")

  # Update URL
  git remote set-url origin "$new_url"

  echo "Updated to: $new_url"
  echo "---"

  cd - > /dev/null
done
```

### PowerShell Script (Windows)

```powershell
$OldDomain = "old-domain.com"
$NewDomain = "new-domain.com"

Get-ChildItem -Directory -Recurse -Filter ".git" | ForEach-Object {
    $repoDir = $_.Parent.FullName
    Write-Host "Updating: $repoDir"

    Push-Location $repoDir

    $currentUrl = git config --get remote.origin.url
    $newUrl = $currentUrl -replace $OldDomain, $NewDomain

    git remote set-url origin $newUrl

    Write-Host "Updated to: $newUrl"
    Write-Host "---"

    Pop-Location
}
```

## Related Commands

### View Remote Details

```bash
# Show remote with full details
git remote show origin

# Get specific remote URL
git config --get remote.origin.url

# List all remotes with their URLs
git remote -v
```

### Working with Multiple Remotes

```bash
# Add additional remote
git remote add upstream https://github.com/original/repo.git

# Fetch from specific remote
git fetch upstream

# Pull from specific remote
git pull upstream main

# Push to specific remote
git push origin main
```

## Best Practices

1. **Always verify the URL before updating**: Copy the exact URL from your Git hosting platform
2. **Test after changing**: Run `git fetch` to ensure connection works
3. **Update credentials if needed**: HTTPS may require password/token update
4. **Keep SSH keys updated**: Ensure your public key is added to the new platform
5. **Document changes**: Inform team members about URL changes
6. **Update CI/CD configurations**: Don't forget to update deployment pipelines

## Configuration Files

Git stores remote configuration in:

```bash
# Repository-specific configuration
.git/config

# View remote section
cat .git/config | grep -A 2 "\[remote"
```

Example `.git/config` content:

```ini
[remote "origin"]
    url = https://github.com/user/repo.git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

## Security Considerations

### Using SSH (Recommended)

```bash
# More secure than HTTPS
git remote set-url origin git@github.com:user/repo.git

# Benefits:
# - No password prompts
# - Key-based authentication
# - More secure than token/password
```

### Using HTTPS with Personal Access Token

```bash
# For GitHub, GitLab, etc.
git remote set-url origin https://username:TOKEN@github.com/user/repo.git

# Better approach: Use credential helper
git config --global credential.helper store
```

## Additional Resources

- [Git Remote Documentation](https://git-scm.com/docs/git-remote)
- [GitHub: Changing a remote's URL](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories)
- [GitLab: Remote repositories](https://docs.gitlab.com/ee/user/project/working_with_projects.html)

## Summary

Changing a Git remote URL is a simple process that doesn't require re-cloning:

1. Check current URL: `git remote -v`
2. Update URL: `git remote set-url origin <new-url>`
3. Verify change: `git remote -v`
4. Test connection: `git fetch origin`

No data is lost in this process, and all your local branches, commits, and history remain intact.
