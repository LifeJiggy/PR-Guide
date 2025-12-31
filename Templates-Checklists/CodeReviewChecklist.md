# Code Review Checklist

Use this comprehensive checklist to ensure thorough and effective code reviews.

## Pre-Review Preparation
- [ ] Code builds without errors or warnings
- [ ] All tests are passing locally
- [ ] Code follows project style guidelines
- [ ] No debug code or console.log statements
- [ ] Code is properly formatted and indented

## Functionality and Logic
- [ ] Code implements the required functionality correctly
- [ ] Logic is clear and easy to understand
- [ ] Edge cases are properly handled
- [ ] Error handling is comprehensive and appropriate
- [ ] No hardcoded values that should be configurable

## Code Quality
- [ ] Code follows SOLID principles
- [ ] Functions/methods are appropriately sized and focused
- [ ] Variable and function names are descriptive and meaningful
- [ ] Code duplication is minimized
- [ ] Complex logic is well-commented

## Performance
- [ ] No obvious performance bottlenecks
- [ ] Appropriate data structures and algorithms are used
- [ ] Database queries are optimized
- [ ] Memory usage is reasonable
- [ ] Asynchronous operations are used where appropriate

## Security
- [ ] Input validation is implemented
- [ ] No security vulnerabilities introduced
- [ ] Sensitive data is properly handled
- [ ] Authentication and authorization are checked
- [ ] SQL injection and XSS vulnerabilities are prevented

## Testing
- [ ] Adequate test coverage for new code
- [ ] Tests are meaningful and test actual functionality
- [ ] Edge cases are covered in tests
- [ ] Integration tests are included where necessary
- [ ] Test code follows the same quality standards

## Documentation
- [ ] Code comments explain complex logic
- [ ] Public APIs are documented
- [ ] README or relevant documentation is updated
- [ ] Code changes are reflected in documentation
- [ ] Breaking changes are clearly documented

## Integration and Dependencies
- [ ] New dependencies are justified and reviewed
- [ ] No unnecessary dependencies added
- [ ] Package versions are appropriate and secure
- [ ] Integration with existing code is seamless
- [ ] API changes are backward compatible where possible

## Maintainability
- [ ] Code is easy to maintain and extend
- [ ] Configuration is externalized where appropriate
- [ ] Logging and monitoring are implemented
- [ ] Code follows established patterns and conventions
- [ ] Future developers will be able to understand the code

## Final Checks
- [ ] Code follows the team's coding standards
- [ ] No TODO comments or dead code
- [ ] Environment-specific code is properly configured
- [ ] Internationalization considerations are addressed (if applicable)
- [ ] Accessibility requirements are met (if applicable)

## Reviewer Actions
- [ ] Provide constructive and specific feedback
- [ ] Ask questions to understand intent when unclear
- [ ] Suggest improvements rather than just pointing out problems
- [ ] Acknowledge good practices and well-written code
- [ ] Follow up on previous review comments if applicable