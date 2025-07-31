# Release Process

This document outlines the recommended release process for the Augment Review PR GitHub Action.

## Quick Release (Recommended)

Use the built-in npm scripts for easy version bumping:

```bash
# For patch releases (bug fixes): 0.1.0 -> 0.1.1
bun run version:patch

# For minor releases (new features): 0.1.0 -> 0.2.0
bun run version:minor

# For major releases (breaking changes): 0.1.0 -> 1.0.0
bun run version:major
```

These scripts will:

1. Update the version in `package.json`
2. Create a git tag (e.g., `v0.1.1`)
3. Push the changes and tag to GitHub
4. Trigger the automated release workflow

## Manual Release Process

If you prefer manual control:

1. **Update the version in package.json:**

   ```bash
   bun version 0.1.1  # or whatever version you want
   ```

2. **Type check the project:**

   ```bash
   bun run typecheck
   ```

3. **Commit the changes:**

   ```bash
   git add package.json
   git commit -m "chore: release v0.1.1"
   ```

4. **Create and push the tag:**
   ```bash
   git tag v0.1.1
   git push origin main
   git push origin v0.1.1
   ```

## What Happens During Release

When a tag is pushed, the GitHub workflow will:

1. ✅ Verify the package.json version matches the tag
2. 🔍 Type check the project to ensure it compiles
3. 🏷️ Update the major version tag (e.g., `v1` for `v1.2.3`)
4. 📦 Create a GitHub release with usage examples
5. 🚀 Make the release available for users

## Version Strategy

- **Patch** (0.0.X): Bug fixes, documentation updates
- **Minor** (0.X.0): New features, backwards compatible
- **Major** (X.0.0): Breaking changes

## Major Version Tags

The release process automatically maintains major version tags:

- `v0.1.5` creates/updates `v0`
- `v1.2.3` creates/updates `v1`
- `v2.0.0` creates/updates `v2`

This allows users to pin to major versions for automatic updates:

```yaml
- uses: augmentcode/review-pr@v1 # Gets latest v1.x.x
```

## Troubleshooting

**Version mismatch error:**

- Ensure package.json version matches the git tag version
- The tag should be `v` + the package.json version (e.g., `v0.1.1`)

**Type check failures:**

- Run `bun run typecheck` locally to check for TypeScript errors
- Ensure all dependencies are properly installed

**Release not created:**

- Check the GitHub Actions logs for detailed error messages
- Verify the GITHUB_TOKEN has proper permissions
