# Git Best Practices: Professional Version Control Management

This comprehensive guide covers advanced Git practices, workflows, and strategies for professional software development. Mastering these practices ensures clean, maintainable codebases and efficient collaboration.

---

## 1. Repository Structure & Organization

### **Branching Strategy**
- **Main/Master**: Production-ready code
- **Develop**: Integration branch for features
- **Feature branches**: `feature/feature-name` for new features
- **Bugfix branches**: `bugfix/issue-number-description` for fixes
- **Hotfix branches**: `hotfix/critical-fix` for urgent production fixes
- **Release branches**: `release/v1.2.3` for release preparation

### **Directory Structure**
```
project/
├── .github/          # GitHub-specific files
├── docs/            # Documentation
├── src/             # Source code
├── tests/           # Test files
├── scripts/         # Build/deployment scripts
├── .gitignore       # Git ignore rules
└── README.md        # Project documentation
```

### **Git Ignore Best Practices**
```gitignore
# Compiled source
*.com
*.class
*.dll
*.exe
*.o
*.so

# Packages
node_modules/
__pycache__/
*.egg-info/

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Environment variables
.env
.env.local
```

---

## 2. Commit Practices

### **Conventional Commits**
```bash
# Format: type(scope): description

feat(auth): add JWT token validation
fix(api): resolve null pointer exception in user service
docs(readme): update installation instructions
style(lint): fix code formatting issues
refactor(db): optimize query performance
test(auth): add unit tests for login endpoint
chore(deps): update dependencies to latest versions
```

### **Commit Message Guidelines**
- **Subject line**: 50 characters max, imperative mood
- **Body**: Explain what and why, not how
- **Footer**: Reference issues, breaking changes

**Good Example:**
```
feat: implement user authentication system

- Add JWT token generation and validation
- Implement password hashing with bcrypt
- Create user registration and login endpoints
- Add comprehensive test coverage

Closes #123
```

**Bad Example:**
```
fixed bug
```

### **Atomic Commits**
- Each commit should contain one logical change
- Changes should be complete and working
- Easy to review and revert if needed

---

## 3. Branching Workflows

### **Git Flow Workflow**
```bash
# Feature development
git checkout -b feature/new-feature
# Make changes and commits
git checkout develop
git merge feature/new-feature
git branch -d feature/new-feature

# Release process
git checkout -b release/v1.2.3
# Final testing and bug fixes
git checkout main
git merge release/v1.2.3
git tag -a v1.2.3 -m "Release version 1.2.3"
```

### **GitHub Flow (Simpler)**
```bash
# Create feature branch from main
git checkout -b feature/new-feature
# Make changes and push
git push -u origin feature/new-feature
# Create PR on GitHub
# After merge, delete branch
git checkout main
git pull
git branch -d feature/new-feature
```

### **Trunk-Based Development**
- All developers work on main branch
- Short-lived feature branches (< 1 day)
- Continuous integration and deployment
- Requires strong automated testing

---

## 4. Merging Strategies

### **Merge vs. Rebase**
```bash
# Merge: Preserves history
git checkout main
git merge feature-branch
# Creates merge commit

# Rebase: Linear history
git checkout feature-branch
git rebase main
# No merge commit, linear history
```

### **Squash Merging**
```bash
# Combine multiple commits into one
git checkout main
git merge --squash feature-branch
git commit -m "feat: add new feature"
```

### **Fast-Forward vs. Non-Fast-Forward**
- **Fast-forward**: Moves pointer forward (no merge commit)
- **Non-fast-forward**: Always creates merge commit
- Use `--no-ff` for feature branches to preserve history

---

## 5. Conflict Resolution

### **Prevention Strategies**
- Communicate with team about changes
- Pull frequently from main branch
- Use feature flags for conflicting features
- Keep branches short-lived

### **Resolution Process**
```bash
# During merge/rebase conflict
git status  # See conflicted files
# Edit files to resolve conflicts
git add resolved-file
git commit  # For merge
# or
git rebase --continue  # For rebase
```

### **Conflict Markers**
```diff
<<<<<<< HEAD
// Your changes
console.log('Hello World');
=======
// Incoming changes
console.log('Hello Universe');
>>>>>>> feature-branch
```

---

## 6. Advanced Git Commands

### **Interactive Rebase**
```bash
# Edit last 3 commits
git rebase -i HEAD~3

# Commands:
# pick: use commit
# reword: edit message
# edit: amend commit
# squash: combine with previous
# fixup: combine without message
# drop: remove commit
```

### **Cherry Picking**
```bash
# Apply specific commit to current branch
git cherry-pick <commit-hash>

# Cherry pick range
git cherry-pick <start>..<end>
```

### **Bisect for Bug Hunting**
```bash
# Find commit that introduced bug
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
# Git will checkout commits for testing
git bisect reset  # Return to original state
```

### **Stashing Changes**
```bash
# Temporarily save work
git stash push -m "work in progress"

# List stashes
git stash list

# Apply stash
git stash pop

# Apply specific stash
git stash apply stash@{1}
```

---

## 7. Remote Repository Management

### **Multiple Remotes**
```bash
# Add upstream remote
git remote add upstream https://github.com/original/repo.git

# Fetch from upstream
git fetch upstream

# Sync fork
git checkout main
git rebase upstream/main
git push origin main
```

### **Remote Branch Management**
```bash
# List remote branches
git branch -r

# Delete remote branch
git push origin --delete feature-branch

# Prune deleted remote branches
git remote prune origin
```

---

## 8. Git Hooks & Automation

### **Pre-commit Hooks**
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Run linting
npm run lint

# Run tests
npm test

# Check for console.log statements
if git diff --cached | grep -q "console\.log"; then
  echo "Error: console.log found in committed files"
  exit 1
fi
```

### **Commit-msg Hook**
```bash
#!/bin/sh
# .git/hooks/commit-msg

commit_msg=$(cat $1)
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,}"; then
  echo "Invalid commit message format"
  echo "Use: type(scope): description"
  exit 1
fi
```

### **Pre-push Hook**
```bash
#!/bin/sh
# .git/hooks/pre-push

# Run full test suite
npm run test:ci

# Check code coverage
npm run coverage -- --check-coverage
```

---

## 9. Performance Optimization

### **Shallow Cloning**
```bash
# Clone only recent history
git clone --depth 1 https://github.com/user/repo.git

# Deepen shallow clone
git fetch --unshallow
```

### **Partial Cloning**
```bash
# Clone only specific directories
git clone --filter=blob:none --sparse https://github.com/user/repo.git
cd repo
git sparse-checkout set "src/"
```

### **Git LFS for Large Files**
```bash
# Install Git LFS
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.mov"

# Add .gitattributes
git add .gitattributes
git commit -m "feat: add Git LFS tracking"
```

---

## 10. Git Configuration

### **Global Configuration**
```bash
# Set user information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "code --wait"

# Set default branch name
git config --global init.defaultBranch main

# Enable colors
git config --global color.ui auto
```

### **Repository-specific Config**
```bash
# Set different email for work projects
git config user.email "work.email@company.com"

# Set custom diff tool
git config diff.tool vscode
git config difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"
```

### **Aliases for Efficiency**
```bash
# Short status
git config --global alias.s "status --short"

# Pretty log
git config --global alias.lg "log --oneline --graph --decorate"

# Quick commit
git config --global alias.cm "commit -m"

# Amend last commit
git config --global alias.amend "commit --amend --no-edit"
```

---

## 11. Troubleshooting Common Issues

### **Detached HEAD State**
```bash
# Create branch from detached HEAD
git checkout -b temp-branch

# Or return to main
git checkout main
```

### **Lost Commits**
```bash
# Find lost commits
git reflog

# Recover commit
git checkout <commit-hash>
git checkout -b recovery-branch
```

### **Repository Corruption**
```bash
# Check repository health
git fsck

# Rebuild index
rm .git/index
git reset
```

### **Merge Conflicts in Binary Files**
```bash
# Use custom merge driver
git config merge.binary.driver "binary-merge %O %A %B"

# Create merge driver script
echo '#!/bin/sh
# Choose our version
cp "$2" "$1"' > binary-merge
chmod +x binary-merge
```

---

## 12. Git Security Best Practices

### **SSH Key Management**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Add to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Test connection
ssh -T git@github.com
```

### **GPG Commit Signing**
```bash
# Generate GPG key
gpg --full-generate-key

# Configure Git to use GPG
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true

# Verify signature
git log --show-signature
```

### **Repository Security**
- Use `.gitignore` to avoid committing secrets
- Regularly audit repository for exposed credentials
- Use GitHub's security features (Dependabot, CodeQL)
- Implement branch protection rules

---

## 13. Advanced Workflows

### **Pair Programming with Git**
```bash
# Create pair branch
git checkout -b pair/user1-user2-feature

# Take turns committing
git commit -m "feat: implement core logic (pair: user1, user2)"

# Use co-authorship
git commit -m "feat: add user interface

Co-authored-by: User Two <user2@example.com>"
```

### **Code Review Workflow**
```bash
# Create review branch
git checkout -b review/feature-branch

# Make review changes
git add .
git commit -m "review: address feedback on error handling"

# Push for review
git push origin review/feature-branch
```

### **Release Automation**
```bash
# Tag release
git tag -a v1.2.3 -m "Release v1.2.3

## Changes
- Feature A
- Bug fix B
- Performance improvement C"

# Push tags
git push origin v1.2.3

# Create GitHub release
gh release create v1.2.3 --title "Release v1.2.3" --notes-file RELEASE_NOTES.md
```

---

## 14. Git and CI/CD Integration

### **GitHub Actions Integration**
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm test
      - run: npm run lint
```

### **Automated Versioning**
```bash
# Use semantic versioning
npm version patch  # 1.0.0 -> 1.0.1
npm version minor  # 1.0.0 -> 1.1.0
npm version major  # 1.0.0 -> 2.0.0

# Push version and tags
git push && git push --tags
```

---

## 15. Git Maintenance

### **Repository Cleanup**
```bash
# Remove untracked files
git clean -fd

# Remove ignored files
git clean -fdX

# Garbage collection
git gc

# Aggressive cleanup
git gc --aggressive --prune=now
```

### **Branch Cleanup**
```bash
# Delete merged branches
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# Delete remote merged branches
git branch -r --merged | grep -v main | sed 's/origin\///' | xargs -n 1 git push --delete origin
```

### **History Cleanup**
```bash
# Remove sensitive data
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch secrets.txt' --prune-empty --tag-name-filter cat -- --all

# Force push (destructive)
git push origin --force --all
```

---

## Summary

Mastering Git best practices involves understanding branching strategies, maintaining clean commit history, resolving conflicts efficiently, and leveraging advanced Git features. Consistent application of these practices leads to maintainable codebases and smooth collaboration.

**Key Takeaways:**
- Use conventional commits for clarity
- Keep branches focused and short-lived
- Regularly sync with upstream repositories
- Implement pre-commit hooks for quality
- Use interactive rebase for clean history
- Set up proper Git configuration
- Follow security best practices

**Additional Resources:**
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://conventionalcommits.org/)
- [Git Documentation](https://git-scm.com/doc)

---

*Ready for PullRequestManagement.md?*