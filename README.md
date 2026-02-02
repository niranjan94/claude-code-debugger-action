_# Claude Code Debugger Action

A GitHub Action that provides remote access to Claude Code within your GitHub Actions workflow. This allows you to debug CI/CD issues interactively using Claude's AI-powered coding assistant.

## Features

- Installs Claude Code CLI in your GitHub Actions runner
- Creates a breakpoint that pauses the workflow for remote access
- Persists Claude sessions across workflow runs using artifacts
- Supports multiple authentication methods (Anthropic API key, OAuth token)
- Access control via authorized users list

## Usage

```yaml
- uses: niranjan94/claude-code-debugger-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    authorized_users: "your-github-username"
    duration: "30m"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `anthropic_api_key` | Anthropic API key (not needed for Bedrock/Vertex/Foundry) | No | - |
| `claude_code_oauth_token` | Claude Code OAuth token (alternative to API key) | No | - |
| `github_token` | GitHub token with repo and pull request permissions | No | - |
| `authorized_users` | Comma-separated list of GitHub usernames allowed to use this action | Yes | - |
| `duration` | The breakpoint duration (how long to wait for connection) | Yes | `30m` |

## How It Works

1. The action installs Claude Code CLI on the runner
2. It restores any previous Claude session from artifacts (if available)
3. Creates a breakpoint using [namespacelabs/breakpoint-action](https://github.com/namespacelabs/breakpoint-action) that pauses the workflow
4. You can connect to the runner and use Claude Code interactively
5. When the session ends, it saves the Claude session state for future runs

## Example Workflow

```yaml
name: Debug with Claude

on:
  workflow_dispatch:

jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Start Claude Code Debugger
        uses: niranjan94/claude-code-debugger-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          authorized_users: "niranjan94,another-user"
          duration: "1h"
```

## Authentication

You can authenticate Claude Code using one of these methods:

1. **Anthropic API Key**: Set `anthropic_api_key` input with your API key
2. **OAuth Token**: Set `claude_code_oauth_token` input with your OAuth token
3. **Cloud Providers**: For AWS Bedrock, Google Vertex, or Azure Foundry, configure the appropriate environment variables in your workflow

## Session Persistence

Claude sessions are automatically saved as artifacts and restored on subsequent runs. This allows you to:

- Resume previous conversations
- Maintain context across multiple debugging sessions
- Share session state within the same branch

Sessions are stored with a 3-day retention period.

## License

MIT_