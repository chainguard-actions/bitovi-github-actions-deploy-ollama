<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-deploy-ollama/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-deploy-ollama/v0.2.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten:
- `uses: actions/checkout@v4` (tag: v4)
- `uses: bitovi/github-actions-commons@v1` (tag: v1)
These should be pinned to full SHA digests, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yaml:237`
- `action.yaml:283`

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansions of workflow-controllable data in `run:` blocks.

1. In the 'Copy Deployment Config' step, `$GITHUB_ACTION_PATH` (set from `${{ github.action_path }}`) is expanded unquoted: `cp -r  $GITHUB_ACTION_PATH/. "$app_path"`. Shell metacharacters in the value would be parsed by the shell.

2. In the same step, `$app_path` (derived from the unquoted `$GITHUB_WORKSPACE`) is used unquoted: `rm -rf $app_path/operations`.

3. In the 'Set app env config' step, `$OLLAMA_MODELS` (set from `${{ inputs.ollama_models }}`) is expanded unquoted: `echo "MODELS=$OLLAMA_MODELS" >> $app_path/$filename`. An attacker-controlled value with shell metacharacters or newlines could alter the command or inject additional env file entries.

Locations:

- `action.yaml:248`
- `action.yaml:251`
- `action.yaml:275`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 and bitovi/github-actions-commons@v1 to SHA a4636c6df1c274226c029461b31dac4bb79fce09, preserving original tags as comments.
2. Fixed all unquoted shell variable expansions in the 'Copy Deployment Config' and 'Set app env config' steps: quoted $GITHUB_ACTION_PATH, $app_path, and all $app_path/$filename redirections. Also quoted the app_path assignment in both steps to prevent word-splitting on $GITHUB_WORKSPACE.

