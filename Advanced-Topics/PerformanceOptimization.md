# Git Performance Optimization for Large Repositories

This guide covers Git tips for large repositories including sparse checkout, shallow clones, and performance optimization techniques.

## Understanding Git Performance Issues

Large repositories face several performance challenges:
- Slow clone operations
- Large `.git` directory sizes
- Slow status checks and operations
- Memory-intensive operations
- Network bandwidth consumption

## Repository Analysis

### Checking Repository Size

```bash
# Check repository size
du -sh .git

# Check largest files
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '/^blob/ {print substr($0,6)}' | sort -k2nr | head -10

# Check largest directories
find .git/objects -type f -exec ls -lh {} \; | awk '{print $5 " " $9}' | sort -hr | head -10
```

### Performance Benchmarking

```bash
# Time git operations
time git status
time git log --oneline -1000
time git diff HEAD~1
```

## Optimization Techniques

### Git Configuration for Large Repos

```bash
# Enable performance features
git config core.untrackedCache true
git config core.fsmonitor true
git config index.threads 8
git config pack.threads 8

# Optimize pack files
git config pack.deltaCacheSize 2g
git config pack.packSizeLimit 2g
git config pack.windowMemory 2g

# Improve compression
git config core.compression 9
git config core.loosecompression 9
```

### Garbage Collection Optimization

```bash
# Aggressive garbage collection
git gc --aggressive --prune=now

# Repack repository
git repack -a -d --depth=250 --window=250

# Clean up reflog
git reflog expire --expire=30.days.ago --all
git gc --prune=30.days.ago
```

## Sparse Checkout

### Basic Sparse Checkout

```bash
# Initialize sparse checkout
git sparse-checkout init

# Set cone mode for better performance
git sparse-checkout set --cone

# Include specific directories
git sparse-checkout set apps/api packages/shared

# Exclude directories
git sparse-checkout add '!docs'
```

### Advanced Sparse Checkout

```bash
# Use patterns
git sparse-checkout set 'apps/*' 'packages/*' '!apps/mobile'

# Check what's included
git sparse-checkout list

# Disable sparse checkout
git sparse-checkout disable
```

### CI/CD with Sparse Checkout

```yaml
# GitHub Actions example
- uses: actions/checkout@v4
  with:
    sparse-checkout: |
      apps/api
      packages/shared
    sparse-checkout-cone-mode: true
```

## Shallow Cloning

### Basic Shallow Clone

```bash
# Clone with limited history
git clone --depth 1 https://github.com/user/repo.git

# Convert to full clone later
git fetch --unshallow
```

### Shallow Clone with Specific Branch

```bash
# Clone specific branch with limited history
git clone --depth 1 --branch develop https://github.com/user/repo.git

# Fetch more history if needed
git fetch --depth 10 origin develop
```

### Partial Clone

```bash
# Clone without blobs (tree-only)
git clone --filter=tree:0 https://github.com/user/repo.git

# Clone with blob size limit
git clone --filter=blob:limit=1m https://github.com/user/repo.git
```

## Git LFS Optimization

### LFS Configuration

```bash
# Check LFS status
git lfs status

# Optimize LFS
git lfs prune
git lfs dedup

# Migrate large files to LFS
git lfs migrate import --include="*.zip,*.tar.gz" --everything
```

### LFS Performance Tips

```bash
# Use LFS batch API
git config lfs.batch true

# Increase concurrent transfers
git config lfs.concurrenttransfers 10

# Set transfer timeouts
git config lfs.transfer.maxretries 10
```

## Advanced Git Features

### Worktrees for Large Repos

```bash
# Create worktree for feature development
git worktree add ../feature-branch feature/new-feature

# List worktrees
git worktree list

# Remove worktree
git worktree remove ../feature-branch
```

### Git Submodules Optimization

```bash
# Shallow clone submodules
git submodule update --init --depth 1

# Update submodules in parallel
git submodule foreach --recursive 'git fetch --depth 1'
```

## CI/CD Optimization

### GitHub Actions Optimization

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 1
          sparse-checkout: |
            apps/api
            packages/shared

      - name: Setup cache
        uses: actions/cache@v3
        with:
          path: |
            ~/.npm
            .git/lfs
          key: ${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}

      - name: Install dependencies
        run: npm ci --prefer-offline

      - name: Run tests
        run: npm run test:ci
```

### Build Caching Strategies

```yaml
# Cache node_modules
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

# Cache Git LFS
- uses: actions/cache@v3
  with:
    path: .git/lfs
    key: ${{ runner.os }}-lfs-${{ hashFiles('.gitattributes') }}
```

## Repository Maintenance

### Regular Maintenance Tasks

```bash
# Weekly maintenance script
#!/bin/bash

# Garbage collection
git gc --aggressive --prune=now

# Repack
git repack -a -d --depth=250 --window=250

# Clean reflog
git reflog expire --expire=30.days.ago --all

# LFS maintenance
git lfs prune
git lfs dedup

# Update submodules
git submodule update --remote --recursive
```

### Automated Maintenance

```yaml
# GitHub Actions for maintenance
name: Repository Maintenance
on:
  schedule:
    - cron: '0 2 * * 0'  # Weekly on Sunday
  workflow_dispatch:

jobs:
  maintenance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Run maintenance
        run: |
          git gc --aggressive --prune=now
          git repack -a -d
          git lfs prune

      - name: Push maintenance
        run: git push
```

## Monitoring and Metrics

### Performance Monitoring

```bash
# Monitor git operations
git config alias.time '!time git'

# Check operation times
git time status
git time log --oneline -100

# Repository statistics
git count-objects -v
git rev-list --count HEAD
```

### Size Monitoring

```bash
# Repository size over time
#!/bin/bash
echo "$(date): $(du -sh .git | cut -f1)" >> repo-size.log

# Plot size history
gnuplot -e "
  set xdata time;
  set timefmt '%Y-%m-%d';
  set format x '%m/%d';
  plot 'repo-size.log' using 1:2 with lines title 'Repo Size'
"
```

## Troubleshooting Performance Issues

### Common Issues and Solutions

**Slow Status:**
```bash
# Enable untracked cache
git config core.untrackedCache true

# Use fsmonitor
git config core.fsmonitor true
```

**Large Pack Files:**
```bash
# Repack with better compression
git repack -a -d -f --depth=250 --window=250

# Clean up old packs
git gc --prune=now
```

**Slow Clone:**
```bash
# Use partial clone
git clone --filter=blob:limit=1m https://github.com/user/repo.git

# Use shallow clone
git clone --depth 1 https://github.com/user/repo.git
```

**Memory Issues:**
```bash
# Increase Git memory limits
git config pack.windowMemory 2g
git config pack.deltaCacheSize 2g
git config core.packedGitLimit 2g
```

## Best Practices

### Development Workflow
1. **Use shallow clones for development**
2. **Implement sparse checkout for large teams**
3. **Regular repository maintenance**
4. **Monitor performance metrics**
5. **Use worktrees for multiple features**

### CI/CD Optimization
1. **Cache dependencies and artifacts**
2. **Use shallow clones in CI**
3. **Parallelize jobs when possible**
4. **Monitor build times and resource usage**
5. **Implement incremental builds**

### Team Collaboration
1. **Document performance guidelines**
2. **Train team on optimization techniques**
3. **Regular performance reviews**
4. **Automate maintenance tasks**
5. **Monitor repository health**

## Tool Ecosystem

### Git Performance Tools

- **git-sizer**: Analyze repository size and structure
- **git-filter-repo**: Rewrite repository history for size reduction
- **BFG Repo-Cleaner**: Remove large files from history
- **git-lfs-migrate**: Migrate files to LFS

### Monitoring Tools

- **GitStats**: Generate repository statistics
- **git-quick-stats**: Quick repository statistics
- **tig**: Text-mode interface for Git
- **gitui**: Terminal UI for Git

## Migration Strategies

### Converting to Optimized Setup

1. **Analysis Phase:**
   - Assess current repository size and performance
   - Identify large files and directories
   - Analyze usage patterns

2. **Implementation Phase:**
   - Implement Git LFS for large files
   - Set up sparse checkout configurations
   - Configure performance optimizations
   - Update CI/CD pipelines

3. **Migration Phase:**
   - Migrate to new configurations gradually
   - Update team workflows and documentation
   - Monitor performance improvements

4. **Maintenance Phase:**
   - Establish regular maintenance routines
   - Monitor performance metrics
   - Update configurations as needed

## Conclusion

Optimizing Git performance for large repositories requires a combination of configuration changes, workflow adjustments, and regular maintenance. Start with basic optimizations like shallow clones and sparse checkout, then implement more advanced techniques as needed. Regular monitoring and maintenance will ensure your repository remains performant as it grows.