<!-- markdownlint-disable -->

# Hardening Report: golang--govulncheck-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **golang--govulncheck-action/v1.0.4** was hardened automatically. 9 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved:
- `actions/checkout@v4.1.1`
- `actions/setup-go@v5.0.0`
These should be replaced with their full SHA digests (e.g. `actions/checkout@<40-char-sha> # v4.1.1`).

Locations:

- `action.yml:43`
- `action.yml:44`

### script-injection (severity: high)

Two `run:` blocks directly interpolate `${{ inputs.* }}` expressions into shell command strings (sub-rule a). All four inputs — `inputs.work-dir`, `inputs.output-format`, `inputs.go-package`, and `inputs.output-file` — are caller-controlled and are substituted into the shell command before the shell ever sees them, enabling command injection via crafted input values containing shell metacharacters.

Offending lines:
- `run: govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }}`
- `run: govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }} > ${{ inputs.output-file }}`

Fix: move each input into an `env:` variable and reference it as a double-quoted shell variable, e.g.:
```yaml
env:
  WORK_DIR: ${{ inputs.work-dir }}
  OUTPUT_FORMAT: ${{ inputs.output-format }}
  GO_PACKAGE: ${{ inputs.go-package }}
run: govulncheck -C "$WORK_DIR" -format "$OUTPUT_FORMAT" "$GO_PACKAGE"
```

Locations:

- `action.yml:51`
- `action.yml:55`

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
1. Pinned actions/checkout@v4.1.1 to SHA b4ffde65f46336ab88eb53be808477a3936bae11
2. Pinned actions/setup-go@v5.0.0 to SHA 0c52d547c9bc32b1aa3301fd7a9cb496313a4491
3. Moved all ${{ inputs.* }} expressions (work-dir, output-format, go-package, output-file) from run: blocks into env: maps, referencing them as double-quoted shell variables to prevent script injection in both the 'Run govulncheck' and 'Run govulncheck and save to file' steps.

