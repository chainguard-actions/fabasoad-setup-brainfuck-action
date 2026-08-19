<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-brainfuck-action/v1.1.3** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.version }}` into a shell command string: `pip3 install brainfucky==${{ inputs.version }}`. The GitHub Actions template engine substitutes this expression before the shell processes it, allowing an attacker to inject arbitrary shell metacharacters (e.g., `;`, `|`, `&`) via the `version` input. The value should be passed through an `env:` variable and double-quoted in the shell command instead.

Locations:

- `action.yml:15`

### unpinned-uses (severity: high)

Multiple workflow files reference Actions using mutable tags or branch names instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks:
- functional-tests.yml: `actions/checkout@v4` (tag)
- linting.yml: `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main` (branch)
- release.yml: `actions/checkout@v4` (tag), `simbo/changes-since-last-release-action@v1` (tag), `softprops/action-gh-release@v2` (tag), `fischerscode/tagger@v0` (tag)

Locations:

- `.github/workflows/functional-tests.yml:17`
- `.github/workflows/linting.yml:12`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:16`
- `.github/workflows/release.yml:19`
- `.github/workflows/release.yml:25`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and none of their jobs define job-level `permissions:` blocks. Without explicit permissions, workflows run with the default (potentially broad) token permissions. All three workflow files are affected: functional-tests.yml, linting.yml, and release.yml.

Locations:

- `.github/workflows/functional-tests.yml:1`
- `.github/workflows/linting.yml:1`
- `.github/workflows/release.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Brainfuck"; move to env: map

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings:
1. script-injection/static-inline-injection (action.yml line 15/18): Moved `${{ inputs.version }}` to an `env:` block as `VERSION` and referenced it as `"brainfucky==$VERSION"` in the shell command.
2. unpinned-uses: Pinned all mutable references to full SHAs — actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, simbo/changes-since-last-release-action@v1 → @45883b23a1c40599d6967b447b919a5663aaf23d, softprops/action-gh-release@v2 → @3bb12739c298aeb8a4eeaf626c5b8d85266b0e65, fischerscode/tagger@v0 → @5ca3fa63ce3003fb7183cae547644b29f3b632be, fabasoad/reusable-workflows@main → @4e2279474e598bee3ae8ded28899a24bbc7bf971.
3. missing-permissions: Added `permissions: {}` at the top level of all three workflow files. For release.yml, added `permissions: contents: write` at the job level to allow creating releases and tags.

