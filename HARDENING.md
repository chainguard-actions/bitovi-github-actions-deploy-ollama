<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **bitovi--github-actions-deploy-ollama/v0.2.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yaml references two actions using mutable tag-based refs instead of pinned 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the upstream tags are moved or compromised.

- `uses: actions/checkout@v4` (tag ref `v4`)
- `uses: bitovi/github-actions-commons@v1` (tag ref `v1`)

Locations:

- `action.yaml:246`
- `action.yaml:285`

### script-injection (severity: high)

Rule (b) violation: In the 'Copy Deployment Config' step, the env var `GITHUB_ACTION_PATH` is populated from `${{ github.action_path }}` (a github.* context value) and then expanded **unquoted** in the shell command `cp -r $GITHUB_ACTION_PATH/. "$app_path"`. An unquoted shell expansion of a workflow-controllable value allows the shell to parse metacharacters (spaces, globs, etc.) from the value. It should be `"$GITHUB_ACTION_PATH"`.

Locations:

- `action.yaml:255`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed three issues in action.yaml: (1) Pinned actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5; (2) Pinned bitovi/github-actions-commons@v1 to SHA a4636c6df1c274226c029461b31dac4bb79fce09; (3) Fixed unquoted $GITHUB_ACTION_PATH in the 'Copy Deployment Config' step's cp command — wrapped it in double quotes to prevent shell metacharacter injection.

