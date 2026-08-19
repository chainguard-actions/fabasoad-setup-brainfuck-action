<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-brainfuck-action/v1.1.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct expression interpolation in a `run:` block. The composite action's run step uses `${{ inputs.version }}` directly inside the shell command `pip3 install brainfucky==${{ inputs.version }}`. An attacker-controlled `version` input could inject arbitrary shell commands (e.g., `; malicious-command #`).

Locations:

- `action.yml:15`

### script-injection (severity: high)

Rule (a): Direct expression interpolation in `run:` blocks. Three expressions are interpolated directly into shell commands: (1) `${{ github.repository }}` is embedded in a curl URL on the 'Get latest release tag' step; (2) `${{ steps.latest_release.outputs.tag }}` and `${{ steps.current_release.outputs.tag }}` are embedded in a `git log` command in the 'Build changelog' step. These values flow through YAML template substitution before the shell sees them, enabling command injection.

Locations:

- `.github/workflows/create-release.yml:18`
- `.github/workflows/create-release.yml:26`

### unpinned-uses (severity: high)

Action references are pinned to mutable tags rather than immutable full-length SHA digests, making the workflow vulnerable to supply-chain attacks if the tag is moved. Failing references: `actions/checkout@v3` (line 16) and `actions/create-release@v1` (line 33).

Locations:

- `.github/workflows/create-release.yml:16`
- `.github/workflows/create-release.yml:33`

### unpinned-uses (severity: high)

Action reference is pinned to a mutable tag rather than an immutable full-length SHA digest, making the workflow vulnerable to supply-chain attacks if the tag is moved. Failing reference: `actions/checkout@v3` (line 22).

Locations:

- `.github/workflows/functional-tests.yml:22`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and the single job (`create_release`) also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, pull-requests, etc.).

Locations:

- `.github/workflows/create-release.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and the single job (`functional_tests`) also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/functional-tests.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Brainfuck"; move to env: map

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 7 findings across 3 files:

1. action.yml (script-injection / static-inline-injection): Moved `${{ inputs.version }}` out of the `run:` shell command into an `env:` block as `VERSION`, referenced as `"${VERSION}"` in the pip3 install command.

2. .github/workflows/create-release.yml (script-injection): Moved `${{ github.repository }}` into env block as `REPOSITORY` for the curl step; moved `${{ steps.latest_release.outputs.tag }}` and `${{ steps.current_release.outputs.tag }}` into env block as `LATEST_TAG` and `CURRENT_TAG` for the git log step.

3. .github/workflows/create-release.yml (unpinned-uses): Pinned `actions/checkout@v3` → `@a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3` and `actions/create-release@v1` → `@0cb9c9b65d5d1901c1f53e5e66eaf4afd303e70e # v1`.

4. .github/workflows/create-release.yml (missing-permissions): Added `permissions: contents: write` at workflow level (required to create GitHub releases).

5. .github/workflows/functional-tests.yml (unpinned-uses): Pinned `actions/checkout@v3` → `@a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3`.

6. .github/workflows/functional-tests.yml (missing-permissions): Added `permissions: {}` at workflow level (no special permissions needed).

