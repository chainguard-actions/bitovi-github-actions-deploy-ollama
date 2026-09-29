<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-deploy-ollama/v0.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml use mutable version tags instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten:
- `uses: actions/checkout@v2` (tag `v2`)
- `uses: bitovi/github-actions-commons@v0.0.13` (tag `v0.0.13`)
These should be pinned to full SHA digests, e.g. `actions/checkout@<40-char-sha> # v2`.

Locations:

- `action.yaml:163`
- `action.yaml:207`

### script-injection (severity: high)

Rule (b) violation — unquoted shell expansion of env vars holding workflow-controllable context values.

In the 'Copy Deployment Config' step, the env var `GITHUB_ACTION_PATH` is populated from `${{ github.action_path }}` and then expanded **unquoted** in the run script:
  `cp -r  $GITHUB_ACTION_PATH/. "$app_path"`
An attacker-controlled path value with shell metacharacters (spaces, globs, etc.) could alter command behavior. The variable must be double-quoted: `"$GITHUB_ACTION_PATH"`.

Similarly, `$app_path` (derived from `$GITHUB_WORKSPACE/$APP_SUBDIR`) is used unquoted in:
  `rm -rf $app_path/operations`

Locations:

- `action.yaml:188`
- `action.yaml:191`

### script-injection (severity: high)

Rule (b) violation — unquoted shell expansion of env vars holding workflow-controllable context values.

In the 'Set app env config' step, the env var `GITHUB_ACTION_PATH` is again populated from `${{ github.action_path }}` and `DISABLE_SIGNUP` from `${{ inputs.disable-signup }}`. While `$DISABLE_SIGNUP` is safely double-quoted in the `if` comparison, `$app_path` (derived from the inherited `$GITHUB_WORKSPACE`) is used unquoted in:
  `echo "ENABLE_SIGNUP=false" >> $app_path/$filename`
  `echo "ENABLE_SIGNUP=true" >> $app_path/$filename`
These path expansions must be quoted: `"$app_path/$filename"`.

Locations:

- `action.yaml:198`
- `action.yaml:220`
- `action.yaml:223`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all findings in hardened/action/action.yaml:
1. Pinned `actions/checkout@v2` to `actions/checkout@0717577d45739eb3c851188b29f50ed6c0b2194e # v2`
2. Pinned `bitovi/github-actions-commons@v0.0.13` to `bitovi/github-actions-commons@1921283129dc73b959a15637ee81690ce4f6f984 # v0.0.13`
3. Quoted `$GITHUB_ACTION_PATH` in the 'Copy Deployment Config' step: `cp -r "$GITHUB_ACTION_PATH/." "$app_path"`
4. Quoted `$app_path/operations` in the 'Copy Deployment Config' step: `rm -rf "$app_path/operations"`
5. Quoted both `$app_path/$filename` redirect targets in the 'Set app env config' step for the ENABLE_SIGNUP=false and ENABLE_SIGNUP=true echo statements.

