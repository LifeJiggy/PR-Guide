# Tagging and Releasing: Best Practices for Open Source

This guide covers comprehensive tagging strategies, release management, and versioning best practices for open source projects. Proper tagging and releasing ensures clear project history, reliable deployments, and smooth collaboration.

## 🏷️ **Git Tagging Fundamentals**

### **Types of Git Tags**

1. **Lightweight Tags**: Simple pointers to commits
   ```bash
   git tag v1.0.0  # Creates lightweight tag
   ```

2. **Annotated Tags**: Full objects with metadata
   ```bash
   git tag -a v1.0.0 -m "Release version 1.0.0"  # Creates annotated tag
   ```

3. **Signed Tags**: GPG-signed for security
   ```bash
   git tag -s v1.0.0 -m "Release version 1.0.0"  # Creates signed tag
   ```

### **Best Practices for Tags**
- **Always use annotated tags** (`-a`) for releases
- **Use semantic versioning** (v1.2.3)
- **Include meaningful messages**
- **Sign tags** for security-critical projects
- **Push tags separately** from commits

## 📋 **Semantic Versioning (SemVer)**

### **Version Format: MAJOR.MINOR.PATCH**

- **MAJOR**: Breaking changes (1.0.0 → 2.0.0)
- **MINOR**: New features, backward compatible (1.0.0 → 1.1.0)
- **PATCH**: Bug fixes, backward compatible (1.0.0 → 1.0.1)

### **Pre-release Identifiers**
```bash
# Alpha, Beta, Release Candidate
v1.0.0-alpha
v1.0.0-beta.1
v1.0.0-rc.1

# With build metadata
v1.0.0-alpha+build.1
v1.0.0+20130313144700
```

## 🚀 **Release Management Workflow**

### **1. Prepare for Release**
```bash
# Ensure clean working directory
git status

# Update version in code
echo "VERSION = '1.0.0'" > version.py

# Update changelog
echo "# Changelog
## [1.0.0] - 2024-01-15
### Added
- New feature X
### Fixed
- Bug Y
" > CHANGELOG.md

# Commit version bump
git add version.py CHANGELOG.md
git commit -m "chore: bump version to 1.0.0"
```

### **2. Create Release Branch (Optional)**
```bash
# For major releases, create release branch
git checkout -b release/v1.0.0

# Make final adjustments
# Run tests, update docs, etc.

# Merge back to main
git checkout main
git merge release/v1.0.0
```

### **3. Create and Push Tag**
```bash
# Create annotated tag
git tag -a v1.0.0 -m "Release v1.0.0

## What's New
- Feature A
- Feature B

## Bug Fixes
- Fix issue #123
- Fix issue #456

## Breaking Changes
- API change X

Full changelog: https://github.com/user/repo/blob/v1.0.0/CHANGELOG.md"

# Push tag to remote
git push origin v1.0.0
```

### **4. Create GitHub Release**
```bash
# Use GitHub CLI to create release
gh release create v1.0.0 \
  --title "Release v1.0.0" \
  --notes "Release notes here" \
  --target main
```

## 🛠️ **Advanced Tagging Techniques**

### **Automated Version Bumping**
```bash
# Using npm version (for JavaScript projects)
npm version patch  # 1.0.0 → 1.0.1
npm version minor  # 1.0.0 → 1.1.0
npm version major  # 1.0.0 → 2.0.0

# Using poetry version (for Python projects)
poetry version patch
poetry version minor
poetry version major

# Using cargo (for Rust projects)
cargo release patch
cargo release minor
cargo release major
```

### **Pre-release Tags**
```bash
# Create pre-release tag
git tag -a v1.0.0-beta.1 -m "Beta release v1.0.0-beta.1"

# Push pre-release
git push origin v1.0.0-beta.1
```

### **Build Tags**
```bash
# Include build metadata
BUILD_DATE=$(date +%Y%m%d%H%M%S)
git tag -a v1.0.0+build.$BUILD_DATE -m "Build $BUILD_DATE"
```

## 📦 **Release Automation**

### **GitHub Actions Release Workflow**
```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Get version
        id: get_version
        run: echo "version=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT

      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ steps.get_version.outputs.version }}
          body: |
            ## What's Changed
            - See changelog for details
          draft: false
          prerelease: ${{ contains(github.ref, 'beta') || contains(github.ref, 'alpha') }}
```

### **Automated Changelog Generation**
```yaml
# Using conventional commits
name: Changelog

on:
  push:
    tags:
      - 'v*'

jobs:
  changelog:
    runs-on: ubuntu-latest
    steps:
      - name: Generate Changelog
        uses: tj-actions/git-cliff@v1
        with:
          configuration: cliff.toml
          args: --latest --strip header
        env:
          OUTPUT: CHANGELOG.md
```

### **Semantic Release Automation**
```yaml
# .github/workflows/semantic-release.yml
name: Semantic Release

on:
  push:
    branches: [ main ]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Semantic Release
        uses: cycjimmy/semantic-release-action@v3
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          semantic_version: 18.0.0
```

## 🔄 **Managing Tag History**

### **Listing Tags**
```bash
# List all tags
git tag

# List tags with dates
git tag --format='%(creatordate:short) %(refname:short)'

# List tags matching pattern
git tag -l "v1.*"

# Show tag details
git show v1.0.0
```

### **Deleting Tags**
```bash
# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Delete both local and remote
git tag -d v1.0.0 && git push origin --delete v1.0.0
```

### **Moving Tags**
```bash
# Move tag to different commit (dangerous!)
git tag -f v1.0.0 <new-commit-hash>
git push --force-with-lease origin v1.0.0
```

## 📊 **Release Analytics**

### **Git Commands for Release Analysis**
```bash
# Commits between releases
git log --oneline v1.0.0..v1.1.0

# Contributors to release
git shortlog --no-merges v1.0.0..v1.1.0

# Files changed in release
git diff --stat v1.0.0..v1.1.0

# Release timeline
git log --oneline --since="2024-01-01" --until="2024-01-31" --grep="tag:"
```

### **GitHub Release Insights**
- **Download counts** per release
- **Release velocity** (releases per month)
- **Adoption rate** (downloads over time)
- **Issue closure** rate per release

## 🏢 **Enterprise Release Strategies**

### **Release Branches**
```bash
# Create release branch from develop
git checkout develop
git checkout -b release/v1.0.0

# Hotfixes go to release branch
git checkout release/v1.0.0
git cherry-pick <hotfix-commit>

# Merge release to main and develop
git checkout main
git merge release/v1.0.0
git tag -a v1.0.0

git checkout develop
git merge release/v1.0.0
```

### **Git Flow Model**
```
main (production) ──┐
                    ├── v1.0.0 ── v1.1.0 ── v2.0.0
develop ───────────┘
    │
    ├── feature/feature-a
    ├── feature/feature-b
    └── release/v1.1.0
```

### **Trunk-Based Development**
```bash
# Tags created directly from main
git checkout main
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

## 🔐 **Security Considerations**

### **Signed Tags**
```bash
# Configure GPG
git config --global user.signingkey <your-key-id>

# Create signed tag
git tag -s v1.0.0 -m "Signed release v1.0.0"

# Verify signature
git tag -v v1.0.0
```

### **Release Verification**
```bash
# Verify tag signature
git verify-tag v1.0.0

# Check commit signatures
git log --show-signature v1.0.0..HEAD
```

## 📋 **Release Checklist**

### **Pre-Release**
- [ ] **Version bumped** in all relevant files
- [ ] **Changelog updated** with all changes
- [ ] **Tests passing** on all supported platforms
- [ ] **Documentation updated** for new features
- [ ] **Breaking changes documented**
- [ ] **Dependencies updated** and tested
- [ ] **Security review** completed

### **Release Process**
- [ ] **Release branch created** (if using release branches)
- [ ] **Final testing** completed
- [ ] **Tag created** with proper annotation
- [ ] **Tag pushed** to remote repository
- [ ] **GitHub release created** with notes
- [ ] **CI/CD pipelines** triggered
- [ ] **Package registries** updated (PyPI, npm, etc.)

### **Post-Release**
- [ ] **Release announced** in relevant channels
- [ ] **Issues/PRs closed** with release
- [ ] **Next version** planned
- [ ] **Feedback collected** from users
- [ ] **Metrics monitored** (downloads, issues)

## 🎯 **Best Practices Summary**

### **Tagging**
- Use annotated tags for releases
- Follow semantic versioning
- Include detailed release notes
- Sign tags for security

### **Releasing**
- Automate as much as possible
- Use consistent workflows
- Test thoroughly before release
- Communicate changes clearly

### **Maintenance**
- Keep changelog up to date
- Monitor release metrics
- Plan releases regularly
- Respond to user feedback

## 📚 **Additional Resources**

- [Semantic Versioning Specification](https://semver.org/)
- [GitHub Releases Documentation](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)
- [Conventional Commits](https://conventionalcommits.org/)
- [Keep a Changelog](https://keepachangelog.com/)

## 🚀 **Quick Start Template**

```bash
#!/bin/bash
# release.sh

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: $0 <version>"
    echo "Example: $0 v1.2.3"
    exit 1
fi

# Update version file
echo "VERSION = '$VERSION'" > version.py

# Update changelog
echo "## [$VERSION] - $(date +%Y-%m-%d)" >> CHANGELOG.md
echo "### Added" >> CHANGELOG.md
echo "- " >> CHANGELOG.md
echo "### Fixed" >> CHANGELOG.md
echo "- " >> CHANGELOG.md

# Commit changes
git add version.py CHANGELOG.md
git commit -m "chore: release $VERSION"

# Create tag
git tag -a $VERSION -m "Release $VERSION"

# Push
git push origin main
git push origin $VERSION

echo "Release $VERSION created successfully!"
```

**Happy releasing! 🎉**