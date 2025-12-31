# GitHub Automation: Scripts, Tools, and Workflow Optimization

This guide covers advanced automation techniques for GitHub workflows, including custom scripts, CLI tools, and integration strategies to streamline your open source contribution process.

---

## 1. GitHub CLI (gh) Advanced Usage

### **Custom Aliases for Efficiency**
```bash
# Set up powerful aliases
gh alias set prc 'pr create --fill --web'
gh alias set prm 'pr merge --merge --delete-branch'
gh alias set iss 'issue list --assignee @me'
gh alias set repo-sync 'repo sync --force'

# List all aliases
gh alias list
```

### **Bulk Operations with gh**
```bash
# Bulk close issues
gh issue list --label "wontfix" --json number --jq '.[].number' | xargs -I {} gh issue close {}

# Bulk assign reviewers
gh pr list --json number --jq '.[].number' | xargs -I {} gh pr edit {} --add-reviewer maintainer1,maintainer2

# Auto-label PRs based on file changes
gh pr view $PR_NUMBER --json files --jq '.files[].path' | grep -q "docs/" && gh pr edit $PR_NUMBER --add-label documentation
```

---

## 2. Custom Automation Scripts

### **PR Quality Checker Script**
```bash
#!/bin/bash
# pr-quality-check.sh

PR_NUMBER=$1
REPO="owner/repo"

echo "🔍 Checking PR #$PR_NUMBER quality..."

# Check if tests are included
if ! gh pr view $PR_NUMBER --json files --jq '.files[].path' | grep -q "test"; then
    echo "❌ No test files found!"
    exit 1
fi

# Check PR size
LINES_CHANGED=$(gh pr view $PR_NUMBER --json additions,deletions --jq '.additions + .deletions')
if [ "$LINES_CHANGED" -gt 1000 ]; then
    echo "⚠️  Large PR detected ($LINES_CHANGED lines). Consider splitting."
fi

# Check for conventional commit
TITLE=$(gh pr view $PR_NUMBER --json title --jq '.title')
if ! echo "$TITLE" | grep -qE "^(feat|fix|docs|style|refactor|test|chore): "; then
    echo "⚠️  PR title doesn't follow conventional commit format"
fi

echo "✅ PR quality check completed"
```

### **Automated Fork Sync Script**
```bash
#!/bin/bash
# sync-fork.sh

UPSTREAM="original-owner/repo"
FORK="your-username/repo"

echo "🔄 Syncing fork with upstream..."

# Fetch upstream
gh repo clone $FORK temp-repo -- --depth 1
cd temp-repo
git remote add upstream https://github.com/$UPSTREAM.git
git fetch upstream

# Check if main branch needs sync
if git log HEAD..upstream/main --oneline | grep -q .; then
    echo "📦 Updates found. Creating sync PR..."
    git checkout -b sync-upstream-$(date +%Y%m%d)
    git reset --hard upstream/main
    git push origin sync-upstream-$(date +%Y%m%d)

    # Create PR
    gh pr create --title "chore: sync with upstream" \
                 --body "Automated sync with upstream repository" \
                 --base main --head sync-upstream-$(date +%Y%m%d)
else
    echo "✅ Fork is up to date"
fi

cd ..
rm -rf temp-repo
```

### **Contribution Tracker Script**
```bash
#!/bin/bash
# contribution-tracker.sh

USERNAME="your-username"
REPO="target/repo"

echo "📊 Contribution Summary for $USERNAME in $REPO"

# Count PRs
PR_COUNT=$(gh pr list --author $USERNAME --repo $REPO --state all --json number --jq length)
echo "Pull Requests: $PR_COUNT"

# Count Issues
ISSUE_COUNT=$(gh issue list --author $USERNAME --repo $REPO --state all --json number --jq length)
echo "Issues Created: $ISSUE_COUNT"

# Recent activity
echo "Recent PRs:"
gh pr list --author $USERNAME --repo $REPO --limit 5 --json title,number,state,createdAt --template '{{range .}}{{tablerow .title .number .state (timeago .createdAt)}}{{end}}'
```

---

## 3. Git Hooks for Automation

### **pre-commit Hook for Quality Checks**
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "🔍 Running pre-commit checks..."

# Run linter
if command -v ruff &> /dev/null; then
    ruff check . || exit 1
fi

# Run type checker
if command -v mypy &> /dev/null; then
    mypy . || exit 1
fi

# Check for large files
find . -type f -size +50M | grep -q . && echo "⚠️  Large files detected" && exit 1

# Check commit message format
COMMIT_MSG=$(cat .git/COMMIT_EDITMSG)
if ! echo "$COMMIT_MSG" | grep -qE "^(feat|fix|docs|style|refactor|test|chore): "; then
    echo "❌ Commit message doesn't follow conventional format"
    exit 1
fi

echo "✅ All checks passed"
```

### **pre-push Hook for PR Validation**
```bash
#!/bin/bash
# .git/hooks/pre-push

echo "🚀 Running pre-push validation..."

# Check if branch is up to date
if ! git diff --quiet HEAD origin/main; then
    echo "⚠️  Branch is not up to date with main"
fi

# Run tests
if [ -f "pytest.ini" ] || [ -f "pyproject.toml" ]; then
    python -m pytest --tb=short || exit 1
fi

# Check for secrets
if command -v gitleaks &> /dev/null; then
    gitleaks detect --verbose --redact || exit 1
fi

echo "✅ Pre-push validation completed"
```

---

## 4. GitHub Actions Automation

### **Auto-label PRs Based on Content**
```yaml
# .github/workflows/auto-label.yml
name: Auto Label PRs

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v4
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
          configuration-path: .github/labeler.yml
```

```yaml
# .github/labeler.yml
documentation:
  - docs/**/*
  - README.md
  - CONTRIBUTING.md

tests:
  - tests/**/*
  - test_*.py

bugfix:
  - any: ['fix:*', 'hotfix:*']

feature:
  - any: ['feat:*', 'feature/*']
```

### **Automated Dependency Updates**
```yaml
# .github/workflows/dependabot.yml
name: Dependabot
on:
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Mondays

jobs:
  dependabot:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: dependabot/dependabot-core@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### **PR Size Checker**
```yaml
# .github/workflows/pr-size.yml
name: PR Size Check

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  size:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: codacy/git-version@v2
        id: version
      - name: Check PR size
        run: |
          ADDITIONS=$(gh pr view ${{ github.event.pull_request.number }} --json additions --jq '.additions')
          DELETIONS=$(gh pr view ${{ github.event.pull_request.number }} --json deletions --jq '.deletions')
          TOTAL=$((ADDITIONS + DELETIONS))

          if [ $TOTAL -gt 1000 ]; then
            echo "⚠️ Large PR detected ($TOTAL lines changed)"
            gh pr comment ${{ github.event.pull_request.number }} --body "⚠️ This PR is quite large ($TOTAL lines). Consider splitting it into smaller, focused PRs."
          fi
```

---

## 5. Integration with External Tools

### **Slack Notifications for PR Events**
```bash
#!/bin/bash
# slack-pr-notification.sh

WEBHOOK_URL="your-slack-webhook-url"
PR_NUMBER=$1
REPO=$2

PR_INFO=$(gh pr view $PR_NUMBER --repo $REPO --json title,author,url --jq '{title: .title, author: .author.login, url: .url}')

curl -X POST -H 'Content-type: application/json' \
  --data "{\"text\":\"New PR: ${PR_INFO}\"}" \
  $WEBHOOK_URL
```

### **Automated Release Notes**
```yaml
# .github/workflows/release-notes.yml
name: Generate Release Notes

on:
  release:
    types: [published]

jobs:
  release-notes:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Generate release notes
        run: |
          # Extract PRs merged since last release
          LAST_TAG=$(git describe --tags --abbrev=0 HEAD~1)
          PRS=$(git log --oneline --grep="Merge pull request" $LAST_TAG..HEAD)

          # Generate formatted release notes
          echo "# Release Notes" > release-notes.md
          echo "## Changes" >> release-notes.md
          echo "$PRS" >> release-notes.md

      - uses: actions/upload-artifact@v3
        with:
          name: release-notes
          path: release-notes.md
```

### **Automated Code Review Comments**
```python
# auto-review.py
import os
import requests
from github import Github

def post_review_comment(pr_number, comments):
    g = Github(os.getenv('GITHUB_TOKEN'))
    repo = g.get_repo(os.getenv('GITHUB_REPOSITORY'))
    pr = repo.get_pull(pr_number)

    for comment in comments:
        pr.create_review_comment(
            body=comment['body'],
            commit_id=pr.head.sha,
            path=comment['path'],
            position=comment['position']
        )

# Example usage
comments = [
    {
        'body': 'Consider adding type hints for better code clarity',
        'path': 'src/main.py',
        'position': 10
    }
]

post_review_comment(os.getenv('PR_NUMBER'), comments)
```

---

## 6. Advanced Workflow Templates

### **Hacktoberfest Contribution Workflow**
```yaml
# .github/workflows/hacktoberfest.yml
name: Hacktoberfest Validator

on:
  pull_request_target:
    types: [opened, reopened, synchronize, closed]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Validate Hacktoberfest PR
        run: |
          # Check if PR has hacktoberfest-accepted label
          LABELS=$(gh pr view ${{ github.event.pull_request.number }} --json labels --jq '.labels[].name')

          if echo "$LABELS" | grep -q "hacktoberfest-accepted"; then
            echo "✅ PR accepted for Hacktoberfest"
          else
            echo "⏳ PR pending Hacktoberfest review"
          fi

      - name: Check PR quality
        if: github.event.action != 'closed'
        run: |
          # Run quality checks
          ./scripts/pr-quality-check.sh ${{ github.event.pull_request.number }}
```

### **Automated Backport Workflow**
```yaml
# .github/workflows/backport.yml
name: Backport PR

on:
  pull_request:
    types: [closed]

jobs:
  backport:
    if: github.event.pull_request.merged == true && contains(github.event.pull_request.labels.*.name, 'backport')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Backport to stable branch
        run: |
          git checkout stable
          git cherry-pick ${{ github.event.pull_request.merge_commit_sha }}
          git push origin stable
```

---

## 7. Monitoring and Analytics

### **Contribution Analytics Dashboard**
```python
# contribution-analytics.py
import requests
from datetime import datetime, timedelta

def get_contribution_stats(username, repo):
    # GitHub API calls to gather stats
    api_url = f"https://api.github.com/repos/{repo}/pulls"

    params = {
        'state': 'all',
        'creator': username,
        'per_page': 100
    }

    response = requests.get(api_url, params=params)
    prs = response.json()

    stats = {
        'total_prs': len(prs),
        'merged_prs': len([pr for pr in prs if pr['merged_at']]),
        'open_prs': len([pr for pr in prs if pr['state'] == 'open']),
        'avg_time_to_merge': calculate_avg_merge_time(prs)
    }

    return stats

def calculate_avg_merge_time(prs):
    merge_times = []
    for pr in prs:
        if pr['merged_at'] and pr['created_at']:
            created = datetime.fromisoformat(pr['created_at'].replace('Z', '+00:00'))
            merged = datetime.fromisoformat(pr['merged_at'].replace('Z', '+00:00'))
            merge_times.append((merged - created).days)

    return sum(merge_times) / len(merge_times) if merge_times else 0
```

### **Repository Health Monitor**
```bash
#!/bin/bash
# repo-health-monitor.sh

REPO="owner/repo"

echo "🏥 Repository Health Check for $REPO"

# Check CI status
CI_STATUS=$(gh api repos/$REPO/actions/runs --jq '.workflow_runs[0].conclusion')
echo "CI Status: $CI_STATUS"

# Check open issues
OPEN_ISSUES=$(gh issue list --repo $REPO --state open --json number --jq length)
echo "Open Issues: $OPEN_ISSUES"

# Check PR review times
PR_STATS=$(gh pr list --repo $REPO --state open --json createdAt,updatedAt --jq 'group_by(.createdAt | strptime("%Y-%m-%dT%H:%M:%SZ") | mktime | strftime("%U")) | map({week: .[0].createdAt, count: length})')
echo "PRs by week: $PR_STATS"

# Generate health report
if [ "$CI_STATUS" = "success" ] && [ "$OPEN_ISSUES" -lt 50 ]; then
    echo "✅ Repository is healthy"
else
    echo "⚠️  Repository needs attention"
fi
```

---

## 8. Best Practices for Automation

### **Security Considerations**
- Store sensitive data in GitHub Secrets
- Use least-privilege tokens
- Regularly rotate API keys
- Audit automation scripts for vulnerabilities

### **Performance Optimization**
- Use caching for expensive operations
- Implement rate limiting for API calls
- Batch operations when possible
- Monitor resource usage

### **Maintainability**
- Document all automation scripts
- Use version control for automation code
- Test automation in staging environments
- Keep automation code DRY (Don't Repeat Yourself)

### **Monitoring and Alerting**
- Set up alerts for automation failures
- Log automation activities
- Monitor API rate limits
- Track automation success metrics

---

## Summary

Automation is key to efficient open source contribution. By leveraging GitHub CLI, custom scripts, Git hooks, and GitHub Actions, you can:

- **Streamline workflows** with custom aliases and scripts
- **Ensure quality** with automated checks and validations
- **Scale contributions** with bulk operations and templates
- **Monitor progress** with analytics and health checks

Start small with basic aliases and scripts, then gradually build more complex automation as you become comfortable with the tools.

**Additional Resources**
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Git Hooks Documentation](https://git-scm.com/docs/githooks)

---

```text
project="OpenSourceContribution" file="GitHubAutomation.md" version=1
GitHub Automation: Scripts, Tools, and Workflow Optimization

This guide covers advanced automation techniques for GitHub workflows, including custom scripts, CLI tools, and integration strategies to streamline your open source contribution process.