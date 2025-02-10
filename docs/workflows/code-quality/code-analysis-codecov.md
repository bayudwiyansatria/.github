# Code Analysis with Codecov Workflow

This GitHub Actions workflow automates the process of analyzing code coverage using Codecov. It ensures that every time changes are pushed to the repository, the code is tested, and the coverage report is uploaded to Codecov.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

## Inputs

In the workflow, inputs are used to define configurable parameters that can be set when invoking the workflow. Below are the possible input parameters:

| Input Name            | Type   | Required | Default Value                         | Description                                              |
| --------------------- | ------ | -------- | ------------------------------------- | -------------------------------------------------------- |
| `OS_VERSION`          | string | No       | `ubuntu-24.04`                        | The operating system version used in the GitHub runner.  |
| `ARTIFACT_REPOSITORY` | string | No       | `${{ github.event.repository.name }}` | The repository name where the artifact will be uploaded. |
| `ARTIFACT_NAME`       | string | No       | `coverage`                            | The name of the artifact being uploaded.                 |
| `ARTIFACT_PATH`       | string | No       | `dist/coverage`                       | The path where the coverage file is located.             |
| `ARTIFACT_RETENTION`  | string | No       | `1`                                   | The retention period for the artifact.                   |
| `ARTIFACT_FILE`       | string | No       | `clover.xml`                          | The name of the coverage file to be uploaded to Codecov. |

## Secrets

Secrets are sensitive values that are securely stored in the GitHub repository settings. The workflow uses these secrets for authentication and access control. Below is a list of secrets required for the workflow:

| Secret Name     | Required | Description                                                                                                       |
| --------------- | -------- | ----------------------------------------------------------------------------------------------------------------- |
| `CODECOV_TOKEN` | Yes      | The token used to authenticate with the Codecov API. It is needed to securely upload coverage reports to Codecov. |

To add this secret to your repository:

1. Go to your repository's Settings.
2. Under Secrets and Variables, select Actions.
3. Add a new secret called CODECOV_TOKEN and paste your token value.

## Workflow Jobs

### 1. Code Analysis Job

- **Job Name**: `code-analysis`
- **Runs On**: The workflow will run on the operating system defined by the `OS_VERSION` input.

#### Steps:

1. **Prepare Repository**

   - **Action**: `actions/checkout@v3`
   - **Purpose**: This step checks out the repository to the workflow runner.

2. **Download Artifact**

   - **Action**: `actions/download-artifact@v4`
   - **Purpose**: Downloads the artifact containing the coverage data. The artifact name and path are defined by the inputs `ARTIFACT_NAME` and `ARTIFACT_PATH`.

3. **Run Code Analysis**
   - **Action**: `codecov/codecov-action@v3`
   - **Purpose**: Uploads the coverage results to CodeCov using the specified `CODECOV_TOKEN`. It also includes options to define the artifact directory, file, flags, and other settings:
     - `token`: The CodeCov token used for authentication.
     - `directory`: The path where the artifact is located.
     - `files`: The file containing the coverage results.
     - `flags`: A label for the type of tests (e.g., `unittests`).
     - `name`: The name of the repository, used for identification on CodeCov.
     - `fail_ci_if_error`: If set to `true`, it will fail the CI job if there is an error uploading the coverage results.

## Example Usage

```yaml
name: Main
on:
  push:
    branches:
      - master
jobs:
  code-quailty:
    name: Code Quality
    needs:
      - test
    uses: bayudwiyansatria/.github/.github/workflows/code-analysis-codecov.yml@master
    with:
      ARTIFACT_NAME: coverage
      ARTIFACT_PATH: dist/coverage
    secrets:
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```
