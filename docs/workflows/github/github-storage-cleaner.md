# GitHub Storage Cleaner Workflow

This workflow is designed to automate the process of cleaning up storage in a GitHub repository by removing old artifacts and other unnecessary files.

## Workflow Configuration

The workflow is triggered on a schedule or manually to ensure that the repository storage is kept clean and within limits.

### Input Parameters

| Name             | Type   | Required | Default | Description                            |
| ---------------- | ------ | -------- | ------- | -------------------------------------- |
| `retention-days` | string | Yes      | `30`    | The number of days to retain artifacts |
| `artifact-name`  | string | No       | `*`     | The name of the artifacts to clean up  |

## Secrets

| Name           | Required | Description                                          |
| -------------- | -------- | ---------------------------------------------------- |
| `GITHUB_TOKEN` | Yes      | The GitHub token to authenticate with the repository |

## Jobs

### Clean Storage

This job handles the process of cleaning up storage in the GitHub repository.

#### Steps

1. **Checkout Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **List Artifacts**:
   - Lists the artifacts in the repository.
3. **Delete Old Artifacts**:
   - Deletes artifacts older than the specified retention period.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml
name: GitHub Storage Cleaner

on:
  schedule:
    - cron: "0 0 * * 0" # Runs weekly
  workflow_dispatch:

jobs:
  clean-storage:
    name: Clean Storage
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: List Artifacts
        run: |
          echo "Listing artifacts..."
          gh api -X GET /repos/${{ github.repository }}/actions/artifacts

      - name: Delete Old Artifacts
        run: |
          echo "Deleting old artifacts..."
          gh api -X DELETE /repos/${{ github.repository }}/actions/artifacts --jq '.artifacts[] | select(.created_at < now - ${{ inputs.retention-days }} * 86400) | .id' | xargs -I {} gh api -X DELETE /repos/${{ github.repository }}/actions/artifacts/{}
```
