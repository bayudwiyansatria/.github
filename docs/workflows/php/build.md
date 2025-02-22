# Build PHP Workflow

This workflow is designed to build PHP projects using Composer with GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                 | Type   | Required | Default              | Description                                            |
| -------------------- | ------ | -------- | -------------------- | ------------------------------------------------------ |
| `PHP_COMMAND`        | string | No       | `composer run build` | The command to build the project                       |
| `WORKING_DIR`        | string | No       | `.`                  | The working directory for the build command            |
| `OS_VERSION`         | string | No       | `ubuntu-24.04`       | The operating system version for the workflow runtime. |
| `PHP_VERSION`        | string | No       | `8.4`                | The version of PHP to use                              |
| `ARTIFACT_NAME`      | string | No       | `build`              | The name of the artifact to upload                     |
| `ARTIFACT_PATH`      | string | No       | `.`                  | The path to the artifact to upload                     |
| `ARTIFACT_RETENTION` | string | No       | `1`                  | The retention period for the artifact in days          |

### Secrets

This workflow does not require any secrets.

## Jobs

This job handles the process of building php.

### Steps

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Setup Platform**:
   - Configures PHP and Node.js using custom actions.
3. **Installing Package**:
   - Installs the PHP packages.
4. **Build Package**:
   - Builds the PHP project.
5. **Upload Artifact**:
   - Uploads the built artifact.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
