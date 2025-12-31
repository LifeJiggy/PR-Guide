# Commit Convention Guidelines

Standardized commit message format for maintaining clean and meaningful commit history.

## Format Structure

```
<type>(<scope>): <subject>

<body>

<footer>
```

## Commit Types

- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that do not affect the meaning of the code
- **refactor**: A code change that neither fixes a bug nor adds a feature
- **perf**: A code change that improves performance
- **test**: Adding missing tests or correcting existing tests
- **build**: Changes that affect the build system or external dependencies
- **ci**: Changes to CI configuration files and scripts
- **chore**: Other changes that don't modify src or test files
- **revert**: Reverts a previous commit

## Examples

### Feature Commit
```
feat(auth): add user authentication with JWT tokens

Implement JWT-based authentication system including:
- User login and registration endpoints
- Token validation middleware
- Refresh token mechanism
- Password hashing with bcrypt

Closes #123
```

### Bug Fix Commit
```
fix(api): resolve memory leak in user service

- Fixed unclosed database connections
- Added proper resource cleanup
- Implemented connection pooling

Fixes #456
```

### Documentation Commit
```
docs(readme): update installation instructions

- Added Windows installation steps
- Included troubleshooting section
- Updated system requirements

No issue
```

### Refactor Commit
```
refactor(database): optimize user query performance

- Replaced N+1 queries with eager loading
- Added database indexes for frequently queried fields
- Simplified query building logic

Related to #789
```

## Best Practices

1. **Use imperative mood**: "Add feature" not "Added feature"
2. **Limit subject line to 50 characters**
3. **Capitalize the subject line**
4. **Do not end the subject line with a period**
5. **Use the body to explain what and why vs. how**
6. **Use the footer to reference issues or breaking changes**
7. **Keep commits atomic**: One commit should represent one logical change