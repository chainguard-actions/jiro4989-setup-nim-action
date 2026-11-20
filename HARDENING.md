<!-- markdownlint-disable -->

# Hardening Report: jiro4989--setup-nim-action/v2.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jiro4989--setup-nim-action/v2.5.0** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the 'Install nim' step puts expressions straight into the run script: "${{ github.action_path }}/install_nim.sh", --nim-version "${{ inputs.nim-version }}", --parent-nim-install-directory "${{ inputs.parent-nim-install-directory }}", --nim-install-directory "${{ inputs.nim-install-directory }}", --os "${{ runner.os }}", --use-nightlies "${{ inputs.use-nightlies }}", --nimble-commit "${{ inputs.nimble-commit }}", --repo-token "${{ inputs.repo-token }}". Fix: pass each value through env: and reference it as a double-quoted shell variable.

Locations:

- `action.yml:51`

### script-injection (severity: high)

Rule (a): the 'Set PATH for Unix' step puts expressions straight into the run script: parent="${{ inputs.parent-nim-install-directory }}" and echo "$parent/${{ inputs.nim-install-directory }}/bin" >> "$GITHUB_PATH".

Locations:

- `action.yml:62`
- `action.yml:66`

### script-injection (severity: high)

Rule (a): the 'Set PATH for Windows' step (pwsh) puts expressions straight into the run script: $parent = "${{ inputs.parent-nim-install-directory }}" and echo "$parent\${{ inputs.nim-install-directory }}\bin" >> $Env:GITHUB_PATH.

Locations:

- `action.yml:73`
- `action.yml:77`

### github-env-injection (severity: high)

The 'Set PATH for Unix' step writes inputs.parent-nim-install-directory and inputs.nim-install-directory to $GITHUB_PATH without stripping newlines first (printf '%s' ... | tr -d '\n\r'). A newline in either input would add extra PATH entries.

Locations:

- `action.yml:66`

### github-env-injection (severity: high)

The 'Set PATH for Windows' step writes inputs.parent-nim-install-directory and inputs.nim-install-directory to $Env:GITHUB_PATH without stripping newlines first, so a newline in either input would add extra PATH entries.

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

In action.yml, every ${{ }} expression in the 'Install nim' (bash), 'Set PATH for Unix' (bash) and 'Set PATH for Windows' (pwsh) run blocks now goes through the step's env: block. Bash reads them as "$VAR" and PowerShell as "$env:VAR". The arguments passed to install_nim.sh are the same as upstream's, including empty values. Before each step writes its path to GITHUB_PATH, it now strips CR/LF from the path: bash uses printf | tr -d '\n\r' and PowerShell uses -replace "[`r`n]". No other changes, and nothing was run to test the edits.

