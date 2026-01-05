# Reusable GitHub Actions Workflows

This guide explains how to create and use callable/reusable GitHub Actions workflows for efficient CI/CD management.

## Overview

Reusable workflows allow you to define a workflow in one repository and call it from workflows in other repositories, promoting code reuse and consistency across projects.

## Creating a Reusable Workflow

### 1. Define the Reusable Workflow

Create a workflow file in `.github/workflows/` with the `workflow_call` trigger:

```yaml
name: Reusable CI Workflow

on:
  workflow_call:
    inputs:
      node-version:
        required: false
        type: string
        default: '18'
      environment:
        required: false
        type: string
        default: 'development'
    secrets:
      token:
        required: true

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

### 2. Call the Reusable Workflow

From another workflow, call it using `uses`:

```yaml
name: CI

on:
  push:
    branches: [ main ]

jobs:
  call-reusable-workflow:
    uses: ./.github/workflows/reusable-ci.yml
    with:
      node-version: '20'
      environment: 'production'
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

## Best Practices

1. **Input Validation**: Always validate inputs in your reusable workflows
2. **Secret Management**: Use secrets carefully and document required secrets
3. **Version Pinning**: Pin workflow versions to ensure stability
4. **Error Handling**: Implement proper error handling and status reporting
5. **Documentation**: Document inputs, outputs, and required secrets clearly

## Advanced Usage

### Using Outputs

```yaml
# In reusable workflow
outputs:
  test-results:
    value: ${{ jobs.test.outputs.results }}

# In calling workflow
jobs:
  call-workflow:
    uses: ./.github/workflows/reusable-ci.yml
    outputs:
      results: ${{ jobs.call-workflow.outputs.test-results }}
```

### Conditional Workflows

```yaml
jobs:
  call-workflow:
    if: github.event_name == 'pull_request'
    uses: ./.github/workflows/reusable-ci.yml
```

## Benefits

- **DRY Principle**: Avoid duplicating workflow logic
- **Consistency**: Ensure uniform CI/CD across repositories
- **Maintenance**: Update workflows in one place
- **Security**: Centralized control over sensitive operations
- **Scalability**: Easy to extend and modify workflows