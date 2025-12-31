# Troubleshooting Git and GitHub Issues

This comprehensive guide covers common Git and GitHub problems and their solutions, helping you resolve issues quickly and efficiently.

---

## 1. Authentication Issues

### **Problem: Authentication Failed**
```
remote: Support for password authentication was removed on August 13, 2021.
fatal: Authentication failed for 'https://github.com/username/repo.git/'
```

**Solutions:**

#### **Use Personal Access Token (PAT)**
```bash
# Generate PAT in GitHub: Settings > Developer settings > Personal access tokens
git remote set-url origin https://YOUR_USERNAME:YOUR_PAT@github.com/YOUR_USERNAME/repo.git
```

#### **Switch to SSH**
```bash
# Change remote URL to SSH
git remote set-url origin git@github.com:YOUR_USERNAME/repo.git

# Ensure SSH key is added to ssh-agent
ssh-add ~/.ssh/id_rsa

# Test connection
ssh -T git@github.com
```

#### **Use GitHub CLI**
```bash
gh auth login
gh repo clone YOUR_USERNAME/repo
```

---

## 2. Push/Pull Issues

### **Problem: Non-Fast-Forward Updates Rejected**
```
! [rejected] main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/username/repo.git'
```

**Solutions:**

#### **Pull with Rebase**
```bash
git pull --rebase origin main
git push origin main
```

#### **Force Push (Use Carefully)**
```bash
# Only if you know what you're doing
git push --force-with-lease origin main
```

#### **Merge Instead of Rebase**
```bash
git pull origin main  # Creates merge commit
git push origin main
```

### **Problem: Push Rejected Due to Large Files**
```
remote: error: File file.zip is 125.00 MB; this exceeds GitHub's file size limit of 100.00 MB
```

**Solutions:**

#### **Use Git LFS**
```bash
# Install Git LFS
git lfs install

# Track large files
git lfs track "*.zip"
git lfs track "*.mp4"

# Add and commit
git add .gitattributes
git add large-file.zip
git commit -m "Add large file with LFS"
git push origin main
```

#### **Remove Large Files from History**
```bash
# Use BFG Repo-Cleaner or git-filter-repo
git filter-repo --strip-blobs-bigger-than 100M
git push --force
```

---

## 3. Branch Management Issues

### **Problem: Branch Already Exists**
```
fatal: A branch named 'feature-branch' already exists.
```

**Solutions:**

#### **Switch to Existing Branch**
```bash
git checkout feature-branch
```

#### **Delete and Recreate**
```bash
git branch -D feature-branch
git checkout -b feature-branch
```

#### **Rename Branch**
```bash
git branch -m feature-branch feature-branch-v2
```

### **Problem: Cannot Delete Branch**
```
error: Cannot delete branch 'main' checked out at '/path/to/repo'
```

**Solutions:**

#### **Switch to Different Branch First**
```bash
git checkout develop
git branch -d main
```

#### **Force Delete**
```bash
git branch -D main  # Force delete unmerged branch
```

---

## 4. Merge Conflict Issues

### **Problem: Merge Conflicts**
```
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**Solutions:**

#### **Resolve Conflicts Manually**
```bash
# Edit conflicted files
# Look for <<<<<<< HEAD, =======, >>>>>>> branch-name markers
# Choose which changes to keep

# Stage resolved files
git add file.txt

# Complete merge
git commit -m "Resolve merge conflicts"
```

#### **Use Merge Tools**
```bash
# Configure merge tool
git config --global merge.tool vimdiff
git mergetool
```

#### **Abort Merge**
```bash
git merge --abort  # Or git rebase --abort for rebase conflicts
```

---

## 5. Repository Corruption Issues

### **Problem: Loose Object Corruption**
```
error: object file .git/objects/xx/xxxxx is empty
fatal: loose object xx...xxxx (stored in .git/objects/xx/xxxxx) is corrupt
```

**Solutions:**

#### **Find and Remove Corrupt Objects**
```bash
# Find corrupt objects
find .git/objects/ -type f -empty -delete
find .git/objects/ -type f -exec git fsck --full --no-dangling {} \;

# Rebuild repository
git fsck --full
git gc --prune=now
```

#### **Clone Fresh Repository**
```bash
# Backup current work
cp -r .git ../backup.git

# Remove and re-clone
rm -rf .git
git clone <repository-url> .
```

---

## 6. GitHub-Specific Issues

### **Problem: Repository Not Found**
```
fatal: repository 'https://github.com/user/repo.git/' not found
```

**Solutions:**

#### **Check Repository Access**
```bash
# Verify repository exists and you have access
gh repo view user/repo

# Check if repository is private
gh repo list user  # Lists your repositories
```

#### **Fix Remote URL**
```bash
# Check current remote
git remote -v

# Update remote URL
git remote set-url origin https://github.com/correct-user/correct-repo.git
```

### **Problem: Branch Protection Rules**
```
! [remote rejected] main -> main (protected branch hook declined)
```

**Solutions:**

#### **Create Pull Request**
```bash
# Push to feature branch instead
git checkout -b feature-branch
git push origin feature-branch

# Create PR on GitHub
gh pr create --title "Feature" --body "Description"
```

#### **Bypass Protection (Admin Only)**
```bash
# Repository admins can temporarily disable protection
# Or configure branch protection to allow specific users
```

---

## 7. Performance Issues

### **Problem: Slow Git Operations**
**Solutions:**

#### **Optimize Repository**
```bash
# Clean up unnecessary files
git gc --aggressive --prune=now

# Repack repository
git repack -a -d --depth=250 --window=250
```

#### **Shallow Clone for Large Repos**
```bash
# Clone only recent history
git clone --depth 1 <repository-url>

# Later deepen if needed
git fetch --unshallow
```

#### **Use Git LFS for Large Files**
```bash
git lfs install
git lfs track "*.pdf"
git lfs track "*.jpg"
```

---

## 8. Git Configuration Issues

### **Problem: Wrong User Configuration**
```bash
# Check current config
git config --list --show-origin

# Wrong user.name or user.email
```

**Solutions:**

#### **Set Global Configuration**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### **Set Repository-Specific Config**
```bash
git config user.name "Work Name"
git config user.email "work.email@company.com"
```

#### **Fix Commit Author**
```bash
# Amend last commit
git commit --amend --reset-author

# For multiple commits
git rebase -i HEAD~n --exec "git commit --amend --reset-author --no-edit"
```

---

## 9. Submodule Issues

### **Problem: Submodule Not Initialized**
```
fatal: Not a git repository: submodule/path/.git
```

**Solutions:**

#### **Initialize Submodules**
```bash
# Initialize all submodules
git submodule init
git submodule update

# Or clone with submodules
git clone --recurse-submodules <repository-url>
```

#### **Update Submodules**
```bash
git submodule update --remote
git submodule foreach git pull origin main
```

---

## 10. Common Git Command Mistakes

### **Problem: Accidentally Committed Wrong Files**
**Solutions:**

#### **Remove from Last Commit**
```bash
git reset --soft HEAD~1  # Keep changes staged
git reset HEAD file.txt  # Unstage specific file
git commit -c ORIG_HEAD  # Re-commit without wrong file
```

#### **Remove from Git History**
```bash
# Use git-filter-repo or BFG
git filter-repo --path file-to-remove --invert-paths
```

### **Problem: Lost Commits**
**Solutions:**

#### **Find Lost Commits**
```bash
git reflog  # Show recent actions
git checkout <commit-hash>
```

#### **Recover Deleted Branch**
```bash
git checkout -b recovered-branch <commit-hash>
```

---

## 11. GitHub Actions Issues

### **Problem: Workflow Fails**
**Solutions:**

#### **Check Workflow Logs**
```bash
# View on GitHub or use CLI
gh run list
gh run view <run-id>
```

#### **Debug Locally**
```bash
# Use act tool for local testing
act -j job-name
```

#### **Common Fixes**
```yaml
# Add debugging
- name: Debug
  run: |
    echo "GitHub ref: $GITHUB_REF"
    echo "GitHub event: $GITHUB_EVENT_NAME"
```

---

## 12. Advanced Troubleshooting

### **Problem: Repository Size Issues**
**Solutions:**

#### **Check Repository Size**
```bash
# GitHub repository size
gh repo view --json diskUsage

# Local repository size
du -sh .git
```

#### **Clean Large Files**
```bash
# Find large files in history
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '/^blob/ {print substr($0,6)}' | sort -k2nr | head -10

# Remove large blobs
git filter-repo --strip-blobs-bigger-than 50M
```

### **Problem: Git Ignore Not Working**
**Solutions:**

#### **Check .gitignore Location**
```bash
# Must be in repository root or parent directories
ls -la .gitignore

# Check if files are already tracked
git ls-files | grep problematic-file
```

#### **Remove Already Tracked Files**
```bash
git rm --cached problematic-file
git commit -m "Remove tracked file"
```

---

## 13. Prevention Best Practices

### **Regular Maintenance**
```bash
# Weekly cleanup
git gc --prune=now --aggressive
git remote prune origin

# Monthly deep clean
git reflog expire --all --expire=30.days.ago
git gc --prune=30.days.ago
```

### **Backup Strategy**
```bash
# Create backups before major operations
cp -r .git ../repo-backup.git

# Use GitHub backups for critical repositories
gh repo clone user/repo --mirror ../backup
```

### **Configuration Backup**
```bash
# Backup Git configuration
git config --list --show-origin > git-config-backup.txt

# Backup SSH keys
cp ~/.ssh/id_rsa* ../ssh-backup/
```

---

## 14. Getting Help

### **Built-in Help**
```bash
git help <command>
git <command> --help
man git-<command>
```

### **Online Resources**
- [Git Documentation](https://git-scm.com/doc)
- [GitHub Help](https://docs.github.com/en)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/git)
- [GitHub Community](https://github.community/)

### **Community Support**
```bash
# GitHub CLI support
gh issue list -R cli/cli

# Search existing issues
gh issue list -R desktop/desktop --search "git clone"
```

---

## Summary

Most Git and GitHub issues can be resolved by:
1. **Understanding the error message**
2. **Checking repository status** (`git status`, `git log`)
3. **Verifying remote configuration** (`git remote -v`)
4. **Using appropriate Git commands** for the situation
5. **Following security best practices**

Remember: When in doubt, **don't force push** without understanding the consequences, and **always backup** before major operations.

**Additional Resources**
- [Git Troubleshooting](https://git-scm.com/docs/git-bisect)
- [GitHub Debug Issues](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows)
- [Git LFS](https://git-lfs.github.io/)