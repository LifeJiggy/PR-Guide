# GitHub Discussions and Funding

This guide covers enabling GitHub Discussions for community engagement and setting up FUNDING.yml for sponsorships.

## GitHub Discussions

### Enabling Discussions

1. Go to your repository Settings
2. Scroll down to "Features" section
3. Check "Discussions"
4. Click "Save changes"

### Discussion Categories

Create categories to organize discussions:

**General:**
- Announcements - Project updates and news
- General - Open discussions about the project
- Ideas - Feature requests and suggestions
- Polls - Community polls and surveys

**Q&A:**
- Q&A - Questions and answers
- Help - Getting help with usage
- Show and tell - Share your projects

**Support:**
- Bug Reports - Report bugs (link to Issues)
- Feature Requests - Request features (link to Issues)

### Best Practices

**For Maintainers:**
- Welcome new contributors in announcements
- Pin important discussions
- Convert discussions to issues when appropriate
- Moderate spam and off-topic content

**For Contributors:**
- Search existing discussions before creating new ones
- Use appropriate categories
- Be respectful and constructive
- Follow community guidelines

### Integration with Issues

```markdown
<!-- In discussion -->
This has been converted to an issue: #123
```

```markdown
<!-- In issue -->
Related discussion: #discussion/123
```

## FUNDING.yml

### Setting Up Sponsorships

Create `.github/FUNDING.yml` in your repository:

```yaml
# These are supported funding model platforms

github: [your-username] # GitHub Sponsors
patreon: your-username # Patreon
open_collective: your-project # Open Collective
ko_fi: your-username # Ko-fi
tidelift: npm/your-package # Tidelift
community_bridge: your-project # Community Bridge
liberapay: your-username # Liberapay
issuehunt: your-username # IssueHunt
otechie: your-username # Otechie
custom: ['https://www.paypal.me/your-username'] # Custom URL
```

### Choosing Platforms

**GitHub Sponsors:**
- Integrated with GitHub
- Supports one-time and recurring payments
- Automatic tax forms for US residents

**Patreon:**
- Good for creators and ongoing support
- Tiered sponsorship levels
- Community features

**Open Collective:**
- Good for open source projects
- Transparent fund management
- Fiscal hosting options

**Ko-fi:**
- Simple one-time donations
- No platform fees for donations under $3
- Good for occasional supporters

### Sponsorship Tiers

Define meaningful tiers:

**Bronze ($5/month):**
- Name in README
- Access to private Discord channel

**Silver ($10/month):**
- Bronze benefits
- Early access to features
- Priority support

**Gold ($25/month):**
- Silver benefits
- Private repository access
- Direct communication with maintainers

### Communicating Value

**README Badge:**
```markdown
[![Sponsor](https://img.shields.io/badge/Sponsor-%E2%9D%A4-pink.svg)](https://github.com/sponsors/your-username)
```

**Sponsorship Section:**
```markdown
## Sponsors

Thank you to our amazing sponsors! ❤️

<!-- sponsors --><!-- sponsors -->
```

**Sponsorship Call-to-Action:**
```markdown
## Support This Project

If you find this project helpful, please consider supporting it:

- ⭐ Star the repository
- 🐛 Report bugs and request features
- 📖 Improve documentation
- 💰 [Become a sponsor](https://github.com/sponsors/your-username)
```

## Advanced Features

### GitHub Sponsors Integration

**Sponsor Button:**
- Automatically appears on repository page
- Links to your GitHub Sponsors page

**Sponsor Benefits:**
- Custom sponsor benefits
- Sponsor-only content
- Direct communication

### Discussion Templates

Create discussion templates for consistency:

```yaml
# .github/DISCUSSION_TEMPLATE/feature-request.yml
title: "Feature Request: "
labels: ["enhancement"]
body:
  - type: textarea
    attributes:
      label: Problem
      description: What's the problem this feature would solve?
    validations:
      required: true
  - type: textarea
    attributes:
      label: Solution
      description: Describe the solution you'd like
    validations:
      required: true
  - type: textarea
    attributes:
      label: Alternatives
      description: Describe alternatives you've considered
```

### Analytics and Insights

**Discussion Insights:**
- Track engagement metrics
- Identify popular topics
- Monitor community growth

**Sponsorship Analytics:**
- Track sponsorship revenue
- Monitor sponsor retention
- Analyze sponsorship sources

## Community Building

### Engagement Strategies

1. **Regular Updates:** Post weekly/monthly updates
2. **AMA Sessions:** Host "Ask Me Anything" discussions
3. **Contribute Guides:** Help new contributors get started
4. **Showcase Work:** Feature community projects
5. **Polls and Surveys:** Gather community feedback

### Moderation Guidelines

**Community Guidelines:**
```markdown
# Community Guidelines

## Our Standards
- Be respectful and inclusive
- Focus on constructive feedback
- Help others learn and grow
- Report issues appropriately

## Unacceptable Behavior
- Harassment or discrimination
- Spam or off-topic content
- Trolling or disruptive behavior
- Sharing private information
```

### Recognition Programs

**Contributor Recognition:**
- Monthly contributor spotlights
- Hall of fame for top contributors
- Special badges for achievements

**Sponsor Recognition:**
- Sponsor walls of fame
- Special mentions in releases
- Exclusive sponsor updates

## Best Practices Summary

### For Discussions
1. **Enable Early:** Set up discussions when your project grows
2. **Create Categories:** Organize discussions logically
3. **Moderate Actively:** Keep discussions on-topic and respectful
4. **Convert to Issues:** Move actionable items to issues
5. **Engage Regularly:** Participate in discussions actively

### For Funding
1. **Be Transparent:** Clearly communicate how funds are used
2. **Offer Value:** Provide meaningful benefits for sponsors
3. **Start Small:** Begin with one or two platforms
4. **Express Gratitude:** Thank sponsors publicly and personally
5. **Track Metrics:** Monitor sponsorship growth and engagement

### Integration Tips
- Link discussions in your README
- Mention sponsorship opportunities in releases
- Cross-promote between discussions and issues
- Use automation to acknowledge new sponsors

## Tools and Resources

### GitHub CLI for Discussions

```bash
# List discussions
gh discussion list

# Create discussion
gh discussion create --title "New Feature Ideas" --body "Share your feature requests" --category "Ideas"

# View discussion
gh discussion view 123
```

### Third-Party Tools

- **Discourse:** Advanced discussion platform
- **Discord:** Real-time community chat
- **Slack:** Team communication integration
- **Open Collective:** Fund management and transparency

### Templates and Examples

**Discussion Welcome Message:**
```markdown
Welcome to our discussions! 🎉

This is the place to:
- Ask questions about the project
- Share ideas and suggestions
- Discuss features and improvements
- Connect with other community members

Please be respectful and follow our community guidelines.
```

**Sponsorship Update Template:**
```markdown
## Monthly Sponsor Update

Thank you to all our sponsors! Here's what your support helped us achieve this month:

### 🚀 New Features
- Added dark mode support
- Improved performance by 40%

### 🐛 Bug Fixes
- Fixed login issues on mobile
- Resolved memory leaks

### 📈 Growth
- 500+ new stars
- 1000+ downloads

Your support keeps this project going. Thank you! ❤️