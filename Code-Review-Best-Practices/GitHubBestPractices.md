# GitHub Best Practices: Professional Repository Management

This guide covers advanced GitHub features, workflows, and best practices for maintaining professional open source projects. Master these techniques to streamline collaboration and project management.

---

## Table of Contents
1. [Repository Setup](#repository-setup)
2. [Branch Protection Rules](#branch-protection-rules)
3. [GitHub Actions & CI/CD](#github-actions--cicd)
4. [Issue Management](#issue-management)
5. [Pull Request Workflows](#pull-request-workflows)
6. [Release Management](#release-management)
7. [Security Practices](#security-practices)
8. [Community Management](#community-management)
9. [Analytics & Insights](#analytics--insights)
10. [Automation & Bots](#automation--bots)

---

## 1. Repository Setup

### **Repository Configuration**
```yaml
# .github/settings.yml (if using Probot settings)
repository:
  name: my-project
  description: "A professional open source project"
  homepage: https://my-project.dev
  topics: ["python", "api", "web-framework"]
  private: false
  has_issues: true
  has_projects: true
  has_wiki: false
  has_downloads: true
  default_branch: main
  allow_squash_merge: true
  allow_merge_commit: false
  allow_rebase_merge: true
  delete_branch_on_merge: true
```

### **Essential Files Structure**
```
.github/
├── workflows/           # CI/CD pipelines
│   ├── ci.yml          # Continuous integration
│   ├── release.yml     # Automated releases
│   └── security.yml    # Security scanning
├── ISSUE_TEMPLATE/      # Issue templates
│   ├── bug-report.md
│   ├── feature-request.md
│   └── question.md
├── PULL_REQUEST_TEMPLATE.md
└── CODEOWNERS          # Code ownership rules

docs/                    # Documentation
├── CONTRIBUTING.md     # Contribution guidelines
├── CODE_OF_CONDUCT.md  # Community standards
└── SECURITY.md         # Security policy

scripts/                # Automation scripts
├── release.sh
└── setup.sh
```

### **Repository Settings**
- **Features**:
  - [x] Issues
  - [x] Projects
  - [x] Wiki (if needed)
  - [x] Discussions (for community)
- **Merge Options**:
  - [x] Allow squash merging
  - [x] Allow rebase merging
  - [ ] Allow merge commits
  - [x] Delete head branches after merge

---

## 2. Branch Protection Rules

### **Main Branch Protection**
```yaml
# .github/workflows/branch-protection.yml
name: Branch Protection Check

on:
  pull_request:
    branches: [main, develop]

jobs:
  check-protection:
    runs-on: ubuntu-latest
    steps:
      - name: Check branch protection
        run: |
          # Custom logic to verify protection rules
          echo "Branch protection verified"
```

**Protection Rules for `main`:**
- [x] Require pull request reviews before merging
- [x] Require status checks to pass before merging
- [x] Require branches to be up to date before merging
- [x] Include administrators in restrictions
- [x] Restrict pushes that create matching branches
- [x] Allow force pushes (only by admins)
- [x] Allow deletions (only by admins)

**Required Status Checks:**
- CI tests (GitHub Actions)
- Code coverage (Codecov)
- Linting (ESLint, Pylint)
- Security scanning (Dependabot, Snyk)

### **Development Branch Strategy**
```bash
# Git Flow branching model
main          # Production-ready code
├── develop   # Integration branch
│   ├── feature/feature-name
│   ├── bugfix/bug-name
│   └── hotfix/critical-fix
└── release/v1.2.0  # Release preparation
```

---

## 3. GitHub Actions & CI/CD

### **CI Pipeline Template**
```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8, 3.9, "3.10", "3.11"]

    steps:
      - uses: actions/checkout@v4
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install -r requirements-dev.txt

      - name: Run linting
        run: |
          black --check .
          flake8 .
          mypy .

      - name: Run tests
        run: |
          pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
```

### **Advanced CI Features**
```yaml
# Matrix builds for multiple environments
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    python-version: [3.8, 3.9, "3.10", "3.11"]
    exclude:
      - os: macos-latest
        python-version: 3.8

# Caching for faster builds
- name: Cache pip dependencies
  uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements*.txt') }}

# Conditional jobs
jobs:
  test:
    if: github.event_name == 'pull_request'
    # Test job configuration

  deploy:
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    needs: test
    # Deploy job configuration
```

### **Automated Releases**
```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          body: |
            ## Changes
            - List of changes in this release
          draft: false
          prerelease: false
```

---

## 4. Issue Management

### **Issue Templates**
```markdown
<!-- .github/ISSUE_TEMPLATE/bug-report.md -->
---
name: Bug Report
about: Create a report to help us improve
title: "[BUG] "
labels: bug
assignees: ''

---

## Bug Description
A clear and concise description of what the bug is.

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

## Expected Behavior
A clear and concise description of what you expected to happen.

## Screenshots
If applicable, add screenshots to help explain your problem.

## Environment
- OS: [e.g., Windows 10]
- Browser: [e.g., Chrome 91]
- Version: [e.g., 1.0.0]

## Additional Context
Add any other context about the problem here.
```

### **Issue Labels System**
```yaml
# Label categories and colors
labels:
  # Priority
  - name: "priority: critical"
    color: "b60205"
    description: "Critical issue that blocks work"
  - name: "priority: high"
    color: "d93f0b"
    description: "High priority issue"
  - name: "priority: medium"
    color: "fbca04"
    description: "Medium priority issue"
  - name: "priority: low"
    color: "0e8a16"
    description: "Low priority issue"

  # Type
  - name: "type: bug"
    color: "d73a49"
    description: "Something isn't working"
  - name: "type: enhancement"
    color: "a2eeef"
    description: "New feature or request"
  - name: "type: documentation"
    color: "0075ca"
    description: "Documentation needed"
  - name: "type: question"
    color: "d876e3"
    description: "Question or discussion"

  # Status
  - name: "status: help wanted"
    color: "ffffff"
    description: "Community contribution welcome"
  - name: "status: good first issue"
    color: "7057ff"
    description: "Good for newcomers"
  - name: "status: in progress"
    color: "ffa500"
    description: "Work in progress"
```

### **Issue Management Workflow**
1. **Triage**: Label and prioritize new issues
2. **Assign**: Assign to appropriate team members
3. **Track**: Use projects for organization
4. **Close**: Close resolved issues with references
5. **Archive**: Archive stale issues periodically

---

## 5. Pull Request Workflows

### **PR Template**
```markdown
<!-- .github/PULL_REQUEST_TEMPLATE.md -->
## Description
Please include a summary of the change and which issue is fixed. Please also include relevant motivation and context.

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Refactoring (no functional changes)
- [ ] Performance improvement

## How Has This Been Tested?
- [ ] Unit tests
- [ ] Integration tests
- [ ] Manual testing
- [ ] All tests pass

## Checklist:
- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes

## Screenshots (if appropriate):

## Additional Notes:
```

### **PR Automation**
```yaml
# .github/workflows/pr-checks.yml
name: PR Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  pr-validation:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Validate PR title
        uses: amannn/action-semantic-pull-request@v5
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Check PR size
        uses: codacy/git-version@2.7.1
        with:
          release-branch: main

      - name: Auto-assign reviewers
        uses: kentaro-m/auto-assign-action@v1.2.4
        with:
          configuration-path: .github/auto-assign.yml
```

### **Code Owners**
```ini
# .github/CODEOWNERS
# Global owners
* @project-maintainers

# Specific directories
/src/api/ @api-team
/src/frontend/ @frontend-team
/docs/ @docs-team

# Specific files
package.json @platform-team
Dockerfile @devops-team
```

---

## 6. Release Management

### **Versioning Strategy**
Follow [Semantic Versioning](https://semver.org/):
```
MAJOR.MINOR.PATCH
│     │    │
│     │    └─ Bug fixes
│     └─ New features (backward compatible)
└─ Breaking changes
```

### **Release Process**
```yaml
# .github/workflows/release.yml
name: Create Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate changelog
        uses: tj-actions/git-cliff@v1
        id: git-cliff
        with:
          configuration: cliff.toml
          args: --verbose --latest --strip header
        env:
          OUTPUT: CHANGELOG.md

      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          body: ${{ steps.git-cliff.outputs.content }}
          draft: false
          prerelease: false

      - name: Publish to PyPI
        if: startsWith(github.ref, 'refs/tags/v')
        uses: pypa/gh-action-pypi-publish@release/v1
        with:
          password: ${{ secrets.PYPI_API_TOKEN }}
```

### **Changelog Generation**
```toml
# cliff.toml
[changelog]
header = """
# Changelog\n
All notable changes to this project will be documented in this file.\n
"""
body = """
{%- if version %}\
    ## [{{ version | trim_start_matches(pat="v") }}] - {{ timestamp | date(format="%Y-%m-%d") }}
{%- else -%}
    ## [unreleased]
{%- endif %}\

{% for group, commits in commits | group_by(attribute="group") %}
    ### {{ group | upper_first }}
    {% for commit in commits %}
        - {% if commit.scope %}**{{commit.scope}}**: {% endif %}\
            {% if commit.breaking %}[**breaking**] {% endif %}\
            {{ commit.message | upper_first }}
    {%- endfor -%}
{% endfor %}\n
"""
```

---

## 7. Security Practices

### **Security Policy**
```markdown
<!-- SECURITY.md -->
# Security Policy

## Supported Versions
We actively support the following versions with security updates:

| Version | Supported          |
| ------- | ------------------ |
| 2.1.x   | :white_check_mark: |
| 2.0.x   | :white_check_mark: |
| < 2.0   | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability, please report it to us as follows:

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, please report security vulnerabilities by emailing:
- security@example.com

You should receive a response within 24 hours. If for some reason you do not, please follow up via email to ensure we received your original message.

Please include the following information in your report:
- A clear description of the vulnerability
- Steps to reproduce the issue
- Potential impact of the vulnerability
- Any suggested fixes or mitigations
```

### **Security Scanning**
```yaml
# .github/workflows/security.yml
name: Security Scan

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Mondays

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        if: always()
        with:
          sarif_file: 'trivy-results.sarif'

      - name: Run Snyk to check for vulnerabilities
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --file=requirements.txt
```

### **Dependabot Configuration**
```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    reviewers:
      - "team-platform"
    assignees:
      - "platform-lead"
    commit-message:
      prefix: "deps"
      include: "scope"

  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

---

## 8. Community Management

### **Community Health Files**
- **Code of Conduct**: Defines acceptable behavior
- **Contributing Guide**: How to contribute effectively
- **Issue and PR Templates**: Standardized reporting
- **Security Policy**: How to report vulnerabilities
- **License**: Project licensing terms

### **Discussion Management**
```yaml
# .github/workflows/discussion-automation.yml
name: Discussion Management

on:
  discussion:
    types: [created, answered]

jobs:
  manage-discussions:
    runs-on: ubuntu-latest
    steps:
      - name: Add welcome comment
        if: github.event.action == 'created'
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: 'Thanks for starting this discussion! The team will review and respond soon.'
            })

      - name: Label discussions
        uses: actions/github-script@v6
        with:
          script: |
            // Auto-label based on content
            const title = context.payload.discussion.title.toLowerCase()
            const labels = []

            if (title.includes('help')) labels.push('help wanted')
            if (title.includes('idea') || title.includes('suggestion')) labels.push('enhancement')
            if (title.includes('bug')) labels.push('bug')

            if (labels.length > 0) {
              github.rest.issues.addLabels({
                issue_number: context.issue.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                labels: labels
              })
            }
```

### **Community Analytics**
- **Traffic Analytics**: Repository views and clones
- **Community Insights**: Contributor statistics
- **Issue/PR Metrics**: Response times, resolution rates
- **Dependency Insights**: Security vulnerabilities

---

## 9. Analytics & Insights

### **Repository Insights**
GitHub provides built-in analytics:
- **Traffic**: Views, unique visitors, popular content
- **Commits**: Contribution patterns over time
- **Code frequency**: Additions/deletions over time
- **Contributors**: Active contributors and their impact
- **Network**: Forks and stars over time

### **Custom Analytics**
```yaml
# .github/workflows/analytics.yml
name: Generate Analytics

on:
  schedule:
    - cron: '0 0 1 * *'  # Monthly
  workflow_dispatch:

jobs:
  analytics:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate contributor stats
        run: |
          echo "# Contributor Statistics" > analytics.md
          echo "" >> analytics.md
          git shortlog -sn --no-merges | head -20 >> analytics.md

      - name: Generate code metrics
        run: |
          echo "## Code Metrics" >> analytics.md
          echo "- Total lines: $(find src -name "*.py" -exec wc -l {} \; | awk '{sum+=$1} END {print sum}')" >> analytics.md
          echo "- Total files: $(find src -name "*.py" | wc -l)" >> analytics.md

      - name: Commit analytics
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "docs: update monthly analytics"
          file_pattern: analytics.md
```

---

## 10. Automation & Bots

### **GitHub Apps and Bots**
- **Dependabot**: Automated dependency updates
- **CodeQL**: Security vulnerability scanning
- **Stale**: Close inactive issues/PRs
- **Auto-assign**: Automatically assign reviewers
- **Welcome**: Greet new contributors
- **Release Drafter**: Generate release notes

### **Probot Apps**
```javascript
// .github/probot.js (example)
module.exports = (app) => {
  app.on('issues.opened', async (context) => {
    const issueComment = context.issue({
      body: 'Thanks for opening this issue! The team will review it soon.'
    })
    return context.github.issues.createComment(issueComment)
  })

  app.on('pull_request.opened', async (context) => {
    const prComment = context.issue({
      body: 'Thanks for the PR! Please make sure to update tests and documentation.'
    })
    return context.github.issues.createComment(prComment)
  })
}
```

### **Custom Automation Scripts**
```bash
# scripts/automate-releases.sh
#!/bin/bash

# Automated release script
VERSION=$1
BRANCH="release/v$VERSION"

# Create release branch
git checkout -b $BRANCH

# Update version files
echo $VERSION > VERSION
sed -i "s/version = .*/version = \"$VERSION\"/" pyproject.toml

# Commit changes
git add VERSION pyproject.toml
git commit -m "chore: bump version to $VERSION"

# Create PR
gh pr create --title "Release v$VERSION" --body "Automated release PR" --base main
```

---

## Quick Reference

### **Essential GitHub Features**
```bash
# Repository management
gh repo create my-project --public
gh repo clone my-org/my-project

# Issue management
gh issue list --label "bug"
gh issue create --title "Bug title" --body "Description"
gh issue edit 123 --add-label "priority: high"

# PR management
gh pr list
gh pr create --title "Feature title" --body "Description"
gh pr review 456 --approve
gh pr merge 456

# Release management
gh release create v1.0.0 --title "Release v1.0.0"
```

### **Key Workflows**
- **CI/CD**: Automated testing and deployment
- **Security**: Vulnerability scanning and updates
- **Releases**: Automated versioning and publishing
- **Community**: Issue triage and contributor management

### **Best Practice Checklist**
- [ ] Branch protection rules configured
- [ ] CI/CD pipelines set up
- [ ] Issue and PR templates created
- [ ] Code owners defined
- [ ] Security scanning enabled
- [ ] Dependabot configured
- [ ] Community health files present
- [ ] Analytics and insights monitored

---

## Additional Resources

- [GitHub Documentation](https://docs.github.com/)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
- [Probot Apps](https://probot.github.io/)
- [GitHub Community Guidelines](https://docs.github.com/en/github/site-policy/github-community-guidelines)
- [Open Source Guides](https://opensource.guide/)

---

*Mastering these GitHub best practices will help you maintain a professional, efficient, and welcoming open source project. Regular review and updates to your processes will ensure continued success.* 🚀