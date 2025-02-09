# Build PHP Workflow

This workflow is designed to build PHP projects using Composer with GitHub Actions.

## Inputs

| Name                     | Type   | Required | Default              | Description                                   |
| ------------------------ | ------ | -------- | -------------------- | --------------------------------------------- |
| NODE_VERSION             | string | No       | `16`                 | The version of Node.js to use                 |
| PHP_VERSION              | string | No       | `8.4`                | The version of PHP to use                     |
| PHP_BUILD_COMMAND        | string | No       | `composer run build` | The command to build the project              |
| PHP_WORKING_DIR          | string | No       | `.`                  | The working directory for the build command   |
| BUILD_ARTIFACT_NAME      | string | No       | `build`              | The name of the artifact to upload            |
| BUILD_ARTIFACT_PATH      | string | No       | `.`                  | The path to the artifact to upload            |
| BUILD_ARTIFACT_RETENTION | string | No       | `1`                  | The retention period for the artifact in days |

## Secrets

This workflow does not require any secrets.

## Jobs

### Build

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures PHP and Node.js using custom actions.
3. **Installing Package**: Installs the PHP packages.
4. **Build Package**: Builds the PHP project.
5. **Upload Artifact**: Uploads the built artifact.

## Example Usage

```yaml

```
