# Code Analysis with Secret Detection Workflow

This GitHub Actions workflow scans a repository for committed credentials using [Gitleaks](https://github.com/gitleaks/gitleaks). It publishes the findings to the job summary, uploads them to GitHub code scanning as SARIF, and fails the job when a potential secret is detected.

## Workflow Configuration

The workflow is triggered using the `workflow_call` event, allowing for flexible inputs for different parameters.

## Inputs

In the workflow, inputs are used to define configurable parameters that can be set when invoking the workflow. Below are the possible input parameters:

| Input Name           | Type    | Required | Default          | Description                                                                         |
| -------------------- | ------- | -------- | ---------------- | ----------------------------------------------------------------------------------- |
| `SCAN_MODE`          | string  | No       | `dir`            | Gitleaks scan mode. `dir` scans the working tree, `git` scans the commit history.   |
| `SCAN_ARGS`          | string  | No       | `""`             | Additional Gitleaks flags, for example `--baseline-path gitleaks-baseline.json`.    |
| `GITLEAKS_CONFIG`    | string  | No       | `""`             | Path to a custom `gitleaks.toml`. Gitleaks uses its bundled rule set when empty.    |
| `WORKING_DIR`        | string  | No       | `.`              | The directory that is scanned.                                                      |
| `FETCH_DEPTH`        | string  | No       | `0`              | Checkout depth. Keep `0` so `SCAN_MODE: git` can walk the full history.             |
| `REDACT_ENABLED`     | boolean | No       | `true`           | Redacts the matched value in the report and the logs.                               |
| `SARIF_ENABLED`      | boolean | No       | `true`           | Uploads the SARIF report to GitHub code scanning.                                   |
| `ARTIFACT_ENABLED`   | boolean | No       | `true`           | Uploads the SARIF report as a workflow artifact.                                    |
| `FAIL_ON_DETECTION`  | boolean | No       | `true`           | Fails the job on a detection. When `false`, the detection is reported as a warning. |
| `OS_VERSION`         | string  | No       | `ubuntu-24.04`   | The operating system version used in the GitHub runner.                             |
| `GITLEAKS_VERSION`   | string  | No       | `latest`         | The Gitleaks release to install, for example `8.24.3`.                              |
| `ARTIFACT_NAME`      | string  | No       | `gitleaks`       | The name of the artifact being uploaded.                                            |
| `ARTIFACT_PATH`      | string  | No       | `gitleaks.sarif` | The report file name, relative to `WORKING_DIR`.                                    |
| `ARTIFACT_RETENTION` | string  | No       | `1`              | The retention period for the artifact.                                              |

## Secrets

This workflow requires no secrets. It authenticates to the GitHub API with the automatically provided `github.token`.

## Permissions

The job requests the following permissions:

| Permission        | Access  | Purpose                                           |
| ----------------- | ------- | ------------------------------------------------- |
| `contents`        | `read`  | Checks out the repository being scanned.          |
| `security-events` | `write` | Uploads the SARIF report to GitHub code scanning. |

> Code scanning uploads require a public repository or GitHub Advanced Security. Set `SARIF_ENABLED: false` when neither applies.

## Workflow Jobs

### 1. Code Analysis Job

- **Job Name**: `code-analysis`
- **Runs On**: The workflow will run on the operating system defined by the `OS_VERSION` input.

#### Steps:

1. **Prepare Repository**
   - **Action**: `actions/checkout@v4`
   - **Purpose**: Checks out the repository at the depth defined by `FETCH_DEPTH`.

2. **Setup Gitleaks**
   - **Purpose**: Resolves `GITLEAKS_VERSION` and installs the Gitleaks binary into `/usr/local/bin`.

3. **Secret Detection Analysis**
   - **Purpose**: Runs `gitleaks` in `SCAN_MODE` and writes a SARIF report to `ARTIFACT_PATH`. Gitleaks is invoked with `--exit-code 2`, so a detection is distinguished from a tool failure: exit code `2` marks findings, and any other non-zero exit code fails the step as an error.

4. **Secret Detection Summary**
   - **Purpose**: Renders the findings as a table in the GitHub job summary.

5. **Upload Analysis Result**
   - **Action**: `github/codeql-action/upload-sarif@v3`
   - **Purpose**: Publishes the report to the repository's code scanning alerts under the `secret-detection` category.

6. **Upload Artifact**
   - **Action**: `actions/upload-artifact@v4`
   - **Purpose**: Uploads the SARIF report using the `ARTIFACT_NAME` and `ARTIFACT_RETENTION` inputs.

7. **Verify Secret Detection**
   - **Purpose**: Fails the job when a secret was detected and `FAIL_ON_DETECTION` is `true`, otherwise emits a warning.

## Example Usage

```yaml
name: Main
on:
  push:
    branches:
      - master
jobs:
  secret-detection:
    name: Secret Detection
    uses: bayudwiyansatria/.github/.github/workflows/code-analysis-secret.yml@master
```

Scanning the full commit history with a custom rule set:

```yaml
name: Main
on:
  pull_request:
jobs:
  secret-detection:
    name: Secret Detection
    uses: bayudwiyansatria/.github/.github/workflows/code-analysis-secret.yml@master
    with:
      SCAN_MODE: git
      GITLEAKS_CONFIG: .gitleaks.toml
      GITLEAKS_VERSION: 8.24.3
```

Reporting findings without failing the pipeline:

```yaml
name: Main
on:
  pull_request:
jobs:
  secret-detection:
    name: Secret Detection
    uses: bayudwiyansatria/.github/.github/workflows/code-analysis-secret.yml@master
    with:
      FAIL_ON_DETECTION: false
      SARIF_ENABLED: false
```

## Handling a Detection

A detection means the credential is in the repository, and rewriting history does not undo the exposure:

1. Rotate the exposed credential at its provider.
2. Move the value into GitHub Actions secrets or another secret manager.
3. Remove the value from the code and, where required, from the commit history.
4. Record an accepted finding in a Gitleaks baseline or `.gitleaksignore` so it stops failing the build.
