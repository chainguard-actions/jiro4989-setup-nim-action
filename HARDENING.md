<!-- markdownlint-disable -->

# Hardening Report: jiro4989--setup-nim-action/v2.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jiro4989--setup-nim-action/v2.5.1** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the 'Install nim' step puts expressions straight into the run: script: "${{ github.action_path }}/install_nim.sh", --nim-version "${{ inputs.nim-version }}", --parent-nim-install-directory "${{ inputs.parent-nim-install-directory }}", --nim-install-directory "${{ inputs.nim-install-directory }}", --os "${{ runner.os }}", --use-nightlies "${{ inputs.use-nightlies }}", --nimble-commit "${{ inputs.nimble-commit }}", --repo-token "${{ inputs.repo-token }}". Pass these through env: variables and use them double-quoted.

Locations:

- `action.yml:51`

### script-injection (severity: high)

Rule (a): the 'Set PATH for Unix' step puts inputs straight into bash: parent="${{ inputs.parent-nim-install-directory }}" and echo "$parent/${{ inputs.nim-install-directory }}/bin" >> "$GITHUB_PATH".

Locations:

- `action.yml:62`

### script-injection (severity: high)

Rule (a): the 'Set PATH for Windows' step puts inputs straight into pwsh: $parent = "${{ inputs.parent-nim-install-directory }}" and echo "$parent\${{ inputs.nim-install-directory }}\bin" >> $Env:GITHUB_PATH.

Locations:

- `action.yml:73`

### github-env-injection (severity: high)

The 'Set PATH for Unix' step writes inputs.parent-nim-install-directory and inputs.nim-install-directory to $GITHUB_PATH without first stripping newlines with printf '%s' ... | tr -d '\n\r'. The line is: echo "$parent/${{ inputs.nim-install-directory }}/bin" >> "$GITHUB_PATH". A value containing a newline can add any directory to PATH.

Locations:

- `action.yml:66`

### github-env-injection (severity: high)

The 'Set PATH for Windows' step writes inputs.parent-nim-install-directory and inputs.nim-install-directory to $Env:GITHUB_PATH without stripping newlines. The line is: echo "$parent\${{ inputs.nim-install-directory }}\bin" >> $Env:GITHUB_PATH.

Locations:

- `action.yml:77`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.nim-version }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:52`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.parent-nim-install-directory }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:53`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.nim-install-directory }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.use-nightlies }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.nimble-commit }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repo-token }}" appears directly in run: block of step "Install nim"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.parent-nim-install-directory }}" appears directly in run: block of step "Set PATH for Unix"; move to env: map

Locations:

- `action.yml:63`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.nim-install-directory }}" appears directly in run: block of step "Set PATH for Unix"; move to env: map

Locations:

- `action.yml:67`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.parent-nim-install-directory }}" appears directly in run: block of step "Set PATH for Windows"; move to env: map

Locations:

- `action.yml:74`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.nim-install-directory }}" appears directly in run: block of step "Set PATH for Windows"; move to env: map

Locations:

- `action.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

I moved every ${{ }} expression in the three run: steps of action.yml into env: blocks. In the bash steps the values are now used double-quoted ("$VAR"). The pwsh step uses $env:VAR. Before anything is written to GITHUB_PATH, newlines are now stripped: with printf '%s' | tr -d '\n\r' on Unix, and with -replace "[`r`n]", "" on Windows. The install script still gets the same arguments as upstream, and an empty value is still passed as an empty argument. Nothing was run to test these changes.

