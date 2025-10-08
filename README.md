# Augment Review PR GitHub Action

AI-powered pull request reviews using Auggie. This action automatically analyzes your PR changes and generates comprehensive, informative reviews.

## Quick Start

### 1. Get Your Augment API Credentials

First, you'll need to obtain your Augment Authentication information from your local Auggie session:

Example session JSON:

```json
{
  "accessToken": "your-api-token-here",
  "tenantURL": "https://your-tenant.api.augmentcode.com"
}
```

There are 2 ways to get the credentials:

- Run `auggie tokens print`
  - Copy the JSON after `TOKEN=`
- Copy the credentials stored in your Augment cache directory, defaulting to `~/.augment/session.json`

> **⚠️ Security Warning**: These tokens are OAuth tokens tied to your personal Augment account and provide access to your Augment services. They are not tied to a team or enterprise. Treat them as sensitive credentials:
>
> - Never commit them to version control
> - Only store them in secure locations (like GitHub secrets)
> - Don't share them in plain text or expose them in logs
> - If a token is compromised, immediately revoke it using `auggie tokens revoke`

### 2. Set Up the GitHub Repository Secret

You need to add your Augment credentials to your GitHub repository:

#### Adding Secret

1. **Navigate to your repository** on GitHub
2. **Go to Settings** → **Secrets and variables** → **Actions**
3. **Add the following**:
   - **Secret**: Click "New repository secret"
     - Name: `AUGMENT_SESSION_AUTH`
     - Value: The JSON value from step 1

> **Need more help?** For detailed instructions on managing GitHub secrets, see GitHub's official documentation:
>
> - [Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

### 3. Create Your Workflow File

Add a new workflow file to your repository's `.github/workflows/` directory and merge it.

### Example Workflows

For complete workflow examples, see the [`example-workflows/`](./example-workflows/) directory which contains:

- **[Basic PR Review](./example-workflows/basic-pr-review.yml)** - Simple setup for all new PRs
- **[Custom Guidelines Review](./example-workflows/custom-guidelines-review.yml)** - Demonstrates custom guidelines usage
- **[Draft PR Review](./example-workflows/draft-pr-review.yml)** - Only review draft PRs
- **[Feature Branch Review](./example-workflows/feature-branch-review.yml)** - Target specific branch patterns with labeling
- **[Robust PR Review](./example-workflows/robust-pr-review.yml)** - Includes error handling and fallback notifications
- **[On-Demand Review](./example-workflows/on-demand-review.yml)** - Triggered by adding the `augment_review` label

Each example includes a complete workflow file that you can copy to your `.github/workflows/` directory and customize for your needs.

## Features

- **Automatic PR Analysis**: Analyzes code changes, file modifications, and diff content
- **Intelligent Reviews**: Generates comprehensive PR reviews using AI
- **Context-Aware**: Understands your codebase structure and change patterns
- **GitHub Integration**: Seamlessly updates PR reviews via GitHub API

## Inputs

| Input                  | Description                                                                                | Required | Example                                             |
| ---------------------- | ------------------------------------------------------------------------------------------ | -------- | --------------------------------------------------- |
| `augment_session_auth` | Augment session authentication JSON containing accessToken and tenantURL (store as secret) | Yes      | `${{ secrets.AUGMENT_SESSION_AUTH }}`               |
| `github_token`         | GitHub token with `repo` scopes                                                            | Yes      | `${{ secrets.GITHUB_TOKEN }}`                       |
| `pull_number`          | The number of the pull request being reviewed                                              | Yes      | `${{ github.event.pull_request.number }}`           |
| `repo_name`            | The full name (owner/repo) of the repository                                               | Yes      | `${{ github.repository }}`                          |
| `custom_guidelines`    | Custom guidelines to include in PR reviews                                                 | No       | See [Custom Guidelines](#custom-guidelines) section |
| `model`                | Optional model name to use; passed directly to augment agent                               | No       | e.g. `sonnet4`, from `auggie --list-models`         |
| `rules`                | JSON array of rule file paths forwarded to augment agent as repeated `--rules` flags       | No       | `'[".augment/rules.md"]'`                           |
| `mcp_configs`          | JSON array of MCP config file paths forwarded as repeated `--mcp-config` flags             | No       | `'[".augment/mcp.json"]'`                           |
| `fetch_depth`          | Number of commits to fetch. Use `0` for full history (default), `1` for shallow clone, or any positive integer for specific depth | No       | `1` (shallow), `50` (last 50 commits), `0` (full) |

## How It Works

1. **Checkout**: Checks out the PR head to access the complete codebase
2. **Data Gathering**: Fetches PR metadata, changed files, and diff content from GitHub API
3. **Template Rendering**: Uses Nunjucks templates to create a structured instruction for the AI
4. **AI Processing**: Calls the Augment Agent to analyze the changes and generate a review
5. **PR Review Submission**: The Augment Agent submits a single GitHub review containing a brief summary and multiple inline comments anchored to specific lines in the PR diff (including suggestion blocks where appropriate)

## Custom Guidelines

You can provide custom guidelines to tailor the PR review process to your team's specific needs. These guidelines will be added to the default review criteria.

### Basic Usage

```yaml
- name: Generate PR Review
  uses: augmentcode/review-pr@v0
  with:
    augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pull_number: ${{ github.event.pull_request.number }}
    repo_name: ${{ github.repository }}
    custom_guidelines: |
      - Focus on TypeScript type safety and avoid using `any`
      - Ensure all new functions have JSDoc comments
      - Check for proper error handling in async functions
      - Verify that new components follow our design system
```

## Permissions

The action requires the following GitHub token permissions:

```yaml
permissions:
  contents: read # To checkout the repository and read files
  pull-requests: write # To update PR descriptions
```
