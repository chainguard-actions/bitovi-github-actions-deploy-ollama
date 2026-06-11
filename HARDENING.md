<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **bitovi--github-actions-deploy-ollama/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable tags rather than full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved:
- `uses: actions/checkout@v2` (line ~167)
- `uses: bitovi/github-actions-commons@v0.0.13` (line ~196)

Locations:

- `action.yaml:167`
- `action.yaml:196`

### script-injection (severity: high)

Sub-rule (b) violation: In the 'Copy Deployment Config' step, the env var `GITHUB_ACTION_PATH` is sourced from `${{ github.action_path }}` (a workflow-controllable `github.*` context) and then expanded unquoted in shell commands:
  `cp -r  $GITHUB_ACTION_PATH/. "$app_path"`
  `rm -rf $app_path/operations`
Unquoted shell variable expansion allows shell metacharacters in the value to be interpreted by the shell. The variable should be double-quoted: `"$GITHUB_ACTION_PATH"`.

Locations:

- `action.yaml:183`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. Pinned `actions/checkout@v2` to full SHA `ee0669bd1cc54295c223e0bb666b733df41de1c5` (comment `# v2` preserved). 2. Pinned `bitovi/github-actions-commons@v0.0.13` to full SHA `1921283129dc73b959a15637ee81690ce4f6f984` (comment `# v0.0.13` preserved). 3. Fixed unquoted shell variable expansion in the 'Copy Deployment Config' step: changed `cp -r  $GITHUB_ACTION_PATH/. "$app_path"` to `cp -r "$GITHUB_ACTION_PATH/." "$app_path"` and `rm -rf $app_path/operations` to `rm -rf "$app_path/operations"`. All SHAs were resolved via lookup_action_sha.

