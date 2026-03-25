# CodeCanary Action

GitHub Action that runs [CodeCanary](https://github.com/alansikora/codecanary) on pull requests. It installs the CLI, obtains a GitHub App token via OIDC, and executes the review.

For setup instructions, configuration, and how CodeCanary works, see the [main repo](https://github.com/alansikora/codecanary).

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `claude_code_oauth_token` | Claude OAuth token | — |
| `anthropic_api_key` | Anthropic API key (alternative) | — |
| `config_path` | Path to config file | `.codecanary.yml` |
| `post_comment` | Post findings as PR comment | `true` |
| `codecanary_version` | CLI version to install | `latest` |
| `reply_only` | Only evaluate thread replies | `false` |

## Usage

```yaml
- uses: alansikora/codecanary-action@v1
  with:
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
    reply_only: ${{ github.event_name == 'pull_request_review_comment' }}
```

To use the canary (development) release of the CLI instead:

```yaml
- uses: alansikora/codecanary-action@canary
```

## License

MIT
