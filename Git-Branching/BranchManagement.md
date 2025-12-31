# Branch Management Best Practices

Effective branch management is crucial for organized development and successful pull requests. This guide covers strategies for creating, maintaining, and merging branches in open source projects.

## Branch Naming Conventions

### Standard Branch Types
- `main` or `master`: Production-ready code
- `develop` or `dev`: Integration branch for features
- `feature/*`: New features
- `bugfix/*` or `hotfix/*`: Bug fixes
- `release/*`: Release preparation
- `docs/*`: Documentation updates

### Good Branch Names
```bash
# Feature branches
feature/add-user-authentication
feature/implement-payment-system
feature/ui-redesign

# Bug fixes
bugfix/fix-login-validation
bugfix/resolve-memory-leak
hotfix/critical-security-patch

# Documentation
docs/update-api-reference
docs/add-contributing-guide

# Releases
release/v2.1.0
```

## Creating Branches

### From Main Branch
```bash
# Ensure you're on main and up to date
git checkout main
git pull origin main

# Create and switch to new branch
git checkout -b feature/new-feature-name
```

### From Current Branch
```bash
# Create branch from current state
git checkout -b bugfix/issue-123
```

## Branch Workflow Strategies

### 1. Feature Branch Workflow
```bash
# Create feature branch
git checkout -b feature/add-search-functionality

# Make changes and commits
git add .
git commit -m "feat: implement basic search"

# Push to remote
git push -u origin feature/add-search-functionality

# Create pull request
# After approval, merge and delete branch
```

### 2. Git Flow Workflow
```bash
# Develop new features on feature branches
git checkout -b feature/new-ui-component

# Merge features to develop
git checkout develop
git merge feature/new-ui-component

# Create release branch
git checkout -b release/v1.2.0

# Merge release to main and develop
git checkout main
git merge release/v1.2.0
git checkout develop
git merge release/v1.2.0
```

## Managing Multiple Branches

### Listing Branches
```bash
# List all branches
git branch -a

# List remote branches
git branch -r

# Show branch status
git status
```

### Switching Between Branches
```bash
# Switch to existing branch
git checkout feature/login-system

# Switch and create if doesn't exist
git checkout -b feature/new-feature
```

### Comparing Branches
```bash
# Compare branches
git diff main..feature/new-feature

# See commits unique to branch
git log main..feature/new-feature --oneline
```

## Merging Strategies

### Merge Commit (Default)
```bash
git checkout main
git merge feature/completed-feature
# Creates merge commit preserving history
```

### Squash Merge
```bash
git checkout main
git merge --squash feature/completed-feature
git commit -m "feat: add completed feature"
# Combines all commits into one
```

### Rebase (Linear History)
```bash
git checkout feature/your-branch
git rebase main
# Replays your commits on top of main
```

## Handling Merge Conflicts

### During Merge
```bash
# Start merge
git checkout main
git merge feature/conflicting-branch

# If conflicts occur, edit files to resolve
# Then mark as resolved
git add resolved-file.py
git commit
```

### During Rebase
```bash
git checkout feature/your-branch
git rebase main

# Resolve conflicts
git add resolved-file.py
git rebase --continue

# Or abort if needed
git rebase --abort
```

## Branch Cleanup

### Delete Local Branches
```bash
# Delete merged branch
git branch -d feature/completed-feature

# Force delete unmerged branch
git branch -D feature/abandoned-feature
```

### Delete Remote Branches
```bash
# Delete remote branch
git push origin --delete feature/old-feature
```

### Prune Stale Branches
```bash
# Remove references to deleted remote branches
git remote prune origin

# Or fetch and prune
git fetch --prune
```

## Advanced Branch Management

### Interactive Rebase
```bash
# Rebase last 3 commits
git rebase -i HEAD~3

# Commands: pick, reword, edit, squash, fixup, drop
```

### Cherry Picking
```bash
# Apply specific commit to current branch
git cherry-pick abc123

# Cherry pick multiple commits
git cherry-pick abc123 def456
```

### Branch Tracking
```bash
# Set upstream branch
git branch --set-upstream-to=origin/feature/branch

# Unset upstream
git branch --unset-upstream
```

## Best Practices

### 1. Keep Branches Short-lived
- Feature branches: 1-2 weeks max
- Bug fixes: Complete quickly
- Regular rebasing to stay current

### 2. Regular Synchronization
```bash
# Update feature branch with main
git checkout feature/your-branch
git fetch origin
git rebase origin/main
```

### 3. Clear Commit Messages
- Use conventional commits
- Reference issues: `fix: resolve issue #123`
- Keep messages under 50 characters

### 4. Branch Protection
- Never commit directly to main
- Require pull requests for merges
- Enable branch protection rules

## Troubleshooting

### Branch Not Tracking Remote
```bash
# Set tracking
git branch --set-upstream-to=origin/your-branch
```

### Lost Commits After Rebase
```bash
# Find lost commits
git reflog

# Recover
git checkout lost-commit-hash
```

### Branch Diverged
```bash
# Reset to remote
git reset --hard origin/your-branch

# Or merge with care
git pull --no-ff
```

## Summary

Effective branch management involves:
- Consistent naming conventions
- Regular synchronization
- Appropriate merge strategies
- Clean branch lifecycle
- Conflict resolution skills

Master these practices to maintain clean, organized repositories and smooth collaboration.