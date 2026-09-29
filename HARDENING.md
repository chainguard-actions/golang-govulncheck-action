<!-- markdownlint-disable -->

# Hardening Report: golang--govulncheck-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **golang--govulncheck-action/v1.0.4** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of full 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten.
- `actions/checkout@v4.1.1` (line ~40)
- `actions/setup-go@v5.0.0` (line ~42)
These should be pinned to their full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.1.1`.

Locations:

- `action.yml:40`
- `action.yml:42`

### script-injection (severity: high)

Two `run:` steps directly interpolate user-controlled `inputs.*` expressions into shell command strings (sub-rule a). An attacker who controls the calling workflow can supply values containing shell metacharacters (`;`, `|`, `$(...)`, etc.) to achieve arbitrary command execution.

Affected step "Run govulncheck" (line ~52):
  `govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }}`

Affected step "Run govulncheck and save to file" (line ~56):
  `govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }} > ${{ inputs.output-file }}`

All four inputs (`work-dir`, `output-format`, `go-package`, `output-file`) are `required: false` and caller-supplied. They must be moved to `env:` variables and double-quoted in the shell script.

Locations:

- `action.yml:52`
- `action.yml:56`

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
3. Moved all ${{ inputs.* }} expressions from run: blocks to env: maps (WORK_DIR, OUTPUT_FORMAT, GO_PACKAGE, OUTPUT_FILE) and referenced them with double-quoted "$VAR" syntax in both 'Run govulncheck' and 'Run govulncheck and save to file' steps.

