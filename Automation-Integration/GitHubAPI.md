# GitHub API: Advanced Integration and Automation

This comprehensive guide covers GitHub's REST API and GraphQL API for advanced repository management, automation, and integration workflows.

## 🎯 **API Overview**

### **Available APIs**
- **REST API**: Traditional HTTP-based API for most operations
- **GraphQL API**: Modern, efficient API for complex queries
- **Webhooks**: Real-time event notifications
- **GitHub Apps**: OAuth apps with fine-grained permissions

### **Authentication Methods**
```bash
# Personal Access Token (PAT)
curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/user

# GitHub App Installation Token
curl -H "Authorization: token YOUR_INSTALLATION_TOKEN" https://api.github.com/user

# OAuth App Token
curl -H "Authorization: Bearer YOUR_OAUTH_TOKEN" https://api.github.com/user
```

## 🔧 **REST API Fundamentals**

### **Rate Limits**
- **Authenticated requests**: 5,000 per hour
- **Unauthenticated requests**: 60 per hour
- **GraphQL**: 5,000 nodes per hour

```bash
# Check rate limit status
curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/rate_limit
```

### **Common Headers**
```bash
# Accept different API versions
Accept: application/vnd.github.v3+json
Accept: application/vnd.github.machine-man-preview+json

# Specify API version
X-GitHub-Api-Version: 2022-11-28
```

## 📊 **Repository Management**

### **Get Repository Information**
```bash
# Get repository details
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO

# Get repository contents
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/contents

# Get specific file content
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/contents/path/to/file
```

### **Create and Update Files**
```bash
# Create or update a file
curl -X PUT -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/contents/path/to/file \
  -d '{
    "message": "Add new file",
    "content": "'$(echo "Hello World" | base64)'",
    "branch": "main"
  }'

# Delete a file
curl -X DELETE -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/contents/path/to/file \
  -d '{
    "message": "Delete file",
    "sha": "FILE_SHA",
    "branch": "main"
  }'
```

## 🔄 **Pull Request Operations**

### **List and Get PRs**
```bash
# List pull requests
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/pulls

# Get specific PR
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER

# Get PR files
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER/files

# Get PR reviews
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER/reviews
```

### **Create and Update PRs**
```bash
# Create a pull request
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/pulls \
  -d '{
    "title": "Amazing new feature",
    "body": "Please pull these awesome changes in!",
    "head": "octocat:new-feature",
    "base": "main"
  }'

# Update PR
curl -X PATCH -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER \
  -d '{
    "title": "Updated title",
    "body": "Updated description"
  }'

# Merge PR
curl -X PUT -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER/merge \
  -d '{
    "commit_title": "Merge pull request #NUMBER",
    "merge_method": "merge"
  }'
```

### **PR Reviews**
```bash
# Submit a review
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER/reviews \
  -d '{
    "body": "Great work!",
    "event": "APPROVE",
    "comments": [
      {
        "path": "file.txt",
        "position": 1,
        "body": "Looks good!"
      }
    ]
  }'

# Dismiss a review
curl -X PUT -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/pulls/NUMBER/reviews/REVIEW_ID/dismissals \
  -d '{
    "message": "Review dismissed"
  }'
```

## 🏷️ **Issues and Labels**

### **Issue Management**
```bash
# List issues
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/issues

# Create an issue
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/issues \
  -d '{
    "title": "Found a bug",
    "body": "I'm having a problem with this.",
    "labels": ["bug"]
  }'

# Update an issue
curl -X PATCH -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/issues/NUMBER \
  -d '{
    "state": "closed"
  }'
```

### **Label Operations**
```bash
# List labels
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/labels

# Create a label
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/labels \
  -d '{
    "name": "enhancement",
    "color": "84cc16",
    "description": "New feature or request"
  }'

# Add labels to an issue
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/issues/NUMBER/labels \
  -d '{
    "labels": ["bug", "urgent"]
  }'
```

## 🌐 **GraphQL API**

### **GraphQL Basics**
```bash
# GraphQL endpoint
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/graphql \
  -d '{
    "query": "query { viewer { login }}"
  }'
```

### **Complex Queries**
```graphql
# Get repository information with PRs
query {
  repository(owner: "OWNER", name: "REPO") {
    name
    description
    pullRequests(first: 10, states: [OPEN]) {
      nodes {
        number
        title
        author {
          login
        }
        reviews(first: 5) {
          nodes {
            author {
              login
            }
            state
          }
        }
      }
    }
  }
}

# Get user contributions
query {
  user(login: "USERNAME") {
    contributionsCollection {
      totalCommitContributions
      totalPullRequestContributions
      totalIssueContributions
    }
  }
}
```

### **Mutations**
```graphql
# Create an issue
mutation {
  createIssue(input: {
    repositoryId: "REPO_ID",
    title: "New issue",
    body: "Issue description"
  }) {
    issue {
      number
      url
    }
  }
}

# Merge a pull request
mutation {
  mergePullRequest(input: {
    pullRequestId: "PR_ID",
    commitHeadline: "Merge PR",
    mergeMethod: MERGE
  }) {
    pullRequest {
      merged
      url
    }
  }
}
```

## 🔗 **Webhooks**

### **Webhook Configuration**
```bash
# List webhooks
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/hooks

# Create a webhook
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/hooks \
  -d '{
    "name": "web",
    "active": true,
    "events": ["push", "pull_request"],
    "config": {
      "url": "https://example.com/webhook",
      "content_type": "json",
      "secret": "your_secret"
    }
  }'

# Test a webhook
curl -X POST -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/hooks/HOOK_ID/tests
```

### **Webhook Events**
Common events to listen for:
- `push`: Code pushed to repository
- `pull_request`: PR opened, closed, or updated
- `issues`: Issues created or modified
- `release`: Releases published
- `workflow_run`: GitHub Actions workflow completed

## 🤖 **GitHub Apps**

### **App Creation**
```bash
# Create a GitHub App (via web interface)
# 1. Go to https://github.com/settings/apps
# 2. Fill in app details
# 3. Configure permissions
# 4. Generate private key
```

### **App Authentication**
```python
import jwt
import requests
from datetime import datetime, timedelta

def get_installation_token(installation_id, app_id, private_key):
    now = datetime.utcnow()
    payload = {
        'iat': now,
        'exp': now + timedelta(minutes=10),
        'iss': app_id
    }

    token = jwt.encode(payload, private_key, algorithm='RS256')

    response = requests.post(
        f'https://api.github.com/app/installations/{installation_id}/access_tokens',
        headers={
            'Authorization': f'Bearer {token}',
            'Accept': 'application/vnd.github.machine-man-preview+json'
        }
    )

    return response.json()['token']
```

## 🛠️ **Automation Scripts**

### **PR Merger Script**
```python
import requests
import time

def merge_pr_if_ready(owner, repo, pr_number, token):
    headers = {
        'Authorization': f'token {token}',
        'Accept': 'application/vnd.github.v3+json'
    }

    # Check PR status
    pr_url = f'https://api.github.com/repos/{owner}/{repo}/pulls/{pr_number}'
    pr_response = requests.get(pr_url, headers=headers)
    pr_data = pr_response.json()

    if pr_data['mergeable'] and pr_data['mergeable_state'] == 'clean':
        # Check reviews
        reviews_url = f'{pr_url}/reviews'
        reviews_response = requests.get(reviews_url, headers=headers)
        reviews = reviews_response.json()

        approved_reviews = [r for r in reviews if r['state'] == 'APPROVED']

        if len(approved_reviews) >= 2:  # Require 2 approvals
            # Merge PR
            merge_url = f'{pr_url}/merge'
            merge_data = {
                'commit_title': f'Merge pull request #{pr_number}',
                'merge_method': 'squash'
            }

            merge_response = requests.put(merge_url, headers=headers, json=merge_data)

            if merge_response.status_code == 200:
                print(f'Successfully merged PR #{pr_number}')
            else:
                print(f'Failed to merge PR #{pr_number}: {merge_response.json()}')
        else:
            print(f'PR #{pr_number} needs more approvals')
    else:
        print(f'PR #{pr_number} is not ready to merge')
```

### **Automated Labeling**
```python
def label_pr_based_on_size(owner, repo, pr_number, token):
    headers = {
        'Authorization': f'token {token}',
        'Accept': 'application/vnd.github.v3+json'
    }

    # Get PR files
    files_url = f'https://api.github.com/repos/{owner}/{repo}/pulls/{pr_number}/files'
    files_response = requests.get(files_url, headers=headers)
    files = files_response.json()

    total_changes = sum(f['changes'] for f in files)

    # Determine label based on size
    if total_changes < 50:
        label = 'size:XS'
    elif total_changes < 200:
        label = 'size:S'
    elif total_changes < 500:
        label = 'size:M'
    elif total_changes < 1000:
        label = 'size:L'
    else:
        label = 'size:XL'

    # Add label
    labels_url = f'https://api.github.com/repos/{owner}/{repo}/issues/{pr_number}/labels'
    labels_data = {'labels': [label]}

    requests.post(labels_url, headers=headers, json=labels_data)
```

## 📊 **Analytics and Reporting**

### **Repository Statistics**
```bash
# Get repository stats
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/stats/contributors

# Get commit activity
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/stats/commit_activity

# Get code frequency
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/stats/code_frequency
```

### **Traffic Analytics**
```bash
# Get traffic views
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/traffic/views

# Get traffic clones
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/traffic/clones

# Get popular referrers
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/traffic/popular/referrers
```

## 🔒 **Security Best Practices**

### **Token Management**
```bash
# Use fine-grained personal access tokens
# Rotate tokens regularly
# Store tokens securely (environment variables, secret managers)
# Never commit tokens to code

# Example: Using environment variables
TOKEN=$GITHUB_TOKEN
curl -H "Authorization: token $TOKEN" https://api.github.com/user
```

### **Rate Limiting**
```python
import time
import requests

class GitHubAPIClient:
    def __init__(self, token):
        self.token = token
        self.last_request_time = 0
        self.min_interval = 1  # Minimum seconds between requests

    def make_request(self, url, method='GET', data=None):
        # Rate limiting
        elapsed = time.time() - self.last_request_time
        if elapsed < self.min_interval:
            time.sleep(self.min_interval - elapsed)

        headers = {
            'Authorization': f'token {self.token}',
            'Accept': 'application/vnd.github.v3+json'
        }

        response = requests.request(method, url, headers=headers, json=data)
        self.last_request_time = time.time()

        # Handle rate limiting
        if response.status_code == 403:
            reset_time = int(response.headers.get('X-RateLimit-Reset', 0))
            wait_time = max(0, reset_time - time.time())
            if wait_time > 0:
                print(f'Rate limited. Waiting {wait_time} seconds...')
                time.sleep(wait_time)
                return self.make_request(url, method, data)

        return response
```

## 📚 **Advanced Topics**

### **Pagination**
```bash
# Handle paginated responses
curl -H "Authorization: token YOUR_TOKEN" \
  "https://api.github.com/repos/OWNER/REPO/issues?page=2&per_page=100"

# GraphQL pagination
query {
  repository(owner: "OWNER", name: "REPO") {
    issues(first: 100, after: "CURSOR") {
      pageInfo {
        hasNextPage
        endCursor
      }
      nodes {
        number
        title
      }
    }
  }
}
```

### **Conditional Requests**
```bash
# Use ETags for caching
curl -H "Authorization: token YOUR_TOKEN" \
  -H "If-None-Match: \"ETAG\"" \
  https://api.github.com/repos/OWNER/REPO

# Use Last-Modified
curl -H "Authorization: token YOUR_TOKEN" \
  -H "If-Modified-Since: Mon, 01 Jan 2024 00:00:00 GMT" \
  https://api.github.com/repos/OWNER/REPO
```

## 🔗 **Resources**

- [GitHub REST API Documentation](https://docs.github.com/en/rest)
- [GitHub GraphQL API Documentation](https://docs.github.com/en/graphql)
- [GitHub Webhooks Documentation](https://docs.github.com/en/webhooks)
- [GitHub Apps Documentation](https://docs.github.com/en/apps)
- [PyGitHub Library](https://github.com/PyGitHub/PyGitHub)
- [Octokit Libraries](https://github.com/octokit)

---

**Pro Tip:** Start with the REST API for simple operations, then explore GraphQL for complex queries that need specific data efficiently.