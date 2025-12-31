# Basic and Advanced Git Push Commands

This comprehensive guide covers all essential and advanced git push commands, from basic usage to complex scenarios involving multiple remotes, force pushes, and workflow optimization.

## 🚀 **Basic Push Commands**

### **Push Current Branch**
```bash
# Push current branch to origin with same name
git push

# Explicitly specify remote and branch
git push origin main

# Push current branch and set upstream (first push)
git push -u origin feature-branch
git push --set-upstream origin feature-branch
```

### **Push All Branches**
```bash
# Push all local branches to remote
git push origin --all

# Push all branches and tags
git push origin --all --tags
```

### **Push Specific Branch**
```bash
# Push a specific local branch to remote
git push origin local-branch:remote-branch

# Push local branch to remote with different name
git push origin feature-x:feature-y
```

## 🔄 **Advanced Push Scenarios**

### **Push to Multiple Remotes**
```bash
# Add multiple remotes
git remote add origin git@github.com:yourusername/repo.git
git remote add upstream git@github.com:original/repo.git

# Push to all remotes
git remote | xargs -I {} git push {} main

# Or use git config for multiple push URLs
git remote set-url --add --push origin git@github.com:yourusername/repo.git
git remote set-url --add --push origin git@gitlab.com:yourusername/repo.git
```

### **Push with Options**
```bash
# Push with verbose output
git push -v origin main

# Push with progress (useful for large repos)
git push --progress origin main

# Dry run (see what would be pushed)
git push --dry-run origin main

# Push with no-thin (for large changes)
git push --no-thin origin main
```

### **Atomic Pushes**
```bash
# Push multiple branches atomically
git push origin main feature-x feature-y

# Push with atomic option (all or nothing)
git push --atomic origin main feature-x
```

## ⚡ **Force Push (Use with Caution)**

### **Safe Force Push**
```bash
# Force push with lease (recommended)
git push --force-with-lease origin main

# Force push with lease and verbose
git push --force-with-lease -v origin main

# Force push with lease to specific branch
git push --force-with-lease origin feature-branch
```

### **Unsafe Force Push (Avoid if Possible)**
```bash
# Force push (overwrites remote history)
git push --force origin main

# Force push with mirror (dangerous)
git push --mirror origin
```

### **When to Use Force Push**
- **Rebasing after upstream changes**
- **Fixing commit history mistakes**
- **Interactive rebase cleanup**
- **Never on shared branches without coordination**

## 🏷️ **Pushing Tags**

### **Push Single Tag**
```bash
# Create and push annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0

# Create and push lightweight tag
git tag v1.0.0
git push origin v1.0.0
```

### **Push All Tags**
```bash
# Push all local tags to remote
git push origin --tags

# Push all tags with verbose output
git push -v origin --tags
```

### **Push Specific Tags**
```bash
# Push multiple specific tags
git push origin tag v1.0.0 tag v1.1.0

# Push tag range
git push origin --tags v1.0.0..v2.0.0
```

### **Delete Remote Tags**
```bash
# Delete remote tag
git push origin --delete tag v1.0.0

# Delete multiple tags
git push origin --delete tag v1.0.0 tag v1.1.0
```

## 🌿 **Branch Management Pushes**

### **Push New Branch**
```bash
# Create and push new branch
git checkout -b new-feature
git push -u origin new-feature

# Push existing branch to new remote
git push new-remote feature-branch
```

### **Delete Remote Branches**
```bash
# Delete remote branch
git push origin --delete feature-branch

# Delete multiple branches
git push origin --delete branch1 branch2 branch3

# Delete branches matching pattern
git branch -r | grep "feature/" | sed 's/origin\///' | xargs git push origin --delete
```

### **Rename Remote Branch**
```bash
# Rename remote branch (requires force push)
git branch -m old-name new-name
git push origin --delete old-name
git push -u origin new-name
```

## 🔀 **Working with Remotes**

### **Push to Different Remote**
```bash
# Push to specific remote
git push upstream main

# Push current branch to different remote
git push production main

# Push to all configured remotes
for remote in $(git remote); do git push $remote main; done
```

### **Push with Different URLs**
```bash
# Push to HTTPS remote
git push https://github.com/user/repo.git main

# Push to SSH remote
git push git@github.com:user/repo.git main
```

## 📦 **Large Repository Handling**

### **Shallow Push**
```bash
# Push with depth limit (for large repos)
git push --depth=1 origin main

# Push with shallow since date
git push --shallow-since="2023-01-01" origin main
```

### **Partial Push**
```bash
# Push only recent commits
git push origin main --since="1 week ago"

# Push with object filtering (Git 2.30+)
git push origin main --filter=blob:limit=1m
```

## 🔧 **Troubleshooting Push Issues**

### **Non-Fast-Forward Error**
```bash
# Error: Updates were rejected because the remote contains work that you do not have locally

# Solution 1: Pull and merge
git pull origin main
git push origin main

# Solution 2: Pull with rebase
git pull --rebase origin main
git push origin main

# Solution 3: Force push (if you know what you're doing)
git push --force-with-lease origin main
```

### **Authentication Issues**
```bash
# For HTTPS: Update credentials
git config --global credential.helper store
git push origin main  # Enter new credentials

# For SSH: Check SSH key
ssh -T git@github.com
git push origin main
```

### **Large File Issues**
```bash
# If push fails due to large files
# Remove large files from history
git filter-branch --tree-filter 'rm -rf large-file.zip' HEAD
git push --force-with-lease origin main

# Or use Git LFS for large files
git lfs install
git lfs track "*.zip"
git add .gitattributes
git commit -m "Track large files with LFS"
git push origin main
```

### **Permission Denied**
```bash
# Check repository permissions on GitHub
# Ensure you have write access

# For organization repos, check branch protection rules
# May need to create PR instead of direct push

# Check if you're pushing to correct remote
git remote -v
```

## 🚀 **Workflow Optimization**

### **Push Aliases**
```bash
# Add to ~/.gitconfig
[alias]
    pf = push --force-with-lease
    pfu = push --force-with-lease -u
    pft = push --follow-tags
    publish = "!git push -u origin $(git branch-name)"

# Usage
git pf origin main
git pfu origin feature-branch
```

### **Automated Pushing**
```bash
# Push on successful build (in CI/CD)
if [ $? -eq 0 ]; then
    git push origin main
fi

# Push with pre-push hook
# .git/hooks/pre-push
#!/bin/bash
echo "Running tests before push..."
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed. Aborting push."
    exit 1
fi
```

### **Batch Operations**
```bash
# Push all feature branches
git branch | grep "feature/" | xargs -I {} git push -u origin {}

# Push branches that exist on remote
git branch -r | grep -v HEAD | sed 's/origin\///' | xargs -I {} git push origin {}

# Clean up merged branches
git branch --merged main | grep -v main | xargs git push origin --delete
```

## 📊 **Push Statistics and Monitoring**

### **Check Push Status**
```bash
# See what will be pushed
git log --oneline origin/main..HEAD

# Check push size
git count-objects -v

# Monitor push progress
git push --progress --verbose origin main
```

### **Push History**
```bash
# See recent pushes
git reflog | grep push

# Check remote update time
git log --oneline -1 --format="%h %s %ar" origin/main
```

## 🎯 **Best Practices**

### **Before Pushing**
- [ ] **Pull latest changes** from upstream
- [ ] **Run tests locally** to ensure code works
- [ ] **Check for merge conflicts** with `git fetch && git status`
- [ ] **Review your commits** with `git log --oneline`
- [ ] **Use meaningful commit messages**

### **Safe Pushing**
- [ ] **Use `--force-with-lease`** instead of `--force`
- [ ] **Avoid force pushing** to shared branches
- [ ] **Communicate** before force pushing shared branches
- [ ] **Test after pushing** to ensure remote is correct
- [ ] **Use branches** for experimental work

### **Collaboration**
- [ ] **Create PRs** for significant changes
- [ ] **Request reviews** before merging
- [ ] **Keep branches short-lived**
- [ ] **Delete merged branches** to keep repo clean
- [ ] **Use protected branches** for main/production

## 📚 **Additional Resources**

- [Git Push Documentation](https://git-scm.com/docs/git-push)
- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/managing-a-branch-protection-rule)
- [Git LFS](https://git-lfs.github.io/)
- [Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

## ✅ **Push Command Quick Reference**

| Command | Description | Use Case |
|---------|-------------|----------|
| `git push` | Push current branch | Basic workflow |
| `git push -u origin branch` | Push and set upstream | First push of branch |
| `git push --force-with-lease` | Safe force push | After rebase/cleanup |
| `git push origin --delete branch` | Delete remote branch | Cleanup |
| `git push origin --tags` | Push all tags | Release management |
| `git push --dry-run` | Preview push | Safety check |
| `git push --all` | Push all branches | Full sync |

Master these commands to become a push master! 🚀💪