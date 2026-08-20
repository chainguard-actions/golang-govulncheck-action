<!-- markdownlint-disable -->

# Hardening Report: golang--govulncheck-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **golang--govulncheck-action/v1.0.4** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of full 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved:
- `actions/checkout@v4.1.1` (line 44)
- `actions/setup-go@v5.0.0` (line 45)
These should be pinned to their full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.1.1`.

Locations:

- `action.yml:44`
- `action.yml:45`

### script-injection (severity: high)

Rule (a): Multiple `run:` blocks in action.yml directly interpolate `inputs.*` expressions inside shell command strings. These values are user-controlled and flow through YAML template substitution before the shell parses them, enabling command injection (e.g. a crafted `inputs.work-dir` value containing shell metacharacters like `;`, `|`, `$(...)` would be executed by the runner shell).

Affected steps and offending expressions:
- Step "Run govulncheck" (line ~55): `govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }}`
- Step "Run govulncheck and save to file" (line ~59): `govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }} > ${{ inputs.output-file }}`

Fix: Move each input into an `env:` variable and reference it as a double-quoted shell variable, e.g.:
```yaml
env:
  WORK_DIR: ${{ inputs.work-dir }}
  OUTPUT_FORMAT: ${{ inputs.output-format }}
  GO_PACKAGE: ${{ inputs.go-package }}
run: govulncheck -C "$WORK_DIR" -format "$OUTPUT_FORMAT" "$GO_PACKAGE"
```

Locations:

- `action.yml:55`
- `action.yml:59`

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

Fixed all 9 findings in hardened/action/action.yml:
1. Pinned actions/checkout@v4.1.1 to full SHA b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
2. Pinned actions/setup-go@v5.0.0 to full SHA 0c52d547c9bc32b1aa3301fd7a9cb496313a4491 # v5.0.0
3. Fixed script injection in 'Run govulncheck' step: moved inputs.work-dir, inputs.output-format, and inputs.go-package into env: block as WORK_DIR, OUTPUT_FORMAT, GO_PACKAGE; updated run: to use double-quoted env vars.
4. Fixed script injection in 'Run govulncheck and save to file' step: moved all four inputs (work-dir, output-format, go-package, output-file) into env: block; updated run: to use double-quoted env vars including the output redirection target.

