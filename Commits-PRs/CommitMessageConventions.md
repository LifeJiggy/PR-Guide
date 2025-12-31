# Commit Message Conventions

Well-crafted commit messages are essential for maintaining a clear project history, enabling effective code reviews, and facilitating automated changelog generation. This guide covers conventional commit standards and best practices.

## 📝 **Conventional Commits Specification**

### **Format Structure**
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### **Types**
- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that do not affect the meaning of the code
- **refactor**: A code change that neither fixes a bug nor adds a feature
- **perf**: A code change that improves performance
- **test**: Adding missing tests or correcting existing tests
- **build**: Changes that affect the build system or external dependencies
- **ci**: Changes to our CI configuration files and scripts
- **chore**: Other changes that don't modify src or test files
- **revert**: Reverts a previous commit

### **Examples**
```bash
feat: add user authentication system
fix: resolve memory leak in image processing
docs: update API documentation for v2.0
style: format code with black
refactor: simplify database connection logic
perf: optimize image loading speed
test: add unit tests for user model
build: update dependencies to latest versions
ci: add GitHub Actions workflow for testing
chore: update copyright year
revert: revert "feat: add user authentication"
```

## 🎯 **Best Practices**

### **Writing Good Commit Messages**

#### **Subject Line**
- **Limit to 50 characters** (soft limit)
- **Start with lowercase** (except proper nouns)
- **No period** at the end
- **Use imperative mood** ("add" not "added", "fix" not "fixed")
- **Be specific and descriptive**

#### **Body (Optional)**
- **Separate from subject** with blank line
- **Wrap at 72 characters**
- **Explain what and why**, not how
- **Include breaking changes** if applicable

#### **Footer (Optional)**
- **Reference issues**: `Closes #123`, `Fixes #456`
- **Breaking changes**: `BREAKING CHANGE: description`
- **Co-authors**: `Co-authored-by: name <email>`

### **Good Examples**
```bash
feat: implement dark mode toggle

Add a toggle button in the header that allows users to switch between
light and dark themes. The preference is saved in localStorage and
persists across sessions.

Closes #234
```

```bash
fix: prevent crash when uploading large files

The file upload component was not handling files larger than 10MB,
causing the application to crash. Added proper validation and error
handling for oversized files.

Fixes #567
```

```bash
refactor: extract user validation logic

Move user input validation from the controller to a separate service
class. This improves testability and follows single responsibility
principle.

BREAKING CHANGE: UserController.validateUser() method removed
```

### **Bad Examples**
```bash
# Too vague
update code

# Wrong tense
added feature

# Too long
feat: this is a very long commit message that exceeds the recommended limit of 50 characters and should be shortened

# Missing type
implement user login

# Wrong format
feat:add user login (missing space)
```

## 🔧 **Tools and Automation**

### **Commitizen**
```bash
# Install
npm install -g commitizen

# Initialize
commitizen init cz-conventional-changelog --save-dev --save-exact

# Use instead of git commit
git cz
```

### **Commitlint**
```bash
# Install
npm install --save-dev @commitlint/cli @commitlint/config-conventional

# Create .commitlintrc.js
module.exports = {
  extends: ['@commitlint/config-conventional']
};
```

### **Husky for Git Hooks**
```bash
# Install
npm install --save-dev husky

# Initialize
npx husky install

# Add commit-msg hook
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit $1'
```

### **VS Code Extensions**
- **Conventional Commits**: Provides snippets and validation
- **GitLens**: Enhanced Git capabilities with conventional commit support
- **Git History**: Better visualization of commit history

## 📊 **Commit Message Analytics**

### **Analyzing Commit History**
```bash
# Count commits by type
git log --oneline | grep -E "^[a-z]+:" | sed 's/:.*//' | sort | uniq -c | sort -nr

# Find commits without conventional format
git log --oneline | grep -v -E "^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert):"

# Generate changelog
git log --pretty=format:"%s" --grep="feat\|fix" | head -20
```

### **Commit Message Quality Metrics**
- **Conventional format compliance**: % of commits following standards
- **Average message length**: Subject and body lengths
- **Issue reference rate**: % of commits referencing issues
- **Breaking change documentation**: Proper BREAKING CHANGE footers

## 🚀 **Advanced Patterns**

### **Scoped Commits**
```bash
feat(api): add user registration endpoint
fix(ui): resolve button alignment issue
refactor(db): optimize query performance
```

### **Emoji in Commits**
```bash
✨ feat: add new feature
🐛 fix: resolve bug
📚 docs: update documentation
🎨 style: improve formatting
♻️ refactor: restructure code
⚡ perf: improve performance
✅ test: add tests
📦 build: update build process
🔧 ci: update CI configuration
🧹 chore: cleanup code
```

### **Semantic Versioning Integration**
```bash
# Patch (fix)
fix: resolve typo in error message

# Minor (feat)
feat: add export functionality

# Major (breaking)
feat: redesign API with breaking changes

BREAKING CHANGE: remove deprecated endpoints
```

## 📋 **Team Guidelines**

### **Establishing Conventions**
1. **Document standards** in CONTRIBUTING.md
2. **Set up automation** with commitlint and husky
3. **Provide examples** in PR templates
4. **Review commit messages** during code review
5. **Use tools** to enforce standards

### **Migration Strategy**
```bash
# For existing projects
# 1. Document new standards
# 2. Update PR templates
# 3. Add commitlint gradually
# 4. Provide training/examples
# 5. Consider rewriting history for major projects
```

### **Common Pitfalls**
- **Inconsistent enforcement**: Some team members follow, others don't
- **Too strict rules**: Discourage contributions
- **Missing context**: Not explaining why changes were made
- **Generic messages**: "update code" instead of specific descriptions

## 🧪 **Testing Commit Messages**

### **Pre-commit Validation**
```bash
#!/bin/bash
# .git/hooks/commit-msg

commit_msg=$(cat $1)
pattern="^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)(\(.+\))?: .{1,50}$"

if ! [[ $commit_msg =~ $pattern ]]; then
  echo "Invalid commit message format"
  echo "Expected: type(scope): description (max 50 chars)"
  exit 1
fi
```

### **CI/CD Validation**
```yaml
# .github/workflows/commit-validation.yml
name: Commit Message Validation
on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - name: Validate current commit
        run: |
          commit_msg=$(git log --format=%B -n 1 HEAD)
          if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)"; then
            echo "Commit message does not follow conventional format"
            exit 1
          fi
```

## 📚 **Resources and Examples**

### **Official Specifications**
- [Conventional Commits](https://conventionalcommits.org/)
- [Angular Commit Guidelines](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit)
- [Semantic Versioning](https://semver.org/)

### **Tools**
- [Commitizen](https://commitizen.github.io/cz-cli/)
- [Commitlint](https://commitlint.js.org/)
- [Husky](https://typicode.github.io/husky/)
- [Standard Version](https://github.com/conventional-changelog/standard-version)

### **Examples from Real Projects**
- [Vue.js](https://github.com/vuejs/vue/commits/main)
- [React](https://github.com/facebook/react/commits/main)
- [Angular](https://github.com/angular/angular/commits/main)

## ✅ **Checklist**

### **For Individual Contributors**
- [ ] Use conventional commit format
- [ ] Keep subject under 50 characters
- [ ] Start with lowercase and imperative mood
- [ ] Reference issues when applicable
- [ ] Document breaking changes

### **For Project Maintainers**
- [ ] Document commit conventions in CONTRIBUTING.md
- [ ] Set up automated validation
- [ ] Provide examples and templates
- [ ] Review commit messages in PRs
- [ ] Generate changelogs automatically

### **For Teams**
- [ ] Agree on scope usage
- [ ] Decide on emoji policy
- [ ] Establish breaking change guidelines
- [ ] Train new contributors
- [ ] Monitor compliance

Well-structured commit messages make project history readable and maintainable! 📝✨