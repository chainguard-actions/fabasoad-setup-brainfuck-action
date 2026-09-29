<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-brainfuck-action/v1.2.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v6`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. This exposes the action to supply-chain attacks if the tag is moved to a different commit.

Locations:

- `action.yml:44`

### script-injection (severity: high)

Rule (b) violation: In the 'Install brainfucky' step, the env var `VERSION` is populated from `${{ case(inputs.version == 'latest', steps.latest-release.outputs.version, inputs.version) }}` — sourcing from `inputs.version` (caller-controlled) and `steps.latest-release.outputs.version` (workflow-controllable). The `run:` block then expands it **unquoted** as `brainfucky==${VERSION}`, allowing an attacker to inject shell metacharacters (e.g. `;`, `|`, `$(...)`) via the `version` input. The fix is to double-quote the expansion: `brainfucky=="${VERSION}"`.

Offending line: `run: python -m pip install brainfucky==${VERSION}`

Locations:

- `action.yml:65`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. Pinned `actions/setup-python@v6` to immutable SHA `ece7cb06caefa5fff74198d8649806c4678c61a1` with `# v6` comment for readability (action.yml line 44). 2. Double-quoted the `${VERSION}` expansion in the pip install command: changed `brainfucky==${VERSION}` to `"brainfucky==${VERSION}"` to prevent shell injection via caller-controlled `inputs.version` (action.yml line 65).

