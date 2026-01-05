# GitHub Issues, Labels, Milestones, and Projects Best Practices

This guide covers best practices for using GitHub's project management tools effectively.

## GitHub Issues

### Creating Effective Issues

**Bug Reports:**
```
Title: [BUG] Login button not working on mobile devices

Description:
## Description
Login button is unresponsive on iOS Safari

## Steps to Reproduce
1. Open app on iOS Safari
2. Navigate to login page
3. Click login button
4. Nothing happens

## Expected Behavior
Login form should submit

## Actual Behavior
Button is unresponsive

## Environment
- OS: iOS 15.2
- Browser: Safari 15.2
- App Version: 2.1.0

## Additional Context
Works fine on Chrome desktop
```

**Feature Requests:**
```
Title: [FEATURE] Add dark mode support

Description:
## Problem
Users working in low-light environments strain their eyes

## Solution
Implement a dark mode theme toggle

## Alternatives Considered
- Automatic system theme detection
- Manual theme selection in settings

## Additional Context
Popular apps like Slack and Discord have this feature
```

### Issue Templates

Create `.github/ISSUE_TEMPLATE/` directory with templates:

**Bug Report Template:**
```markdown
---
name: Bug Report
about: Create a report to help us improve
title: "[BUG] "
labels: bug
assignees: ''

---

**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Environment:**
 - OS: [e.g. iOS]
 - Browser [e.g. chrome, safari]
 - Version [e.g. 22]

**Additional context**
Add any other context about the problem here.
```

## Labels

### Label Categories

**Type Labels:**
- `bug` - Something isn't working
- `feature` - New feature or enhancement
- `documentation` - Documentation improvements
- `question` - Further information needed
- `enhancement` - Improvement to existing feature

**Priority Labels:**
- `priority: critical` - Must fix immediately
- `priority: high` - Should fix soon
- `priority: medium` - Fix when possible
- `priority: low` - Nice to have

**Status Labels:**
- `status: in-progress` - Currently being worked on
- `status: blocked` - Blocked by another issue
- `status: review-needed` - Needs code review
- `status: ready-for-qa` - Ready for testing

**Area Labels:**
- `area: frontend` - Frontend related
- `area: backend` - Backend related
- `area: api` - API related
- `area: database` - Database related

### Label Management

```bash
# Create labels via GitHub CLI
gh label create "priority: critical" --color "B60205" --description "Must fix immediately"
gh label create "area: frontend" --color "0E8A16" --description "Frontend related issues"
```

## Milestones

### Creating Milestones

1. Go to Issues > Milestones
2. Click "New milestone"
3. Set title, description, and due date
4. Assign issues to milestones

### Best Practices

- **Version-Based**: Use semantic versioning (v1.0.0, v2.1.3)
- **Time-Based**: Sprint milestones (Sprint 23, Q1 2024)
- **Feature-Based**: Feature milestones (User Authentication, Payment System)

### Tracking Progress

```bash
# View milestone progress
gh milestone view "v1.0.0"

# List issues in milestone
gh issue list --milestone "v1.0.0"
```

## GitHub Projects

### Project Types

**Table View:**
- Spreadsheet-like view
- Good for tracking status across many items
- Easy to sort and filter

**Board View:**
- Kanban-style boards
- Visual workflow representation
- Good for team collaboration

### Setting Up a Project

1. Go to Projects tab
2. Click "New project"
3. Choose template or start from scratch
4. Add columns/fields for your workflow

### Common Workflows

**Software Development:**
- Backlog
- Ready for Development
- In Progress
- In Review
- Ready for Testing
- Done

**Content Creation:**
- Ideas
- Research
- Draft
- Review
- Publish

### Automation

Use GitHub's built-in automation:

```yaml
# Automatically move issues when labeled
- When an issue is labeled with "ready"
  Then move to "Ready for Development"

# Auto-assign reviewers
- When a pull request is opened
  Then add assignees based on CODEOWNERS
```

## Integration Best Practices

### Linking Issues and PRs

```markdown
<!-- In PR description -->
Closes #123
Fixes #456
Related to #789
```

### Branch Naming

```bash
# Feature branches
git checkout -b feature/add-dark-mode

# Bug fixes
git checkout -b bugfix/login-mobile

# Hotfixes
git checkout -b hotfix/security-patch
```

### Commit Messages

```bash
git commit -m "feat: add dark mode toggle (#123)

- Add theme context
- Implement dark/light themes
- Add theme persistence

Closes #123"
```

## Advanced Features

### Issue Forms (Beta)

Create structured issue forms using YAML:

```yaml
# .github/ISSUE_TEMPLATE/bug-report.yml
name: Bug Report
description: File a bug report
body:
  - type: markdown
    attributes:
      value: |
        Thanks for taking the time to fill out this bug report!
  - type: input
    id: contact
    attributes:
      label: Contact Details
      description: How can we get in touch with you if we need more info?
      placeholder: ex. email@example.com
    validations:
      required: false
  - type: textarea
    id: what-happened
    attributes:
      label: What happened?
      description: Also tell us, what did you expect to happen?
      placeholder: Tell us what you see!
      value: "A bug happened!"
    validations:
      required: true
```

### Project Insights

- Track velocity and burndown charts
- Monitor team productivity
- Identify bottlenecks in workflow

## Tools and Integrations

### GitHub CLI

```bash
# Create issue
gh issue create --title "Add dark mode" --body "Implement dark theme toggle"

# List issues
gh issue list --label "priority: high"

# View project
gh project view 1
```

### Third-Party Tools

- **ZenHub**: Enhanced project management
- **Linear**: Issue tracking and project management
- **Clubhouse**: Story-based project management

## Best Practices Summary

1. **Consistent Labeling**: Establish clear labeling conventions
2. **Regular Grooming**: Review and update issues regularly
3. **Clear Descriptions**: Write detailed, actionable issue descriptions
4. **Link Everything**: Connect issues, PRs, and milestones
5. **Automate Routine Tasks**: Use GitHub's automation features
6. **Monitor Progress**: Use milestones and projects for tracking
7. **Communicate Clearly**: Keep stakeholders informed of progress