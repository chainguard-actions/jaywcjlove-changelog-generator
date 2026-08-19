<!-- markdownlint-disable -->

# Hardening Report: jaywcjlove--changelog-generator/v2.2.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jaywcjlove--changelog-generator/v2.2.6** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in .github/workflows/changelog.yml use mutable tags or branch names instead of pinned 40-character SHA digests, making the workflow vulnerable to supply-chain attacks: `actions/checkout@v4` (line 11), `actions/setup-node@v4` (line 12), `jaywcjlove/github-action-contributors@main` (line 42), `jaywcjlove/coverage-badges-cli@main` (line 46), `peaceiris/actions-gh-pages@v4` (line 49), `jaywcjlove/create-tag-action@main` (line 53), `jaywcjlove/changelog-generator@main` (line 100), `ncipollo/release-action@v1` (line 112).

Locations:

- `.github/workflows/changelog.yml:11`
- `.github/workflows/changelog.yml:12`
- `.github/workflows/changelog.yml:42`
- `.github/workflows/changelog.yml:46`
- `.github/workflows/changelog.yml:49`
- `.github/workflows/changelog.yml:53`
- `.github/workflows/changelog.yml:100`
- `.github/workflows/changelog.yml:112`

### unpinned-uses (severity: high)

Multiple `uses:` references in .github/workflows/path.yml use mutable tags or branch names instead of pinned 40-character SHA digests: `actions/checkout@v4` (line 11), `actions/setup-node@v4` (line 12), `jaywcjlove/create-tag-action@main` (line 21).

Locations:

- `.github/workflows/path.yml:11`
- `.github/workflows/path.yml:12`
- `.github/workflows/path.yml:21`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and the single job also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/changelog.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and the single job also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/path.yml:1`

### script-injection (severity: high)

Multiple `run:` steps directly interpolate `${{ steps.*.outputs.* }}` expressions into shell commands (sub-rule a). The `steps.*` context is workflow-controllable data that flows through YAML template substitution before the shell processes it, allowing an attacker to inject arbitrary shell commands if any upstream step produces a malicious output value. Affected lines: `- run: echo "outputs.changelog - ${{ steps.changelog.outputs.changelog }}"` (line 70), `- run: echo "outputs.tag - ${{ steps.changelog.outputs.tag }}"` (line 71), `- run: echo "outputs.branch - ${{ steps.changelog.outputs.branch }}"` (line 72), `- run: echo "outputs.compareurl - ${{ steps.changelog.outputs.compareurl }}"` (line 73), `- run: echo "outputs.version - ${{ steps.changelog.outputs.version }}"` (line 74), `- run: echo "outputs.gh-pages-hash - ${{ steps.changelog.outputs.gh-pages-hash }}"` (line 75), `- run: echo "outputs.gh-pages-hash - ${{ steps.changelog.outputs['gh-pages-hash'] }}"` (line 76), `- run: echo "outputs.gh-pages-short-hash - ${{ steps.changelog.outputs.gh-pages-short-hash }}"` (line 77), and `run: echo "${{ steps.changelogEmoji.outputs.changelog }}"` (line 97). These values should be passed via env vars and the env vars double-quoted in the shell.

Locations:

- `.github/workflows/changelog.yml:70`
- `.github/workflows/changelog.yml:71`
- `.github/workflows/changelog.yml:72`
- `.github/workflows/changelog.yml:73`
- `.github/workflows/changelog.yml:74`
- `.github/workflows/changelog.yml:75`
- `.github/workflows/changelog.yml:76`
- `.github/workflows/changelog.yml:77`
- `.github/workflows/changelog.yml:97`

### script-injection (severity: high)

Multiple `run:` steps directly interpolate `${{ steps.*.outputs.* }}` expressions into shell commands (sub-rule a). The `steps.*` context is workflow-controllable data that flows through YAML template substitution before the shell processes it. Affected lines: `- run: echo "outputs.tag - ${{ steps.changelog.outputs.tag }}"` (line 36), `- run: echo "outputs.branch - ${{ steps.changelog.outputs.branch }}"` (line 37), `- run: echo "outputs.compareurl - ${{ steps.changelog.outputs.compareurl }}"` (line 38), `- run: echo "outputs.version - ${{ steps.changelog.outputs.version }}"` (line 39), `- run: echo "outputs.gh-pages-hash - ${{ steps.changelog.outputs.gh-pages-hash }}"` (line 40), `- run: echo "outputs.gh-pages-hash - ${{ steps.changelog.outputs['gh-pages-hash'] }}"` (line 41), `- run: echo "outputs.gh-pages-short-hash - ${{ steps.changelog.outputs.gh-pages-short-hash }}"` (line 42).

Locations:

- `.github/workflows/path.yml:36`
- `.github/workflows/path.yml:37`
- `.github/workflows/path.yml:38`
- `.github/workflows/path.yml:39`
- `.github/workflows/path.yml:40`
- `.github/workflows/path.yml:41`
- `.github/workflows/path.yml:42`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings in both .github/workflows/changelog.yml and .github/workflows/path.yml:

1. **unpinned-uses**: Pinned all 8 action references in changelog.yml and 3 in path.yml to full 40-character SHA digests, preserving the original tag/branch name in a trailing comment.

2. **missing-permissions**: Added top-level `permissions:` blocks to both files. changelog.yml gets `contents: write`, `pages: write`, `id-token: write` (needed for gh-pages deployment and release creation). path.yml gets `contents: write` (needed for tag creation).

3. **script-injection**: Consolidated all individual `run: echo "${{ steps.*.outputs.* }}"` steps into single named steps with `env:` blocks that map each `${{ steps.*.outputs.* }}` expression to an environment variable. Shell commands then reference the plain env vars (e.g., `$TAG`, `$CHANGELOG`) instead of inline template expressions. This applies to 9 affected lines in changelog.yml and 7 in path.yml.

