# GitHub Troubleshooting Guide: Common Issues & Solutions

This comprehensive troubleshooting guide covers the most common GitHub issues you'll encounter during development, contribution, and repository management. Each problem includes step-by-step solutions and preventive measures.

---

## Table of Contents
1. [Authentication Issues](#authentication-issues)
2. [Repository Access Problems](#repository-access-problems)
3. [Git Operations Issues](#git-operations-issues)
4. [Pull Request Problems](#pull-request-problems)
5. [GitHub Actions Failures](#github-actions-failures)
6. [Branch and Merge Conflicts](#branch-and-merge-conflicts)
7. [Fork and Upstream Issues](#fork-and-upstream-issues)
8. [Security and Permissions](#security-and-permissions)
9. [Performance Issues](#performance-issues)
10. [API and Integration Problems](#api-and-integration-problems)

---

## 1. Authentication Issues

### **Problem: "Authentication failed" when pushing**
```
remote: Invalid username or password.
fatal: Authentication failed for 'https://github.com/user/repo.git/'
```

**Solutions:**

1. **Check credentials:**
   ```bash
   # For HTTPS
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"

   # Switch to SSH (recommended)
   git remote set-url origin git@github.com:user/repo.git
   ```

2. **Generate new SSH key:**
   ```bash
   ssh-keygen -t ed25519 -C "your.email@example.com"
   cat ~/.ssh/id_ed25519.pub  # Copy this to GitHub
   ssh -T git@github.com      # Test connection
   ```

3. **Use Personal Access Token:**
   ```bash
   # Create token at: https://github.com/settings/tokens
   git remote set-url origin https://USERNAME:TOKEN@github.com/user/repo.git
   ```

4. **Clear credential cache:**
   ```bash
   git config --global --unset credential.helper
   # Or on Windows: git config --global credential.helper wincred
   ```

### **Problem: "Permission denied (publickey)"**
```
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

**Solutions:**

1. **Verify SSH key is added to GitHub:**
   - Go to Settings → SSH and GPG keys
   - Ensure your public key is listed

2. **Check SSH agent:**
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ssh -T git@github.com
   ```

3. **Use correct SSH URL:**
   ```bash
   git remote set-url origin git@github.com:user/repo.git
   ```

### **Problem: Two-factor authentication issues**
**Solutions:**

1. **Use Personal Access Token instead of password**
2. **Configure Git credential helper:**
   ```bash
   git config --global credential.helper store
   # Enter username and token when prompted
   ```

---

## 2. Repository Access Problems

### **Problem: "Repository not found"**
```
fatal: repository 'https://github.com/user/repo.git/' not found
```

**Solutions:**

1. **Check repository URL:**
   ```bash
   git remote -v
   # Should show: origin https://github.com/OWNER/REPO.git
   ```

2. **Verify repository exists and is public:**
   - Check if repository is private (need access)
   - Verify spelling of owner/repo name

3. **Check organization permissions:**
   - Ensure you're added to the organization
   - Check if repository requires specific team membership

### **Problem: "You don't have permission to push to this repository"**
**Solutions:**

1. **Fork the repository:**
   ```bash
   gh repo fork owner/repo --clone=true
   cd repo
   git remote add upstream https://github.com/owner/repo.git
   ```

2. **Create a new branch for your changes:**
   ```bash
   git checkout -b feature/my-feature
   # Make changes, then push to your fork
   git push origin feature/my-feature
   ```

3. **Create PR from your fork to upstream**

### **Problem: Repository is archived or read-only**
**Solutions:**

1. **Check repository status on GitHub**
2. **Contact repository owner if you need write access**
3. **Fork active repositories instead**

---

## 3. Git Operations Issues

### **Problem: "Non-fast-forward" merge conflicts**
```
! [rejected] main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/user/repo.git'
```

**Solutions:**

1. **Pull latest changes first:**
   ```bash
   git pull --rebase origin main
   # Resolve any conflicts, then:
   git push origin main
   ```

2. **Force push (if you know what you're doing):**
   ```bash
   git push --force-with-lease origin main
   ```

3. **Create a new branch:**
   ```bash
   git checkout -b new-branch
   git push origin new-branch
   ```

### **Problem: Large file upload fails**
```
remote: error: GH001: Large files detected.
remote: error: File file.zip is 300.00 MB; this exceeds GitHub's file size limit of 100.00 MB
```

**Solutions:**

1. **Use Git LFS for large files:**
   ```bash
   git lfs install
   git lfs track "*.zip"
   git add .gitattributes
   git add large-file.zip
   git commit -m "Add large file"
   ```

2. **Remove large files from history:**
   ```bash
   # Use BFG Repo-Cleaner or git-filter-repo
   git filter-repo --strip-blobs-bigger-than 100M
   ```

3. **Use releases for large assets:**
   - Upload large files as release assets instead of in repository

### **Problem: Git ignore not working**
**Solutions:**

1. **Check .gitignore location:**
   - Must be in repository root
   - Check for multiple .gitignore files

2. **Files already tracked:**
   ```bash
   git rm --cached file-to-ignore
   git commit -m "Remove tracked file"
   ```

3. **Check syntax:**
   ```gitignore
   # Correct patterns
   *.log
   /build/
   !important.log  # Exception
   ```

---

## 4. Pull Request Problems

### **Problem: PR shows merge conflicts**
**Solutions:**

1. **Sync with base branch:**
   ```bash
   git checkout your-branch
   git fetch upstream
   git rebase upstream/main
   # Resolve conflicts
   git push origin your-branch --force
   ```

2. **Merge base branch:**
   ```bash
   git checkout your-branch
   git merge upstream/main
   # Resolve conflicts
   git push origin your-branch
   ```

3. **Create new PR if conflicts are severe**

### **Problem: PR checks failing**
**Common issues:**

1. **Linting errors:**
   ```bash
   # Run locally first
   npm run lint  # JavaScript
   black .       # Python
   ```

2. **Test failures:**
   ```bash
   npm test
   python -m pytest
   ```

3. **Check GitHub Actions logs for details**

### **Problem: PR automatically closed**
**Solutions:**

1. **Reopen if accidentally closed**
2. **Create new PR with updated changes:**
   ```bash
   git checkout main
   git pull upstream main
   git checkout -b new-feature-branch
   # Reapply changes
   git push origin new-feature-branch
   ```

### **Problem: Can't update PR branch**
**Solutions:**

1. **Allow maintainers to edit PR:**
   - Check "Allow edits from maintainers" in PR settings

2. **Push to your fork:**
   ```bash
   git push origin your-branch --force
   ```

---

## 5. GitHub Actions Failures

### **Problem: Actions not running**
**Solutions:**

1. **Check workflow syntax:**
   ```yaml
   # Common mistakes:
   - Wrong indentation
   - Missing required fields
   - Invalid action names
   ```

2. **Verify file location:**
   - Must be in `.github/workflows/`
   - Filename should end with `.yml` or `.yaml`

3. **Check repository settings:**
   - Actions enabled in repository settings
   - Branch protection rules not blocking

### **Problem: Actions failing with permission errors**
**Solutions:**

1. **Check GITHUB_TOKEN permissions:**
   ```yaml
   jobs:
     job-name:
       runs-on: ubuntu-latest
       permissions:
         contents: read
         issues: write
         pull-requests: write
   ```

2. **Use repository secrets for sensitive operations**

### **Problem: Actions running slowly**
**Solutions:**

1. **Add caching:**
   ```yaml
   - name: Cache dependencies
     uses: actions/cache@v3
     with:
       path: ~/.npm
       key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
   ```

2. **Use faster runners:**
   ```yaml
   runs-on: ubuntu-latest  # Instead of windows-latest
   ```

3. **Parallelize jobs:**
   ```yaml
   strategy:
     matrix:
       node-version: [16, 18]
   ```

---

## 6. Branch and Merge Conflicts

### **Problem: Merge conflict resolution**
**Solutions:**

1. **Understand conflict markers:**
   ```
   <<<<<<< HEAD
   Your changes
   =======
   Incoming changes
   >>>>>>> branch-name
   ```

2. **Resolve conflicts:**
   ```bash
   # Edit files to resolve conflicts
   git add resolved-file
   git commit
   ```

3. **Use merge tools:**
   ```bash
   git mergetool  # Configure with your preferred tool
   ```

### **Problem: Branch out of sync**
**Solutions:**

1. **Rebase on latest:**
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Merge latest:**
   ```bash
   git merge upstream/main
   ```

3. **Reset if needed:**
   ```bash
   git reset --hard upstream/main
   ```

---

## 7. Fork and Upstream Issues

### **Problem: Fork not syncing with upstream**
**Solutions:**

1. **Set up upstream remote:**
   ```bash
   git remote add upstream https://github.com/ORIGINAL_OWNER/REPO.git
   ```

2. **Sync fork:**
   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   git push origin main
   ```

3. **Sync all branches:**
   ```bash
   git fetch upstream
   git push origin upstream/main:main
   ```

### **Problem: Can't push to upstream**
**Solutions:**

1. **You don't have push access to upstream**
2. **Always push to your fork first:**
   ```bash
   git push origin your-branch
   ```

3. **Create PR to upstream**

---

## 8. Security and Permissions

### **Problem: "Repository ruleset violation"**
**Solutions:**

1. **Check repository rules:**
   - Branch protection rules
   - Required status checks
   - Code scanning alerts

2. **Fix security issues:**
   - Update dependencies
   - Fix code scanning alerts
   - Address Dependabot alerts

### **Problem: Dependabot failing**
**Solutions:**

1. **Check configuration:**
   ```yaml
   # .github/dependabot.yml
   version: 2
   updates:
     - package-ecosystem: "npm"
       directory: "/"
       schedule:
         interval: "weekly"
   ```

2. **Fix dependency conflicts**
3. **Update to compatible versions**

---

## 9. Performance Issues

### **Problem: Slow repository operations**
**Solutions:**

1. **Optimize repository size:**
   ```bash
   # Remove large files
   git filter-repo --strip-blobs-bigger-than 100M

   # Clean up
   git gc --aggressive --prune=now
   ```

2. **Use shallow clones:**
   ```bash
   git clone --depth 1 https://github.com/user/repo.git
   ```

3. **Enable Git LFS for large assets**

### **Problem: GitHub Pages not updating**
**Solutions:**

1. **Wait for build (can take 10-20 minutes)**
2. **Check build status in repository settings**
3. **Verify source branch and folder**
4. **Check for Jekyll build errors**

---

## 10. API and Integration Problems

### **Problem: GitHub API rate limiting**
**Solutions:**

1. **Use authentication:**
   ```bash
   export GITHUB_TOKEN=your_token
   ```

2. **Implement retry logic:**
   ```python
   import time
   import requests

   def api_call(url):
       response = requests.get(url, headers={'Authorization': f'token {token}'})
       if response.status_code == 403:
           time.sleep(60)  # Wait for rate limit reset
           return api_call(url)
       return response
   ```

3. **Use GraphQL API for efficiency**

### **Problem: Webhook deliveries failing**
**Solutions:**

1. **Check webhook URL:**
   - Must be publicly accessible
   - Use HTTPS

2. **Verify payload:**
   - Check webhook secret
   - Validate signature

3. **Check recent deliveries in webhook settings**

### **Problem: GitHub CLI authentication**
**Solutions:**

1. **Authenticate CLI:**
   ```bash
   gh auth login
   ```

2. **Check token scopes:**
   - Ensure token has required permissions

3. **Re-authenticate if token expired**

---

## Diagnostic Tools

### **Git Status Check**
```bash
# Check repository status
git status
git remote -v
git log --oneline -5

# Check GitHub connection
ssh -T git@github.com
gh auth status
```

### **Network Diagnostics**
```bash
# Test connectivity
ping github.com
traceroute github.com

# Check DNS
nslookup github.com
```

### **Repository Health Check**
```bash
# Check for issues
gh repo view owner/repo --json issues
gh pr list --state open
gh issue list --state open
```

---

## Prevention Best Practices

### **1. Regular Maintenance**
- Keep dependencies updated
- Monitor security alerts
- Review repository settings periodically
- Clean up old branches

### **2. Proper Setup**
- Use SSH instead of HTTPS
- Configure proper Git settings
- Set up branch protection rules
- Use issue and PR templates

### **3. Backup Strategy**
- Keep local copies of important work
- Use multiple remotes if needed
- Document your workflow

### **4. Monitoring**
- Set up notifications for important events
- Monitor repository analytics
- Review failed CI/CD runs promptly

---

## Emergency Recovery

### **Lost Commits Recovery**
```bash
# Find lost commits
git reflog
git checkout <commit-hash>

# Create new branch from lost commit
git checkout -b recovery-branch <commit-hash>
```

### **Repository Corruption**
```bash
# Fix corrupted repository
git fsck
git gc --prune=now

# If severe, re-clone
git clone https://github.com/user/repo.git repo-new
```

### **Accidental Force Push**
```bash
# Recover from force push (if you have the commits)
git push origin <old-commit>:branch-name --force
```

---

## Getting Help

### **Community Resources**
- [GitHub Community Forum](https://github.community/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/github)
- [GitHub Documentation](https://docs.github.com/)
- [GitHub CLI Manual](https://cli.github.com/manual/)

### **Professional Support**
- GitHub Enterprise support (for organizations)
- Third-party Git hosting services
- Professional consulting services

---

## Quick Reference Commands

```bash
# Authentication
gh auth login                    # Authenticate GitHub CLI
ssh -T git@github.com           # Test SSH connection
git config --global user.name   # Set Git username

# Repository operations
git remote -v                   # Check remotes
git fetch upstream              # Fetch upstream changes
git rebase upstream/main        # Rebase on upstream

# Branch management
git branch -a                   # List all branches
git checkout -b new-branch      # Create new branch
git push origin --delete branch # Delete remote branch

# PR operations
gh pr create                    # Create PR
gh pr list                      # List PRs
gh pr merge                     # Merge PR

# Troubleshooting
git fsck                        # Check repository integrity
git gc --aggressive             # Clean repository
gh repo view                    # Check repository status
```

---

*Remember: When in doubt, check the GitHub status page at [githubstatus.com](https://www.githubstatus.com/) to see if there are any ongoing incidents affecting service availability.*