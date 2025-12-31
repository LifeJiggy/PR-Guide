# Force Push: When and How to Use Safely

Force pushing is one of the most powerful and dangerous git operations. This guide explains when to use force push, how to do it safely, and best practices to avoid common pitfalls.

## ⚠️ **Force Push Warning**

**Force push rewrites history on the remote repository.** This can cause:
- Loss of commits for other contributors
- Broken workflows and CI/CD pipelines
- Confusion and merge conflicts for team members
- Potential data loss

**Always prefer `--force-with-lease` over `--force`**

## 🔒 **Safe Force Push: --force-with-lease**

### **Basic Usage**
```bash
# Safe force push current branch
git push --force-with-lease origin main

# Safe force push specific branch
git push --force-with-lease origin feature-branch

# Safe force push with verbose output
git push --force-with-lease -v origin main
```

### **How --force-with-lease Works**
- Checks if remote branch is at expected state
- Only pushes if no one else has pushed since your last fetch
- Prevents accidental overwrites of others' work
- Fails safely if remote has been updated

## 🚫 **Unsafe Force Push: --force (Avoid)**

### **When NOT to Use --force**
```bash
# DANGER: Overwrites without checking
git push --force origin main

# DANGER: Mirror push (overwrites everything)
git push --mirror origin
```

## ✅ **When to Use Force Push**

### **1. After Rebasing Local Commits**
```bash
# Scenario: You rebased local commits to clean history
git checkout feature-branch
git rebase -i HEAD~3  # Interactive rebase to clean commits
git push --force-with-lease origin feature-branch
```

### **2. Fixing Commit History Mistakes**
```bash
# Scenario: Fix commit message typo
git commit --amend -m "Fixed typo in commit message"
git push --force-with-lease origin feature-branch
```

### **3. Squashing Commits Before PR**
```bash
# Scenario: Squash multiple commits into one
git rebase -i HEAD~5
# Mark commits as 'squash' or 'fixup'
git push --force-with-lease origin feature-branch
```

### **4. Syncing After Upstream Rebase**
```bash
# Scenario: Upstream rebased their main branch
git checkout main
git pull upstream main  # This may rebase
git checkout feature-branch
git rebase main
git push --force-with-lease origin feature-branch
```

### **5. Cleaning Up Personal Branches**
```bash
# Scenario: Reset branch to match main exactly
git checkout feature-branch
git reset --hard main
# Make only the desired changes
git push --force-with-lease origin feature-branch
```

## 🔄 **Common Force Push Workflows**

### **Rebase and Force Push Pattern**
```bash
# Keep feature branch updated with main
git checkout main
git pull upstream main
git checkout feature-branch
git rebase main

# If conflicts resolved, force push
git push --force-with-lease origin feature-branch
```

### **Clean Commit History Pattern**
```bash
# Interactive rebase to clean history
git rebase -i HEAD~10

# Edit commits, squash, reword as needed
# Then force push the cleaned history
git push --force-with-lease origin feature-branch
```

### **Fixup Commits Pattern**
```bash
# Create fixup commits
git commit --fixup HEAD~2

# Auto-squash and force push
git rebase -i --autosquash HEAD~5
git push --force-with-lease origin feature-branch
```

## 🛡️ **Safety Checks Before Force Push**

### **Pre-Flight Checklist**
```bash
# 1. Check what you're about to push
git log --oneline origin/feature-branch..HEAD

# 2. Ensure no one else is working on this branch
git log --oneline HEAD..origin/feature-branch

# 3. Check for unpushed commits from others
git fetch origin
git status

# 4. Confirm you're on the right branch
git branch --show-current

# 5. Backup current state (optional)
git tag backup-before-force-push
```

### **Safe Force Push Script**
```bash
#!/bin/bash
# safe-force-push.sh

BRANCH=$(git branch --show-current)
REMOTE="origin"

echo "🔍 Checking safety for force push to $REMOTE/$BRANCH..."

# Check if remote branch exists
if ! git show-ref --verify --quiet refs/remotes/$REMOTE/$BRANCH; then
    echo "❌ Remote branch $REMOTE/$BRANCH does not exist"
    exit 1
fi

# Check for commits that would be lost
LOCAL_AHEAD=$(git rev-list $REMOTE/$BRANCH..HEAD --count)
REMOTE_AHEAD=$(git rev-list HEAD..$REMOTE/$BRANCH --count)

if [ $REMOTE_AHEAD -gt 0 ]; then
    echo "⚠️  Remote has $REMOTE_AHEAD commits you don't have"
    echo "   Consider pulling first: git pull --rebase $REMOTE $BRANCH"
    exit 1
fi

if [ $LOCAL_AHEAD -eq 0 ]; then
    echo "ℹ️  No local commits to push"
    exit 0
fi

echo "✅ Safe to force push $LOCAL_AHEAD commits"
echo "   Command: git push --force-with-lease $REMOTE $BRANCH"

read -p "Proceed with force push? (y/N): " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    git push --force-with-lease $REMOTE $BRANCH
else
    echo "Aborted."
fi
```

## 🚨 **Force Push Recovery**

### **If You Accidentally Force Pushed**
```bash
# 1. Find the lost commits in reflog
git reflog

# 2. Create a backup branch from lost commit
git checkout -b recovery-branch <commit-hash>

# 3. Restore the branch
git checkout main
git reset --hard recovery-branch

# 4. Push the restored branch
git push --force-with-lease origin main
```

### **If Someone Else Force Pushed**
```bash
# 1. Don't panic - check reflog
git reflog

# 2. Find your commits before the force push
git log --oneline --all --graph

# 3. Create recovery branch
git checkout -b my-recovery <your-last-commit>

# 4. Coordinate with the person who force pushed
# Discuss whether to merge or rebase your changes
```

## 📋 **Force Push Best Practices**

### **Communication**
- **Always communicate** before force pushing shared branches
- **Use pull requests** for significant changes
- **Document force pushes** in team chat
- **Avoid force pushing** `main`, `master`, or `develop` branches

### **Branch Protection**
```yaml
# .github/workflows/branch-protection.yml
name: Branch Protection
on:
  push:
    branches: [ main, develop ]

jobs:
  protect:
    runs-on: ubuntu-latest
    steps:
      - name: Check for force push
        run: |
          if [ "${{ github.event.forced }}" = "true" ]; then
            echo "❌ Force push detected on protected branch"
            echo "Please use pull requests for changes to main/develop"
            exit 1
          fi
```

### **Git Hooks for Safety**
```bash
# .git/hooks/pre-push
#!/bin/bash

protected_branches="^(main|master|develop)$"
current_branch=$(git branch --show-current)

if [[ $current_branch =~ $protected_branches ]]; then
    echo "❌ Cannot force push to protected branch: $current_branch"
    echo "Use a pull request instead"
    exit 1
fi

# Check for --force-with-lease
if [[ "$*" == *"--force"* ]] && [[ "$*" != *"--force-with-lease"* ]]; then
    echo "⚠️  Using --force instead of --force-with-lease"
    echo "Consider using --force-with-lease for safety"
    read -p "Continue anyway? (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
fi
```

## 🏷️ **Tagging and Releases**

### **Force Push Tags (Rare)**
```bash
# Only if you need to update a released tag
git tag -f v1.0.1  # Force update local tag
git push --force-with-lease origin v1.0.1

# But prefer new tags for releases
git tag v1.0.2
git push origin v1.0.2
```

## 🔧 **Advanced Force Push Scenarios**

### **Force Push with Lease Timeout**
```bash
# Force push with custom lease timeout (Git 2.30+)
git push --force-with-lease=10.minutes origin main

# Check lease status
git for-each-ref --format="%(refname:short) %(upstream:track)" refs/heads
```

### **Conditional Force Push**
```bash
# Only force push if remote hasn't changed
git push --force-with-lease=HEAD:origin/main origin main

# Force push only specific refs
git push --force-with-lease=refs/heads/feature:refs/heads/feature origin
```

### **Force Push in CI/CD**
```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Safe force push to production
        run: |
          # Only force push if we know it's safe in CI
          git push --force-with-lease origin production
```

## 📊 **Monitoring Force Pushes**

### **Git Log Analysis**
```bash
# See force push history
git log --oneline --grep="force"

# Check for suspicious patterns
git log --oneline --all --graph | grep -i force

# Audit recent pushes
git reflog --since="1 week ago" | grep push
```

### **Repository Settings**
- Enable branch protection rules
- Require pull requests for changes
- Require status checks before merging
- Require code reviews

## 🎯 **Quick Reference**

| Scenario | Command | Safety Level |
|----------|---------|--------------|
| Safe rebase push | `git push --force-with-lease` | 🟢 High |
| Clean commit history | `git push --force-with-lease` | 🟢 High |
| Fix commit message | `git push --force-with-lease` | 🟢 High |
| Reset feature branch | `git push --force-with-lease` | 🟡 Medium |
| Push to main | `git push --force-with-lease` | 🔴 Low (avoid) |
| Raw force push | `git push --force` | 🔴 Dangerous |

## 📚 **Additional Resources**

- [Git Force Push Documentation](https://git-scm.com/docs/git-push#Documentation/git-push.txt---force)
- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/managing-a-branch-protection-rule)
- [Understanding Git Rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

## ✅ **Force Push Safety Checklist**

- [ ] **Is this a personal branch?** (not main/master/develop)
- [ ] **Have you communicated** with team members?
- [ ] **Are you using `--force-with-lease`** not `--force`?
- [ ] **Have you checked** `git status` and `git log`?
- [ ] **Do you have a backup** of current state?
- [ ] **Is this change** truly necessary?
- [ ] **Can this be done** via pull request instead?

**Remember: With great power comes great responsibility!** ⚡🛡️