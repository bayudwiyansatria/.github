# Test Node Workflow

This workflow is designed to test Node.js projects using npm with GitHub Actions.

## Inputs

| Name                | Type   | Required | Default                      | Description                                   |
| ------------------- | ------ | -------- | ---------------------------- | --------------------------------------------- |
| NODE_VERSION        | string | No       | `16`                         | The version of Node.js to use                 |
| NODE_BUILD_COMMAND  | string | No       | `npm run build --if-present` | The command to build the project              |
| NODE_TEST_COMMAND   | string | No       | `npm test`                   | The command to run tests                      |
| NODE_WORKING_DIR    | string | No       | `.`                          | The working directory for the commands        |
| BUILD_ARTIFACT_NAME | string | No       | `lib`                        | The name of the build artifact                |
| BUILD_ARTIFACT_PATH | string | No       | `lib`                        | The path to the build artifact                |
| ARTIFACT_REPOSITORY | string | Yes      | N/A                          | The repository to store artifacts             |
| ARTIFACT_NAME       | string | No       | `coverage`                   | The name of the test coverage artifact        |
| ARTIFACT_PATH       | string | No       | `dist/coverage`              | The path to the test coverage artifact        |
| ARTIFACT_RETENTION  | string | No       | `1`                          | The retention period for the artifact in days |

## Secrets

| Name           | Required | Description                                 |
| -------------- | -------- | ------------------------------------------- |
| NPM_AUTH_TOKEN | Yes      | The token to authenticate with the registry |
| CODECOV_TOKEN  | Yes      | The token for Codecov                       |

## Jobs

### Test

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Node**: Configures Node.js using a custom action.
3. **Install Dependencies**: Installs the Node.js dependencies.
4. **Run Tests**: Executes the test command.
5. **Upload Coverage**: Uploads the test coverage to Codecov.
6. **Upload Artifact**: Uploads the build and coverage artifacts.

## Example Usage

```yaml

```
