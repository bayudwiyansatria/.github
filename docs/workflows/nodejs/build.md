# Build Node Workflow

This workflow is designed to build Node.js projects using npm with GitHub Actions.

## Inputs

| Name               | Type   | Required | Default                      | Description                                   |
| ------------------ | ------ | -------- | ---------------------------- | --------------------------------------------- |
| NODE_VERSION       | string | No       | `16`                         | The version of Node.js to use                 |
| NODE_BUILD_COMMAND | string | No       | `npm run build --if-present` | The command to build the project              |
| NODE_WORKING_DIR   | string | No       | `.`                          | The working directory for the build command   |
| NPM_REGISTRY       | string | No       | `https://npm.pkg.github.com` | The registry to push packages to              |
| ARTIFACT_NAME      | string | No       | `lib`                        | The name of the artifact to upload            |
| ARTIFACT_PATH      | string | No       | `lib`                        | The path to the artifact to upload            |
| ARTIFACT_RETENTION | string | No       | `1`                          | The retention period for the artifact in days |

## Secrets

| Name           | Required | Description                                 |
| -------------- | -------- | ------------------------------------------- |
| NPM_AUTH_TOKEN | Yes      | The token to authenticate with the registry |

## Jobs

### Build

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures Node.js using a custom action.
3. **Installing Package**: Installs the Node.js packages.
4. **Build Package**: Builds the Node.js project.
5. **Upload Artifact**: Uploads the built artifact.

## Example Usage

```yaml

```
