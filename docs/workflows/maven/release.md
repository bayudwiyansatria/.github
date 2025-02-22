# Release Maven Workflow

This workflow is designed to release Maven projects using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                  | Type   | Required | Default                | Description                                           |
| --------------------- | ------ | -------- | ---------------------- | ----------------------------------------------------- |
| `MAJOR_VERSION`       | string | Yes      | `0`                    | Major Version of the project                          |
| `MINOR_VERSION`       | string | Yes      | `0`                    | Minor Version of the project                          |
| `INCREMENTAL_VERSION` | string | No       | `1`                    | incremental Version of the project                    |
| `OS_VERSION`          | string | No       | `ubuntu-24.04`         | The operating system version for the workflow runtime |
| `JAVA_VERSION`        | string | No       | `11`                   | The version of Java to use                            |
| `MAVEN_REGISTRY`      | string | No       | `maven.pkg.github.com` | The registry to push packages to                      |
| `ARTIFACT_NAME`       | string | No       | `target`               | The name of the artifact to upload                    |
| `ARTIFACT_PATH`       | string | No       | `target`               | The path to the artifact to upload                    |
| `ARTIFACT_RETENTION`  | string | No       | `1`                    | The retention period for the artifact in days         |

### Secrets

| Name             | Required | Description                                    |
| ---------------- | -------- | ---------------------------------------------- |
| `MAVEN_USERNAME` | Yes      | The username to authenticate with the registry |
| `MAVEN_PASSWORD` | Yes      | The password to authenticate with the registry |
| `GPG_PASSPHRASE` | Yes      | The passphrase for GPG signing                 |
| `GITHUB_PAT`     | Yes      | The GitHub Personal Access Token               |

## Jobs

This job handles the process of release maven packages.

### Release

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Setup Maven**:
   - Configures Maven using a custom action.
3. **Deploy Package**:
   - Deploys the Maven package.
4. **Post Deployment**:
   - Updates the version and commits the changes.
5. **Reconfigure Java**:
   - Reconfigures Java and pushes the changes.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml
release:
  name: Release
  uses: bayudwiyansatria/.github/.github/workflows/release-github.yml@master
  with:
    MAJOR_VERSION: 1
    MINOR_VERSION: 0
    INCREMENTAL_VERSION: 0
    COVERAGE_ENABLED: true
    COVERAGE_NAME: coverage
    COVERAGE_PATH: dist/coverage
  secrets:
    GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}
```

## Notes
