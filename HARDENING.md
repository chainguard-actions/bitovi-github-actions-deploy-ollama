<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-deploy-ollama/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten:
- `actions/checkout@v2` (mutable version tag)
- `bitovi/github-actions-commons@v0.0.13` (mutable version tag)
These should be pinned to full commit SHAs, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v2`.

Locations:

- `action.yaml:166`
- `action.yaml:226`

### script-injection (severity: high)

Rule (b) violation — unquoted shell variable expansions of workflow-controllable data in `run:` blocks.

In the 'Copy Deployment Config' step, `$GITHUB_ACTION_PATH` is populated from `${{ github.action_path }}` (a github.* context value) and then used unquoted in the shell:
  `cp -r  $GITHUB_ACTION_PATH/. "$app_path"`
  `rm -rf $app_path/operations`
The `$app_path` variable is derived from `$GITHUB_WORKSPACE` (a workflow-controlled env var) and is also used unquoted throughout both run blocks.

In the 'Set app env config' step, `$app_path` is again used unquoted:
  `echo "ENABLE_SIGNUP=false" >> $app_path/$filename`
  `echo "ENABLE_SIGNUP=true" >> $app_path/$filename`

Unquoted expansions allow shell metacharacters (spaces, globs, semicolons, etc.) embedded in the values to be interpreted by the shell, enabling command injection. All expansions of workflow-controllable variables must be double-quoted.

Locations:

- `action.yaml:193`
- `action.yaml:195`
- `action.yaml:215`
- `action.yaml:218`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed two unpinned-uses findings by pinning actions/checkout@v2 to SHA 0717577d45739eb3c851188b29f50ed6c0b2194e and bitovi/github-actions-commons@v0.0.13 to SHA 1921283129dc73b959a15637ee81690ce4f6f984, with original tags preserved as comments. Fixed script-injection findings by double-quoting all unquoted variable expansions: `$GITHUB_ACTION_PATH/.` → `"$GITHUB_ACTION_PATH/."`, `$app_path/operations` → `"$app_path/operations"`, and both `$app_path/$filename` redirections → `"$app_path/$filename"` in the 'Copy Deployment Config' and 'Set app env config' steps.

