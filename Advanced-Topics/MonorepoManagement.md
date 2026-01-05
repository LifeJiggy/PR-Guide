# Monorepo Management

This guide covers tools and strategies for managing large repositories, including Git LFS, submodules, and modern tools like Nx/Turbo.

## Understanding Monorepos

A monorepo (monolithic repository) contains multiple projects in a single repository, allowing for:
- Shared code and dependencies
- Atomic changes across projects
- Simplified dependency management
- Consistent tooling and processes

## Git LFS (Large File Storage)

### When to Use Git LFS

Git LFS is ideal for:
- Large binary files (images, videos, datasets)
- Model files and ML artifacts
- Design assets and prototypes
- Build artifacts and releases

### Setup and Configuration

```bash
# Install Git LFS
git lfs install

# Track file types
git lfs track "*.psd"
git lfs track "*.mov"
git lfs track "*.zip"

# Track specific files
git lfs track "models/*.pkl"
git lfs track "datasets/*.csv"
```

### .gitattributes Configuration

```gitattributes
# Images
*.png filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.gif filter=lfs diff=lfs merge=lfs -text

# Videos
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.mov filter=lfs diff=lfs merge=lfs -text

# Archives
*.zip filter=lfs diff=lfs merge=lfs -text
*.tar.gz filter=lfs diff=lfs merge=lfs -text

# Data files
*.csv filter=lfs diff=lfs merge=lfs -text
*.json filter=lfs diff=lfs merge=lfs -text
```

### Best Practices

1. **Selective Tracking:** Only track files that change frequently
2. **Compression:** Use compressed formats when possible
3. **Cleanup:** Regularly clean up old LFS objects
4. **Bandwidth:** Monitor LFS bandwidth usage

## Git Submodules

### Basic Usage

```bash
# Add a submodule
git submodule add https://github.com/user/library.git libs/library

# Clone with submodules
git clone --recursive https://github.com/user/project.git

# Update submodules
git submodule update --init --recursive
```

### Advanced Submodule Management

```bash
# Update all submodules to latest
git submodule foreach git pull origin main

# Check submodule status
git submodule status

# Remove a submodule
git submodule deinit -f libs/library
git rm libs/library
rm -rf .git/modules/libs/library
```

### Submodule Workflows

**Pinned Versions:**
```bash
# Pin to specific commit
cd libs/library
git checkout v1.2.3
cd ../..
git add libs/library
git commit -m "Pin library to v1.2.3"
```

**Branch Tracking:**
```bash
# Track a branch
git submodule set-branch --branch develop libs/library
```

## Modern Monorepo Tools

### Nx

**Setup:**
```bash
npx create-nx-workspace@latest myorg --preset=empty
```

**Project Structure:**
```
apps/
├── api/
├── web/
└── mobile/
libs/
├── ui/
├── data-access/
└── utils/
```

**Task Running:**
```bash
# Run tests for affected projects
nx affected:test

# Build all projects
nx run-many --target=build --all

# Graph dependencies
nx graph
```

**Configuration (nx.json):**
```json
{
  "npmScope": "myorg",
  "affected": {
    "defaultBase": "main"
  },
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build", "lint", "test", "e2e"]
      }
    }
  }
}
```

### Turborepo

**Setup:**
```bash
npx create-turbo@latest my-project
```

**Configuration (turbo.json):**
```json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": ["**/.env.*local"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "lint": {},
    "test": {},
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

**Pipeline Configuration:**
```json
{
  "build": {
    "dependsOn": ["^build"],
    "outputs": ["dist/**"],
    "env": ["NODE_ENV"]
  },
  "test": {
    "dependsOn": ["build"],
    "inputs": ["src/**/*.tsx", "src/**/*.ts", "test/**/*.ts"],
    "outputs": ["coverage/**"]
  }
}
```

## Repository Organization Strategies

### Folder Structure

```
monorepo/
├── apps/           # Applications
│   ├── web/
│   ├── api/
│   └── mobile/
├── packages/       # Shared packages
│   ├── ui/
│   ├── utils/
│   └── config/
├── tools/          # Build tools and scripts
├── docs/           # Documentation
└── .github/        # GitHub configuration
```

### Dependency Management

**Internal Dependencies:**
```json
// packages/ui/package.json
{
  "name": "@myorg/ui",
  "version": "1.0.0",
  "main": "dist/index.js",
  "dependencies": {
    "@myorg/utils": "workspace:*"
  }
}
```

**External Dependencies:**
- Use workspace protocols for local packages
- Hoist common dependencies to root
- Use dependency constraints for consistency

## Performance Optimization

### Git Performance

```bash
# Enable Git features for large repos
git config core.untrackedCache true
git config core.fsmonitor true
git config index.threads 8

# Use sparse checkout for CI
git sparse-checkout init --cone
git sparse-checkout set apps/api
```

### Build Optimization

**Caching Strategies:**
- Cache node_modules
- Cache build artifacts
- Use remote caching (Nx Cloud, Turborepo Remote Cache)

**Parallelization:**
- Run tasks in parallel
- Distribute across multiple machines
- Use incremental builds

## CI/CD for Monorepos

### GitHub Actions Example

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        project: [api, web, mobile]
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      - name: Install dependencies
        run: npm ci
      - name: Test ${{ matrix.project }}
        run: npm run test --workspace=${{ matrix.project }}
```

### Selective CI

```yaml
# Only run tests for changed projects
- name: Get changed files
  id: changed-files
  uses: tj-actions/changed-files@v35

- name: Run tests for changed projects
  if: steps.changed-files.outputs.any_changed == 'true'
  run: |
    for file in ${{ steps.changed-files.outputs.all_changed_files }}; do
      # Determine which project changed and run tests
    done
```

## Migration Strategies

### From Multi-Repo to Monorepo

1. **Planning:**
   - Identify shared code
   - Plan folder structure
   - Set up tooling

2. **Migration Steps:**
   - Create new monorepo
   - Migrate repositories one by one
   - Update CI/CD pipelines
   - Update documentation

3. **Post-Migration:**
   - Update import paths
   - Consolidate dependencies
   - Set up monorepo tooling

### Tool Migration

**From Lerna to Nx:**
```bash
# Install Nx
npm install -D nx

# Generate migration
nx migrate @nrwl/workspace

# Run migration
nx migrate --run-migrations
```

## Best Practices

### Code Organization
1. **Clear Boundaries:** Define clear ownership boundaries
2. **Shared Code:** Extract common code into shared packages
3. **Documentation:** Document project relationships and dependencies

### Development Workflow
1. **Atomic Commits:** Make changes that affect multiple projects atomic
2. **Testing:** Test changes across dependent projects
3. **Code Reviews:** Review changes that affect shared code carefully

### Tooling
1. **Automation:** Automate as much as possible
2. **Caching:** Use caching to improve performance
3. **Monitoring:** Monitor build times and resource usage

### Team Collaboration
1. **Communication:** Keep teams informed of changes
2. **Documentation:** Document processes and conventions
3. **Training:** Train team members on monorepo workflows

## Common Challenges and Solutions

### Merge Conflicts
- Use smaller, focused PRs
- Communicate changes across teams
- Use automated conflict resolution tools

### Build Times
- Implement incremental builds
- Use distributed caching
- Optimize CI/CD pipelines

### Dependency Management
- Use workspace dependencies
- Implement dependency constraints
- Regular dependency updates

### Repository Size
- Use Git LFS for large files
- Implement shallow clones for CI
- Archive old branches and tags

## Tools Comparison

| Tool | Best For | Setup Complexity | Performance | Learning Curve |
|------|----------|------------------|-------------|----------------|
| Git LFS | Large files | Low | Good | Low |
| Git Submodules | External dependencies | Medium | Fair | Medium |
| Nx | Task orchestration | High | Excellent | Medium |
| Turborepo | Build optimization | Medium | Excellent | Low |
| Lerna | Package management | Medium | Good | Medium |

## Monitoring and Maintenance

### Repository Health
- Monitor repository size
- Track build times
- Monitor dependency updates
- Regular cleanup tasks

### Team Productivity
- Measure development velocity
- Track cross-team dependencies
- Monitor code quality metrics
- Regular retrospectives

## Conclusion

Monorepos offer significant benefits for large-scale development but require careful planning and tooling. Choose the right combination of tools for your team's needs, and invest time in setting up proper processes and automation. Regular evaluation and adjustment will help maintain an efficient and productive monorepo environment.