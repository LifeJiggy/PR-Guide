# Contributing Guidelines: Professional Open Source Collaboration

This comprehensive guide outlines the standards, processes, and best practices for contributing to this project. Following these guidelines ensures smooth collaboration and maintains high-quality contributions.

---

## Table of Contents
1. [Getting Started](#getting-started)
2. [Development Workflow](#development-workflow)
3. [Code Standards](#code-standards)
4. [Testing Requirements](#testing-requirements)
5. [Documentation Standards](#documentation-standards)
6. [Pull Request Process](#pull-request-process)
7. [Code Review Guidelines](#code-review-guidelines)
8. [Issue Reporting](#issue-reporting)
9. [Community Guidelines](#community-guidelines)
10. [License & Attribution](#license--attribution)

---

## 1. Getting Started

### **Prerequisites**
Before contributing, ensure you have:
- [ ] Git (version 2.25+)
- [ ] Python 3.8+ (for Python projects)
- [ ] Node.js 16+ (for JavaScript projects)
- [ ] Familiarity with GitHub workflow
- [ ] Understanding of the project's technology stack

### **Environment Setup**
```bash
# 1. Fork the repository on GitHub
# 2. Clone your fork locally
git clone https://github.com/YOUR_USERNAME/project-name.git
cd project-name

# 3. Set up upstream remote
git remote add upstream https://github.com/ORIGINAL_OWNER/project-name.git

# 4. Create a virtual environment (Python)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 5. Install dependencies
pip install -r requirements.txt
npm install  # For Node.js projects

# 6. Set up pre-commit hooks
pip install pre-commit
pre-commit install
```

### **Project Structure**
```
project-name/
├── docs/                    # Documentation
├── src/                     # Source code
├── tests/                   # Test files
├── examples/                # Usage examples
├── .github/                 # GitHub configuration
│   ├── workflows/          # CI/CD pipelines
│   └── ISSUE_TEMPLATE/     # Issue templates
├── CONTRIBUTING.md         # This file
├── README.md               # Project overview
└── pyproject.toml          # Python project configuration
```

---

## 2. Development Workflow

### **Branch Naming Convention**
Use descriptive, lowercase branch names with hyphens:
```bash
# Feature branches
git checkout -b feature/add-user-authentication
git checkout -b feature/improve-error-handling

# Bug fix branches
git checkout -b fix/crash-on-login
git checkout -b fix/memory-leak-in-parser

# Documentation branches
git checkout -b docs/update-api-reference
git checkout -b docs/add-contribution-guide

# Hotfix branches (for production issues)
git checkout -b hotfix/security-patch
```

### **Commit Message Format**
Follow conventional commit format:
```bash
# Format: type(scope): description

# Examples:
feat(auth): add JWT token validation
fix(api): resolve crash on invalid input
docs(readme): update installation instructions
test(utils): add unit tests for helper functions
refactor(core): simplify algorithm complexity
style(lint): fix code formatting issues
chore(deps): update dependencies to latest versions

# Breaking changes
feat!: change API response format (breaking change)

# With scope and body
feat(auth): implement OAuth2 login

- Add OAuth2 client configuration
- Support multiple providers (Google, GitHub)
- Include refresh token handling

Closes #123
```

### **Development Process**
1. **Choose an issue** or create one for your contribution
2. **Create a feature branch** from `main` or `develop`
3. **Make changes** following code standards
4. **Write tests** for new functionality
5. **Update documentation** as needed
6. **Run tests and linting** locally
7. **Commit changes** with clear messages
8. **Push branch** and create pull request
9. **Address review feedback**
10. **Merge** once approved

---

## 3. Code Standards

### **General Principles**
- **DRY (Don't Repeat Yourself)**: Avoid code duplication
- **KISS (Keep It Simple, Stupid)**: Prefer simple solutions
- **SOLID Principles**: Follow object-oriented design principles
- **YAGNI (You Aren't Gonna Need It)**: Don't add unnecessary features
- **Clean Code**: Write readable, maintainable code

### **Language-Specific Standards**

#### **Python**
```python
# Use type hints
def calculate_total(items: List[Item]) -> float:
    pass

# Follow PEP 8
class UserManager:
    """Manages user operations."""

    def __init__(self, db_connection: Database) -> None:
        self.db = db_connection

    def create_user(self, user_data: dict) -> User:
        """Create a new user."""
        # Implementation
        pass
```

#### **JavaScript/TypeScript**
```typescript
// Use TypeScript for type safety
interface User {
  id: number;
  name: string;
  email: string;
}

class UserService {
  constructor(private db: Database) {}

  async createUser(userData: Partial<User>): Promise<User> {
    // Implementation
  }
}
```

#### **Go**
```go
// Follow Go conventions
package main

import (
    "fmt"
    "log"
)

// Use proper error handling
func processData(data []byte) error {
    if len(data) == 0 {
        return fmt.Errorf("data cannot be empty")
    }
    // Process data
    return nil
}
```

### **Code Formatting**
- **Python**: Use Black formatter with 88 character line length
- **JavaScript**: Use Prettier with project configuration
- **Go**: Use `gofmt` and `goimports`
- **All languages**: Configure your editor to format on save

### **Linting Rules**
- **Python**: pylint, flake8, mypy for type checking
- **JavaScript**: ESLint with Airbnb config
- **Go**: golint, go vet
- **All**: Fix all linting errors before committing

---

## 4. Testing Requirements

### **Testing Pyramid**
```
End-to-End Tests (E2E)     ┌─────────┐
Integration Tests         │   Few   │
Unit Tests               ┌┴─────────┴┐
                        │   Many    │
                        └───────────┘
```

### **Test Coverage Requirements**
- **Minimum coverage**: 80% overall
- **Critical paths**: 90%+ coverage
- **New features**: 100% coverage for new code
- **Legacy code**: Improve coverage with changes

### **Test Structure**
```python
# tests/test_feature.py
import pytest
from src.feature import FeatureClass

class TestFeatureClass:
    def setup_method(self):
        """Set up test fixtures."""
        self.feature = FeatureClass()

    def test_basic_functionality(self):
        """Test basic feature operation."""
        result = self.feature.process("input")
        assert result == "expected_output"

    def test_error_conditions(self):
        """Test error handling."""
        with pytest.raises(ValueError):
            self.feature.process(None)

    def test_edge_cases(self):
        """Test edge cases."""
        # Test empty input, large input, special characters, etc.
        pass
```

### **Test Naming Conventions**
- `test_[function_name]_[scenario]`
- `test_should_[expected_behavior]_when_[condition]`
- Use descriptive names that explain what is being tested

### **Running Tests**
```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=src --cov-report=html

# Run specific test file
pytest tests/test_feature.py

# Run tests matching pattern
pytest -k "authentication"

# Run tests in verbose mode
pytest -v
```

---

## 5. Documentation Standards

### **README Structure**
Every project should have a comprehensive README:
```markdown
# Project Name

Brief description of what the project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

```bash
pip install project-name
```

## Quick Start

```python
import project_name

# Example usage
```

## Documentation

- [API Reference](docs/api.md)
- [Contributing Guide](CONTRIBUTING.md)
- [Changelog](CHANGELOG.md)

## License

MIT License - see [LICENSE](LICENSE) file.
```

### **Code Documentation**
- **Public APIs**: Document all public functions/classes
- **Complex logic**: Explain non-obvious algorithms
- **Parameters and return values**: Document types and purposes
- **Examples**: Include usage examples in docstrings

```python
def authenticate_user(username: str, password: str) -> Optional[User]:
    """
    Authenticate a user with username and password.

    Args:
        username: User's username (case-insensitive)
        password: User's password (plain text)

    Returns:
        User object if authentication successful, None otherwise

    Raises:
        ConnectionError: If database connection fails

    Example:
        >>> user = authenticate_user("john_doe", "secret123")
        >>> if user:
        ...     print(f"Welcome {user.name}!")
    """
```

### **API Documentation**
- Use OpenAPI/Swagger for REST APIs
- Include request/response examples
- Document authentication requirements
- Specify rate limits and error codes

---

## 6. Pull Request Process

### **PR Checklist**
Before submitting a PR, ensure:
- [ ] **Tests pass**: All tests pass locally
- [ ] **Linting clean**: No linting errors
- [ ] **Documentation updated**: README, API docs, etc.
- [ ] **Commits squashed**: Related commits combined
- [ ] **Branch up-to-date**: Rebased on latest main
- [ ] **No merge conflicts**: Clean merge possible

### **PR Template**
Use this template for all pull requests:
```markdown
## Description
Brief description of the changes.

## Type of Change
- [ ] Bug fix (non-breaking change)
- [ ] New feature (non-breaking change)
- [ ] Breaking change
- [ ] Documentation update
- [ ] Refactoring
- [ ] Performance improvement

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed
- [ ] All tests pass

## Screenshots (if applicable)
Add screenshots for UI changes.

## Additional Notes
Any additional information or context.
```

### **PR Size Guidelines**
- **Small PR**: < 50 lines - Quick review
- **Medium PR**: 50-200 lines - Standard review
- **Large PR**: 200-500 lines - Extended review
- **Extra Large PR**: > 500 lines - Consider splitting

### **PR Review Process**
1. **Automated checks**: CI/CD pipelines run
2. **Self-review**: Author reviews their own code
3. **Peer review**: 1-2 reviewers assigned
4. **Discussion**: Address review comments
5. **Approval**: Maintainers approve when ready
6. **Merge**: Squash merge with conventional commit message

---

## 7. Code Review Guidelines

### **For Reviewers**
- **Be constructive**: Focus on code improvement
- **Explain reasoning**: Why suggestions are made
- **Prioritize issues**: Critical issues first
- **Balance perfection**: Don't block on minor issues
- **Guide juniors**: Help less experienced contributors

### **For Contributors**
- **Address feedback**: Respond to all review comments
- **Explain decisions**: Why certain approaches chosen
- **Ask questions**: Seek clarification when needed
- **Be open to change**: Consider alternative approaches
- **Learn continuously**: Improve from each review

### **Review Checklist**
See [CodeReviewChecklist.md](CodeReviewChecklist.md) for detailed review criteria.

---

## 8. Issue Reporting

### **Bug Reports**
Use this template for bug reports:
```markdown
## Bug Description
Clear, concise description of the bug.

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. See error

## Expected Behavior
What should happen.

## Actual Behavior
What actually happens.

## Environment
- OS: [e.g., Windows 10]
- Browser: [e.g., Chrome 91]
- Version: [e.g., 1.0.0]

## Additional Context
Screenshots, logs, or other relevant information.
```

### **Feature Requests**
Use this template for feature requests:
```markdown
## Feature Summary
Brief description of the feature.

## Problem Statement
What problem does this solve?

## Proposed Solution
How should it work?

## Alternative Solutions
Other approaches considered.

## Additional Context
Mockups, examples, or related issues.
```

### **Issue Labels**
- `bug`: Something isn't working
- `enhancement`: New feature or improvement
- `documentation`: Documentation needed
- `good first issue`: Beginner-friendly
- `help wanted`: Community contribution welcome
- `question`: Further information needed

---

## 9. Community Guidelines

### **Code of Conduct**
- **Be respectful**: Treat everyone with respect
- **Be inclusive**: Welcome people from all backgrounds
- **Be collaborative**: Help others learn and grow
- **Be patient**: Allow time for responses
- **Be constructive**: Focus on solutions, not blame

### **Communication Guidelines**
- **Use clear language**: Avoid jargon when possible
- **Provide context**: Include relevant background
- **Be concise**: Respect others' time
- **Stay on topic**: Keep discussions relevant
- **Use appropriate channels**: Issues for bugs, discussions for ideas

### **Recognition**
- **Credit contributors**: Acknowledge all contributions
- **Share knowledge**: Help others understand
- **Celebrate success**: Recognize achievements
- **Mentor newcomers**: Help people get started
- **Give feedback**: Provide constructive input

---

## 10. License & Attribution

### **License Compliance**
- **Respect licenses**: Follow all license requirements
- **Attribute properly**: Give credit where due
- **Don't copy**: Create original work
- **Check dependencies**: Ensure dependency licenses are compatible

### **Attribution Requirements**
```markdown
# If using code from other projects
# Include attribution in comments
"""
This code is based on [Project Name] by [Author]
Licensed under [License Name]
Source: [URL]
"""
```

### **Contribution Licensing**
By contributing to this project, you agree that:
- Your contributions will be licensed under the same license as the project
- You have the right to license your contributions
- You grant maintainers permission to modify and distribute your work

---

## Quick Reference

### **Commands**
```bash
# Development setup
git clone <fork-url>
cd project-name
pip install -r requirements-dev.txt
pre-commit install

# Testing
pytest --cov=src
black .  # Format code
flake8 .  # Lint code

# Creating PR
git checkout -b feature/my-feature
# Make changes, write tests
git commit -m "feat: add my feature"
git push origin feature/my-feature
# Create PR on GitHub
```

### **Key Files**
- `CONTRIBUTING.md`: This guide
- `README.md`: Project overview
- `pyproject.toml`: Python configuration
- `.pre-commit-config.yaml`: Pre-commit hooks
- `.github/workflows/`: CI/CD pipelines

### **Getting Help**
- **Issues**: For bugs and feature requests
- **Discussions**: For questions and ideas
- **Documentation**: Check docs/ directory
- **Community**: Join our Discord/Slack

---

Thank you for contributing! Your efforts help make this project better for everyone. 🚀

*For questions about these guidelines, please open an issue or start a discussion.*