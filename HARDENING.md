<!-- markdownlint-disable -->

# Hardening Report: golang--govulncheck-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **golang--govulncheck-action/v1.0.4** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in action.yml directly interpolate `${{ inputs.* }}` expressions into shell command strings without routing through env vars. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be executed by the shell. Affected expressions: `${{ inputs.work-dir }}`, `${{ inputs.output-format }}`, `${{ inputs.go-package }}`, and `${{ inputs.output-file }}` appear directly in `govulncheck` invocations. These inputs should be moved to `env:` variables and then double-quoted in the shell script.

Locations:

- `action.yml:48`
- `action.yml:53`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of immutable 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten. Failing references: `actions/checkout@v4.1.1` and `actions/setup-go@v5.0.0`. These should be pinned to their full SHA, e.g. `actions/checkout@<40-hex-sha> # v4.1.1`.

Locations:

- `action.yml:43`
- `action.yml:44`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.work-dir }}" appears directly in run: block of step "Run govulncheck"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output-format }}" appears directly in run: block of step "Run govulncheck"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.go-package }}" appears directly in run: block of step "Run govulncheck"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.work-dir }}" appears directly in run: block of step "Run govulncheck and save to file"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output-format }}" appears directly in run: block of step "Run govulncheck and save to file"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.go-package }}" appears directly in run: block of step "Run govulncheck and save to file"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output-file }}" appears directly in run: block of step "Run govulncheck and save to file"; move to env: map

Locations:

- `action.yml:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. Pinned actions/checkout@v4.1.1 → @b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
2. Pinned actions/setup-go@v5.0.0 → @0c52d547c9bc32b1aa3301fd7a9cb496313a4491 # v5.0.0
3. Moved ${{ inputs.work-dir }}, ${{ inputs.output-format }}, ${{ inputs.go-package }}, and ${{ inputs.output-file }} from run: blocks into env: maps as INPUT_WORK_DIR, INPUT_OUTPUT_FORMAT, INPUT_GO_PACKAGE, and INPUT_OUTPUT_FILE respectively. All env vars are double-quoted in the shell commands to prevent word-splitting and shell injection.

