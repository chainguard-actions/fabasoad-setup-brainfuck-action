<!-- markdownlint-disable -->

# Hardening Report: fabasoad--setup-brainfuck-action/v1.1.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fabasoad--setup-brainfuck-action/v1.1.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in the 'Install Brainfuck' step directly interpolates `${{ inputs.version }}` into a shell command string: `pip3 install brainfucky==${{ inputs.version }}`. The `inputs.version` value is caller-controlled and is substituted into the shell command before the shell ever sees it. An attacker can supply a crafted version string containing shell metacharacters (e.g., `; malicious-command`, `$(...)`, `|`) to achieve arbitrary command execution on the runner.

Locations:

- `action.yml:17`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "Install Brainfuck"; move to env: map

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed the script injection vulnerability in action.yml by moving `${{ inputs.version }}` from the `run:` shell string into an `env:` block as `VERSION`. The shell command was updated from `pip3 install brainfucky==${{ inputs.version }}` to `pip3 install "brainfucky==$VERSION"`, ensuring the caller-controlled input is never directly interpolated into the shell command string.

