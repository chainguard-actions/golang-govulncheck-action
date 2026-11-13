<!-- markdownlint-disable -->

# Hardening Report: golang--govulncheck-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **golang--govulncheck-action/v1.0.4** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite steps point to version tags instead of full 40-character commit SHAs: 'uses: actions/checkout@v4.1.1' (line 43) and 'uses: actions/setup-go@v5.0.0' (line 44).

Locations:

- `action.yml:43`
- `action.yml:44`

### script-injection (severity: high)

Rule (a): expressions are inserted straight into the run: script. The step 'Run govulncheck' runs: 'govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }}'. Each input should move into an env: variable and be expanded with double quotes.

Locations:

- `action.yml:55`

### script-injection (severity: high)

Rule (a): expressions are inserted straight into the run: script. The step 'Run govulncheck and save to file' runs: 'govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }} > ${{ inputs.output-file }}'. Each input should move into an env: variable and be expanded with double quotes.

Locations:

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index f7c11f6..1df16bb 100644
--- a/action.yml
+++ b/action.yml
@@ -39,9 +39,26 @@ inputs:
 runs:
   using: "composite"
   steps:
     - if: inputs.repo-checkout != 'false' # only explicit false prevents repo checkout
       uses: actions/checkout@v4.1.1
     - uses: actions/setup-go@v5.0.0
       with:
         go-version: ${{ inputs.go-version-input }}
         check-latest: ${{ inputs.check-latest }}
@@ -52,9 +69,18 @@ runs:
       shell: bash
     - if: inputs.output-file == ''
       name: Run govulncheck
-      run: govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }}
+      env:
+        WORK_DIR: ${{ inputs.work-dir }}
+        OUTPUT_FORMAT: ${{ inputs.output-format }}
+        GO_PACKAGE: ${{ inputs.go-package }}
+      run: govulncheck -C "$WORK_DIR" -format "$OUTPUT_FORMAT" "$GO_PACKAGE"
       shell: bash
     - if: inputs.output-file != ''
       name: Run govulncheck and save to file
-      run: govulncheck -C ${{ inputs.work-dir }} -format ${{ inputs.output-format }} ${{ inputs.go-package }} > ${{ inputs.output-file }}
+      env:
+        WORK_DIR: ${{ inputs.work-dir }}
+        OUTPUT_FORMAT: ${{ inputs.output-format }}
+        GO_PACKAGE: ${{ inputs.go-package }}
+        OUTPUT_FILE: ${{ inputs.output-file }}
+      run: govulncheck -C "$WORK_DIR" -format "$OUTPUT_FORMAT" "$GO_PACKAGE" > "$OUTPUT_FILE"
       shell: bash

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, prior-fix

**Notes:**

All findings in action.yml are fixed. I haven't run the action to test the changes.

- **Pinned actions:** `actions/checkout` is pinned to b4ffde65f46336ab88eb53be808477a3936bae11 (v4.1.1) and `actions/setup-go` to 0c52d547c9bc32b1aa3301fd7a9cb496313a4491 (v5.0.0). The tag stays as a comment on each line.
- **Script injection:** Both govulncheck steps now take `work-dir`, `output-format`, `go-package` and `output-file` from the step's `env:` block instead of inline `${{ }}` expressions. The single values are passed double-quoted.
- **Different from the previous harden:** that patch passed `go-package` as one quoted argument (`"$GO_PACKAGE"`). I split it into separate arguments instead. Upstream pasted it straight into the command, so a value listing several packages (for example `./cmd/... ./pkg/...`) reached govulncheck as several arguments, and one quoted string would have broken that. The split respects quotes the same way the original command did and does not run anything. It stops with an error if a quote is left open, and an empty value adds no argument.

