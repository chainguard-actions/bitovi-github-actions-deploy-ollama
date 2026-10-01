<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-deploy-ollama/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable version tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised.

1. `uses: actions/checkout@v2` — should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v2`
2. `uses: bitovi/github-actions-commons@v0.0.13` — should be pinned to a full SHA, e.g. `bitovi/github-actions-commons@<40-char-sha> # v0.0.13`

Locations:

- `action.yaml:162`
- `action.yaml:207`

### script-injection (severity: high)

Sub-rule (b) violation: In the 'Copy Deployment Config' step, the env var `$GITHUB_ACTION_PATH` (sourced from `${{ github.action_path }}`) is expanded unquoted in the shell command `cp -r $GITHUB_ACTION_PATH/. "$app_path"`. Similarly, `$app_path` (derived from `$GITHUB_WORKSPACE/$APP_SUBDIR`, where `$GITHUB_WORKSPACE` is an inherited env var) is used unquoted in `rm -rf $app_path/operations`. Unquoted shell expansions allow the shell to parse metacharacters (`;`, `|`, `&`, glob chars, whitespace) out of the value, enabling command injection. All workflow-controllable env vars must be double-quoted in shell expansions.

Locations:

- `action.yaml:181`
- `action.yaml:184`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed three issues in hardened/action/action.yaml:
1. Pinned `actions/checkout@v2` to `actions/checkout@0717577d45739eb3c851188b29f50ed6c0b2194e # v2`.
2. Pinned `bitovi/github-actions-commons@v0.0.13` to `bitovi/github-actions-commons@1921283129dc73b959a15637ee81690ce4f6f984 # v0.0.13`.
3. Fixed script-injection in the 'Copy Deployment Config' step: changed `cp -r $GITHUB_ACTION_PATH/. "$app_path"` to `cp -r "$GITHUB_ACTION_PATH/." "$app_path"` and `rm -rf $app_path/operations` to `rm -rf "$app_path/operations"` to properly double-quote all shell variable expansions.

