# Example Workflows

This directory contains example GitHub Actions workflows for the Augment Review PR action. Copy any of these files to your `.github/workflows/` directory and customize them for your needs.

## Available Examples

### 📝 [Basic PR Review](./basic-pr-review.yml)

The simplest setup - automatically generates reviews for all new pull requests.

**Triggers:** When a PR is opened
**Use case:** Standard setup for most repositories

### ⚙️ [Custom Guidelines Review](./custom-guidelines-review.yml)

Demonstrates how to use custom guidelines to tailor reviews to your project's specific requirements.

**Triggers:** When a PR is opened or updated
**Use case:** Teams with specific coding standards, security requirements, or architectural patterns

### 🚧 [Draft PR Review](./draft-pr-review.yml)

Only generates reviews for draft pull requests, allowing you to iterate on your changes before the final review.

**Triggers:** When a PR is opened as draft or converted to draft
**Use case:** Teams that use draft PRs for work-in-progress reviews

### 🌿 [Feature Branch Review](./feature-branch-review.yml)

Targets specific branch patterns and adds automatic labeling for feature branches.

**Triggers:** PRs from `feature/` or `feat/` branches to main/develop/release branches
**Use case:** Teams with structured branching strategies

### 🛡️ [Robust PR Review](./robust-pr-review.yml)

Includes error handling, retry logic, and fallback notifications when review generation fails.

**Triggers:** When a PR is opened
**Use case:** Production environments where reliability is critical

### 🏷️ [On-Demand Review](./on-demand-review.yml)

Generates reviews only when the `augment_review` label is manually added to a PR.

**Triggers:** When the `augment_review` label is added
**Use case:** Manual control over when reviews are generated, useful for selective usage

## Required Permissions

All workflows require these permissions:

```yaml
permissions:
  contents: read # To checkout the repository
  pull-requests: write # To update PR reviews
```

## Customization Tips

### Custom Guidelines

You can add project-specific guidelines to any workflow by including the `custom_guidelines` input:

```yaml
- name: Generate PR Review
  uses: augmentcode/review-pr@v0
  with:
    # ... other inputs ...
    custom_guidelines: |
      - Focus on TypeScript type safety
      - Ensure proper error handling
      - Check for security vulnerabilities
      - Verify test coverage for new features
```

### Trigger Events

You can modify when the workflow runs by changing the `on` section:

```yaml
# Run on multiple events
on:
  pull_request:
    types: [opened, synchronize, reopened]

# Run only for specific paths
on:
  pull_request:
    types: [opened]
    paths:
      - 'src/**'
      - '!docs/**'
```

### Conditional Execution

Add conditions to control when the action runs:

```yaml
# Only for external contributors
if: github.event.pull_request.head.repo.full_name != github.repository

# Only for large PRs
if: github.event.pull_request.changed_files > 5

# Skip for certain labels
if: "!contains(github.event.pull_request.labels.*.name, 'skip-review')"
```

## Troubleshooting

### Common Issues

1. **Missing permissions**: Ensure your workflow has `pull-requests: write` permission
2. **API rate limits**: Consider adding delays between API calls for high-traffic repositories
3. **Large PRs**: The action handles pagination automatically, but very large PRs may take longer

### Debugging

Enable debug logging by adding this to your workflow:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
```
