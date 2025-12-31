# Comprehensive GitHub and Git Command Guide

This comprehensive guide covers essential and advanced commands for managing local and remote repositories, branches, pull requests, and interactions with GitHub using both Git and GitHub CLI tools. Master these commands to become a GitHub power user.

## 📋 Table of Contents

- [1. Repository Setup & Cloning](#1-repository-setup--cloning)
- [2. Branch Management](#2-branch-management)
- [3. Fetching, Pulling, and Syncing](#3-fetching-pulling-and-syncing)
- [4. Committing Changes](#4-committing-changes)
- [5. Pushing Changes](#5-pushing-changes)
- [6. Working with Pull Requests Using GitHub CLI](#6-working-with-pull-requests-using-github-cli)
- [7. Issues and Labels](#7-issues-and-labels)
- [8. Working with GitHub Actions and Checks](#8-working-with-github-actions-and-checks)
- [9. Custom Aliases (GitHub CLI)](#9-custom-aliases-github-cli)
- [10. Advanced Git Techniques](#10-advanced-git-techniques)
- [11. GitHub CLI Extensions](#11-github-cli-extensions)
- [12. Troubleshooting](#12-troubleshooting)
- [Summary](#summary)
- [Additional Resources](#additional-resources)

---

## 1. Repository Setup & Cloning

### **Basic Cloning**

**Clone repository with SSH:**
```bash
git clone git@github.com:owner/repo.git
```

**Clone with HTTPS:**
```bash
git clone https://github.com/owner/repo.git
```

**Clone specific branch:**
```bash
git clone -b branch-name https://github.com/owner/repo.git
```

**Shallow clone (faster, less history):**
```bash
git clone --depth 1 https://github.com/owner/repo.git
```

### **Forking and Cloning with GitHub CLI**

**Fork and clone in one command:**
```bash
gh repo fork owner/repo --clone=true
```

**Fork without cloning:**
```bash
gh repo fork owner/repo
```

**Clone existing fork:**
```bash
git clone https://github.com/YOUR_USERNAME/repo.git
```

### **Setting Up Remotes**

**Add upstream remote:**
```bash
git remote add upstream https://github.com/ORIGINAL_OWNER/repo.git
```

**Rename origin to your fork:**
```bash
git remote set-url origin https://github.com/YOUR_USERNAME/repo.git
```

**Verify remote configuration:**
```bash
git remote -v
```

## 2. Branch Management

### **Viewing Branches**

**List local branches:**
```bash
git branch
```

**List remote branches:**
```bash
git branch -r
```

**List all branches:**
```bash
git branch -a
```

**Show current branch:**
```bash
git branch --show-current
```

### **Creating and Switching Branches**

**Create and switch to new branch:**
```bash
git checkout -b feature/new-feature
# or
git switch -c feature/new-feature
```

**Switch to existing branch:**
```bash
git checkout existing-branch
# or
git switch existing-branch
```

**Create branch from specific commit:**
```bash
git checkout -b new-branch abc123
```

### **Branch Operations**

**Rename current branch:**
```bash
git branch -m new-branch-name
```

**Delete local branch:**
```bash
git branch -d branch-name
```

**Force delete local branch:**
```bash
git branch -D branch-name
```

**Delete remote branch:**
```bash
git push origin --delete branch-name
```

**Track remote branch:**
```bash
git checkout --track origin/remote-branch
```

## 3. Fetching, Pulling, and Syncing

### **Fetch Operations**

**Fetch all branches from origin:**
```bash
git fetch origin
```

**Fetch from upstream:**
```bash
git fetch upstream
```

**Fetch specific branch:**
```bash
git fetch origin branch-name
```

### **Pull Operations**

**Pull with merge (default):**
```bash
git pull origin main
```

**Pull with rebase (linear history):**
```bash
git pull --rebase origin main
```

**Pull specific branch:**
```bash
git pull origin feature-branch
```

### **Syncing Forks**

**Sync fork with upstream:**
```bash
git checkout main
git fetch upstream
git rebase upstream/main
git push origin main
```

**Alternative merge approach:**
```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

## 4. Committing Changes

### **Staging Changes**

**Stage specific file:**
```bash
git add filename.txt
```

**Stage all changes:**
```bash
git add .
```

**Stage interactively:**
```bash
git add -p
```

**Unstage file:**
```bash
git reset filename.txt
```

### **Committing**

**Commit with message:**
```bash
git commit -m "feat: add new feature"
```

**Commit all staged changes:**
```bash
git commit -a -m "fix: resolve bug"
```

**Amend last commit:**
```bash
git commit --amend
```

**Amend without changing message:**
```bash
git commit --amend --no-edit
```

### **Advanced Commit Techniques**

**Empty commit:**
```bash
git commit --allow-empty -m "trigger: ci build"
```

**Commit with specific author:**
```bash
git commit --author="Name <email@example.com>" -m "message"
```

## 5. Pushing Changes

### **Basic Push**

**Push current branch:**
```bash
git push origin current-branch
```

**Push with upstream tracking:**
```bash
git push -u origin feature-branch
```

**Push all branches:**
```bash
git push --all origin
```

### **Force Push (Use Carefully)**

**Safe force push:**
```bash
git push --force-with-lease origin branch
```

**Dangerous force push:**
```bash
git push --force origin branch
```

### **Push Options**

**Push tags:**
```bash
git push --tags
```

**Push to different remote:**
```bash
git push upstream main
```

**Dry run push:**
```bash
git push --dry-run origin branch
```

## 6. Working with Pull Requests Using GitHub CLI

### **PR Status and Information**

**Check PR status:**
```bash
gh pr status
```

**List PRs:**
```bash
gh pr list
```

**View PR details:**
```bash
gh pr view 123
```

**View PR in browser:**
```bash
gh pr view --web 123
```

### **Creating PRs**

**Create PR interactively:**
```bash
gh pr create
```

**Create PR with options:**
```bash
gh pr create --title "feat: add authentication" \
             --body "Implements user login..." \
             --base main \
             --head feature/auth
```

**Create draft PR:**
```bash
gh pr create --draft
```

### **PR Operations**

**Checkout PR locally:**
```bash
gh pr checkout 123
```

**Update PR branch:**
```bash
gh pr update-branch 123
```

**Merge PR:**
```bash
gh pr merge 123
```

**Merge with options:**
```bash
gh pr merge 123 --squash --delete-branch
```

### **PR Reviews**

**List PR reviews:**
```bash
gh pr review --list 123
```

**Approve PR:**
```bash
gh pr review 123 --approve
```

**Request changes:**
```bash
gh pr review 123 --request-changes --body "Please fix..."
```

## 7. Issues and Labels

### **Issue Management**

**List issues:**
```bash
gh issue list
```

**List issues with filters:**
```bash
gh issue list --label "bug" --state open
```

**View issue:**
```bash
gh issue view 123
```

**Create issue:**
```bash
gh issue create --title "Bug: app crashes" --body "Steps to reproduce..."
```

### **Issue Operations**

**Close issue:**
```bash
gh issue close 123
```

**Reopen issue:**
```bash
gh issue reopen 123
```

**Edit issue:**
```bash
gh issue edit 123 --title "New title"
```

### **Labels**

**List labels:**
```bash
gh label list
```

**Create label:**
```bash
gh label create "priority: high" --color FF0000
```

**Apply label to issue:**
```bash
gh issue edit 123 --add-label "bug"
```

**Remove label:**
```bash
gh issue edit 123 --remove-label "bug"
```

## 8. Working with GitHub Actions and Checks

### **Workflow Management**

**List workflows:**
```bash
gh workflow list
```

**View workflow:**
```bash
gh workflow view "CI"
```

**Run workflow:**
```bash
gh workflow run "CI"
```

### **Run Management**

**List runs:**
```bash
gh run list
```

**View run details:**
```bash
gh run view 12345
```

**Rerun failed run:**
```bash
gh run rerun 12345
```

**Download run artifacts:**
```bash
gh run download 12345
```

### **PR Checks**

**View PR checks:**
```bash
gh pr checks 123
```

**Wait for checks:**
```bash
gh pr checks 123 --watch
```

## 9. Custom Aliases (GitHub CLI)

### **Creating Aliases**

**Add simple alias:**
```bash
gh alias set prc 'pr create --web'
```

**Add complex alias:**
```bash
gh alias set review 'pr checkout $1 && pr diff'
```

**List aliases:**
```bash
gh alias list
```

**Delete alias:**
```bash
gh alias delete prc
```

### **Useful Aliases**

```bash
# Quick PR creation
gh alias set prnew 'pr create --fill'

# Status overview
gh alias set status 'repo view && echo "---" && pr status && echo "---" && issue list --limit 5'

# Branch cleanup
gh alias set clean-branches 'pr list --state merged --json number,headRefName --jq ".[].headRefName" | xargs -I {} git branch -D {}'
```

## 10. Advanced Git Techniques

### **Interactive Rebase**

**Rebase last 3 commits:**
```bash
git rebase -i HEAD~3
```

**Rebase onto different branch:**
```bash
git rebase --onto main feature-branch
```

### **Cherry Picking**

**Cherry pick commit:**
```bash
git cherry-pick abc123
```

**Cherry pick range:**
```bash
git cherry-pick abc123..def456
```

### **Stashing**

**Stash changes:**
```bash
git stash push -m "work in progress"
```

**Apply stash:**
```bash
git stash pop
```

**List stashes:**
```bash
git stash list
```

### **Reflog**

**View reflog:**
```bash
git reflog
```

**Recover lost commit:**
```bash
git checkout -b recovery abc123
```

## 11. GitHub CLI Extensions

### **Installing Extensions**

**Install extension:**
```bash
gh extension install owner/gh-extension-name
```

**List installed extensions:**
```bash
gh extension list
```

**Upgrade extensions:**
```bash
gh extension upgrade --all
```

### **Popular Extensions**

- `gh pr-cleanup` - Clean up merged branches
- `gh notify` - Notifications
- `gh dash` - Dashboard
- `gh copilot` - GitHub Copilot integration

## 12. Troubleshooting

### **Common Git Issues**

**Resolve merge conflicts:**
```bash
git status
# Edit conflicted files
git add resolved-file
git commit
```

**Undo last commit:**
```bash
git reset --soft HEAD~1  # keep changes
git reset --hard HEAD~1  # discard changes
```

**Recover deleted branch:**
```bash
git reflog
git checkout -b recovered-branch abc123
```

### **GitHub CLI Issues**

**Authentication problems:**
```bash
gh auth login
gh auth refresh
```

**Permission denied:**
```bash
gh auth status
# Check token scopes
```

**Rate limiting:**
```bash
gh api -H "Accept: application/vnd.github.v3+json" /rate_limit
```

## 13. Git Hooks and Automation

### **Local Git Hooks**

**Pre-commit hooks:**
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Run linting
npm run lint

# Run tests
npm test

# Check for secrets
git secrets --pre_commit_hook
```

**Pre-push hooks:**
```bash
#!/bin/sh
# .git/hooks/pre-push

# Run full test suite
npm run test:full

# Check code coverage
npm run coverage -- --check-coverage
```

### **Husky for Git Hooks**

**Setup Husky:**
```bash
npm install husky --save-dev
npx husky install

# Add hooks
npx husky add .husky/pre-commit "npm run lint"
npx husky add .husky/pre-push "npm test"
```

**Advanced Husky configuration:**
```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "pre-push": "npm run test"
    }
  },
  "lint-staged": {
    "*.{js,ts}": ["eslint --fix", "prettier --write"],
    "*.{md,mdx}": ["prettier --write"]
  }
}
```

### **Server-Side Hooks**

**GitHub webhooks integration:**
```yaml
# .github/workflows/webhook-handler.yml
name: Handle Webhooks

on:
  repository_dispatch:
    types: [custom-event]

jobs:
  handle:
    runs-on: ubuntu-latest
    steps:
      - name: Process webhook
        run: |
          echo "Event: ${{ github.event.action }}"
          echo "Sender: ${{ github.event.sender.login }}"
```

## 14. Advanced Rebasing Techniques

### **Interactive Rebase**

**Rebase with editing:**
```bash
git rebase -i HEAD~5
# Commands:
# pick - use commit
# reword - edit message
# edit - amend commit
# squash - merge with previous
# fixup - merge without message
# drop - remove commit
```

**Rebase onto different branch:**
```bash
git rebase --onto main feature-branch bugfix-branch
```

### **Rebase Conflict Resolution**

**During rebase conflicts:**
```bash
# Resolve conflicts in files
git add resolved-file

# Continue rebase
git rebase --continue

# Skip conflicted commit
git rebase --skip

# Abort rebase
git rebase --abort
```

### **Rebase Strategies**

**Linear history maintenance:**
```bash
# Before merging feature branch
git checkout feature-branch
git rebase main
git checkout main
git merge feature-branch  # Fast-forward merge
```

**Squashing for clean history:**
```bash
git rebase -i --autosquash main
# Automatically groups fixup! and squash! commits
```

## 15. Git Worktrees for Advanced Workflows

### **Worktree Basics**

**Create worktree:**
```bash
git worktree add ../feature-branch feature-branch
cd ../feature-branch
```

**List worktrees:**
```bash
git worktree list
```

**Remove worktree:**
```bash
git worktree remove feature-branch
```

### **Advanced Worktree Usage**

**Worktree for hotfixes:**
```bash
# Create worktree for hotfix
git worktree add -b hotfix/urgent-fix ../hotfix urgent-fix

# Work on hotfix
cd ../hotfix
# Make changes
git commit -m "fix: urgent security patch"

# Push and create PR
git push -u origin hotfix/urgent-fix
gh pr create --fill

# Remove worktree after merge
git worktree remove ../hotfix
```

**Worktree for experiments:**
```bash
# Create experimental worktree
git worktree add ../experiment experiment

# Experiment freely
cd ../experiment
git reset --hard HEAD~10  # Go back in history
# Test different approaches

# Discard experiment
cd ..
rm -rf experiment
git worktree prune
```

## 16. Git Configuration and Aliases

### **Global Git Configuration**

**Essential config:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "code --wait"
git config --global init.defaultBranch main
git config --global pull.rebase true
```

**Advanced config:**
```bash
# Enable rerere (reuse recorded resolution)
git config --global rerere.enabled true

# Better diff output
git config --global diff.algorithm histogram

# Colored output
git config --global color.ui auto

# Push only current branch
git config --global push.default simple
```

### **Powerful Git Aliases**

**Workflow aliases:**
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
```

**Advanced aliases:**
```bash
# Show contributors
git config --global alias.contributors 'shortlog -sn --no-merges'

# Find commits by message
git config --global alias.find 'log --all --grep'

# Show file history
git config --global alias.filelog 'log -p --follow --'

# Clean merged branches
git config --global alias.clean-branches '!git branch --merged | grep -v "\*" | xargs -n 1 git branch -d'
```

### **Conditional Configuration**

**Per-repository config:**
```bash
# .git/config
[user]
    name = Work Name
    email = work@example.com
[core]
    sshCommand = ssh -i ~/.ssh/work_key
```

**Include files:**
```bash
# ~/.gitconfig
[include]
    path = ~/.gitconfig-work
[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

## 17. Git and GitHub Integration Patterns

### **GitHub Apps Integration**

**Custom GitHub Apps:**
```javascript
// GitHub App webhook handler
app.on('pull_request.opened', async (context) => {
  const pr = context.payload.pull_request;

  // Auto-label PR based on branch
  if (pr.head.ref.startsWith('feature/')) {
    await context.github.issues.addLabels({
      owner: pr.base.repo.owner.login,
      repo: pr.base.repo.name,
      issue_number: pr.number,
      labels: ['enhancement']
    });
  }
});
```

### **GitHub API Integration**

**Automated repository management:**
```bash
# Create issue with GitHub CLI
gh issue create \
  --title "Automated: Dependency Update" \
  --body "Dependencies have been updated automatically." \
  --label "dependencies,automated"

# Bulk operations
gh issue list --label "stale" --json number --jq '.[].number' | \
  xargs -I {} gh issue edit {} --add-label "closing-soon"
```

### **CI/CD Integration**

**GitHub Actions with Git operations:**
```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for tags

      - name: Create release branch
        run: |
          git checkout -b release/v${{ github.event.inputs.version }}
          git push -u origin release/v${{ github.event.inputs.version }}

      - name: Create tag
        run: |
          git tag -a v${{ github.event.inputs.version }} -m "Release v${{ github.event.inputs.version }}"
          git push origin v${{ github.event.inputs.version }}
```

## 18. Performance and Optimization

### **Git Performance Tips**

**Shallow clones for CI:**
```bash
git clone --depth 1 https://github.com/user/repo.git
```

**Partial clones:**
```bash
git clone --filter=blob:none https://github.com/user/repo.git
# Download blobs on demand
```

**GC optimization:**
```bash
git config --global gc.auto 0  # Disable auto GC
git gc --aggressive --prune=now  # Manual optimization
```

### **Large Repository Handling**

**Git LFS for large files:**
```bash
git lfs install
git lfs track "*.psd" "*.mov"
git add .gitattributes
```

**Sparse checkout:**
```bash
git sparse-checkout init --cone
git sparse-checkout set docs/
```

### **Network Optimization**

**SSH multiplexing:**
```bash
# ~/.ssh/config
Host github.com
  ControlMaster auto
  ControlPath ~/.ssh/master-%r@%h:%p
  ControlPersist 10m
```

**Parallel fetching:**
```bash
git config --global fetch.parallel 0  # Use all CPUs
```

## 19. Security Best Practices

### **Git Security**

**Sign commits:**
```bash
git config --global commit.gpgsign true
git config --global user.signingkey YOUR_KEY_ID
```

**Verify signatures:**
```bash
git log --show-signature
```

### **GitHub Security**

**Branch protection:**
- Require PR reviews
- Require status checks
- Include administrators
- Restrict pushes

**Token management:**
```bash
# Use fine-grained tokens
gh auth login --scopes "repo,workflow"

# Rotate tokens regularly
gh auth refresh
```

### **Secret Management**

**GitHub secrets:**
```yaml
# Use in workflows
- name: Deploy
  run: |
    echo "API_KEY=${{ secrets.API_KEY }}" >> .env
```

**Environment-specific secrets:**
```yaml
jobs:
  deploy:
    environment: production
    steps:
      - run: echo "Using ${{ secrets.PROD_API_KEY }}"
```

## 20. Future of Git and GitHub

### **Emerging Features**

**Git improvements:**
- **Sparse index** for large repos
- **Multi-pack index** for performance
- **Commit graphs** for faster operations
- **Partial clones** evolution

**GitHub innovations:**
- **GitHub Copilot** integration
- **Codespaces** for cloud development
- **Discussions** for community engagement
- **Projects** for project management

### **Advanced Collaboration**

**GitHub Teams and Organizations:**
- **Nested teams** for complex hierarchies
- **Team sync** with identity providers
- **Organization-wide insights**
- **Enterprise features**

**Integration ecosystem:**
- **Marketplace apps** expansion
- **API evolution** with GraphQL
- **Webhook enhancements**
- **Real-time collaboration**

## Summary

Mastering Git and GitHub CLI commands transforms you from a basic user to a power contributor. Combine Git's version control with GitHub's collaboration features to streamline your development workflow. Regular practice and exploration of advanced features will make these commands second nature.

## Additional Resources

- [GitHub CLI Official Manual](https://cli.github.com/manual/)
- [Git Basics Documentation](https://git-scm.com/doc)
- [GitHub Pull Requests Documentation](https://docs.github.com/en/pull-requests)
- [Pro Git Book](https://git-scm.com/book/)
- [GitHub CLI Extensions](https://github.com/topics/gh-extension)