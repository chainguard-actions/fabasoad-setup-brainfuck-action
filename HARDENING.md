<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fabasoad--setup-brainfuck-action/v1.2.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag/branch is moved or compromised.

Failing references:
- action.yml: `actions/setup-python@v6`
- functional-tests.yml: `actions/checkout@v7` (two steps)
- linting.yml: `fabasoad/reusable-workflows/.github/workflows/wf-pre-commit.yml@main`
- release.yml: `fabasoad/reusable-workflows/.github/workflows/wf-github-release.yml@main`
- security.yml: `fabasoad/reusable-workflows/.github/workflows/wf-security-sast.yml@main`
- sync-labels.yml: `fabasoad/reusable-workflows/.github/workflows/wf-sync-labels.yml@main`
- update-license.yml: `fabasoad/reusable-workflows/.github/workflows/wf-update-license.yml@main`

Locations:

- `action.yml:47`
- `.github/workflows/functional-tests.yml:44`
- `.github/workflows/functional-tests.yml:73`
- `.github/workflows/linting.yml:9`
- `.github/workflows/release.yml:8`
- `.github/workflows/security.yml:18`
- `.github/workflows/sync-labels.yml:9`
- `.github/workflows/update-license.yml:9`

### script-injection (severity: high)

Rule (b): In the 'Install brainfucky' step of action.yml, the env var `VERSION` is populated from `inputs.version` (an untrusted caller-controlled input) and then expanded **unquoted** inside the `run:` shell command: `python -m pip install brainfucky==${VERSION}`. An attacker-controlled version string containing shell metacharacters (`;`, `|`, `$()`, etc.) could break out of the pip version specifier and execute arbitrary commands. The fix is to double-quote the expansion: `brainfucky=="${VERSION}"`.

Locations:

- `action.yml:62`

### script-injection (severity: high)

Rule (a): In the 'Test action completion' step of the `test-force` job in functional-tests.yml, the expression `${{ matrix.force }}` is interpolated **directly** inside a `run:` shell command string (used as the third argument to `test_equal`). Any GitHub Actions expression inside a `run:` block is substituted before the shell sees it, allowing a crafted matrix value to inject arbitrary shell commands. The value should be moved to an `env:` variable and the env var double-quoted in the script instead.

Offending line: `"${{ matrix.force }}"`

Locations:

- `.github/workflows/functional-tests.yml:99`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 8 unpinned-uses findings by resolving full commit SHAs via lookup_action_sha: actions/setup-python@v6→ece7cb06..., actions/checkout@v7→3d3c42e5... (2 occurrences), and all 5 fabasoad/reusable-workflows@main references→ecce8eb7.... Fixed 2 script-injection findings: (1) in action.yml, quoted the VERSION env var in the pip install command as "brainfucky==${VERSION}" to prevent shell metacharacter injection; (2) in functional-tests.yml, moved ${{ matrix.force }} from the run block into an env variable MATRIX_FORCE and referenced it as "${MATRIX_FORCE}" in the shell script.

