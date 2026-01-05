# Advanced GitHub Actions Tips

This guide covers advanced GitHub Actions features including matrices, concurrency, environments, secrets management, and OIDC for secure deployments.

## Matrix Builds

Run jobs across multiple configurations:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]
        os: [ubuntu-latest, windows-latest, macos-latest]
        exclude:
          - os: macos-latest
            node-version: 16
        include:
          - os: ubuntu-latest
            node-version: 18
            experimental: true
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - name: Run tests
        run: npm test
```

## Concurrency Control

Prevent multiple runs of the same workflow:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## Environments

Manage deployment environments:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to production
        run: echo "Deploying to production"
```

## Secrets Management

### Repository Secrets
- Go to Settings > Secrets and variables > Actions
- Add secrets like `API_KEY`, `DATABASE_URL`

### Environment Secrets
- Create environment in Settings > Environments
- Add secrets specific to that environment

### Using Secrets in Workflows

```yaml
steps:
  - name: Use secret
    run: echo ${{ secrets.MY_SECRET }}
```

## OIDC for Secure Deployments

Use OpenID Connect for secure cloud deployments:

```yaml
permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Azure login
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Deploy to Azure
        run: az webapp up --name myapp --resource-group mygroup
```

## Advanced Features

### Job Dependencies and Outputs

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version.outputs.version }}
    steps:
      - id: version
        run: echo "version=$(date +%s)" >> $GITHUB_OUTPUT

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy version ${{ needs.build.outputs.version }}
        run: echo "Deploying version ${{ needs.build.outputs.version }}"
```

### Conditional Jobs

```yaml
jobs:
  test:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running on PR"

  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying to production"
```

### Custom Actions

Create your own actions for reusable logic:

```yaml
# .github/actions/hello-world/action.yml
name: 'Hello World'
description: 'Greet someone'
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
    default: 'World'
runs:
  using: 'node16'
  main: 'index.js'
```

## Best Practices

1. **Security First**: Use least privilege principles for tokens and secrets
2. **Caching**: Use actions/cache to speed up builds
3. **Artifacts**: Store build artifacts for later use
4. **Notifications**: Send notifications on workflow completion
5. **Monitoring**: Monitor workflow performance and costs
6. **Documentation**: Document complex workflows thoroughly