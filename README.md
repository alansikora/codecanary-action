# CodeCanary Action

GitHub Action for AI-powered code review on pull requests.

## Quick Setup

Run `codecanary init` in your repo — it handles everything (app installation, auth, workflow creation, config generation).

```sh
npx codecanary init
```

## Manual Setup

### 1. Install the CodeCanary Review app

Install [CodeCanary Review](https://github.com/apps/codecanary-review) on your repository.

### 2. Add Claude authentication

Set one of these as a GitHub Actions secret:

- `CLAUDE_CODE_OAUTH_TOKEN` — OAuth token (recommended)
- `ANTHROPIC_API_KEY` — API key

### 3. Create the workflow

```yaml
# .github/workflows/codecanary-review.yml
name: CodeCanary Review

on:
  pull_request:
    types: [opened, synchronize]
  pull_request_review_comment:
    types: [created]

permissions:
  contents: read
  id-token: write
  pull-requests: write

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.pull_request.head.sha || github.sha }}

      - uses: alansikora/codecanary-action@v1
        with:
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
          reply_only: ${{ github.event_name == 'pull_request_review_comment' }}
```

### 4. Add a config file

Create `.codecanary.yml` in your repo root:

```yaml
version: 1

context: |
  Describe your project stack and conventions here.

rules:
  - id: example-rule
    description: "What to check for"
    severity: warning

ignore:
  - "dist/**"
  - "*.lock"
```

Or generate one automatically:

```sh
npx codecanary review generate
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `claude_code_oauth_token` | Claude OAuth token | — |
| `anthropic_api_key` | Anthropic API key (alternative) | — |
| `config_path` | Path to config file | `.codecanary.yml` |
| `post_comment` | Post findings as PR comment | `true` |
| `codecanary_version` | CLI version to install | `latest` |
| `reply_only` | Only evaluate thread replies | `false` |

## How It Works

1. Installs the CodeCanary CLI and Claude Code
2. Obtains a GitHub App token via OIDC exchange
3. Runs `codecanary review` on the pull request
4. Posts findings as inline review comments

On subsequent pushes, CodeCanary re-evaluates existing threads and only reports new findings. Resolved issues are automatically detected and marked.

## License

MIT
