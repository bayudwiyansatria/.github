# Build Maven Workflow

This workflow is designed to build Java projects using Maven with GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                 | Type   | Required | Default                                                               | Description                                   |
| -------------------- | ------ | -------- | --------------------------------------------------------------------- | --------------------------------------------- |
| `MAVEN_COMMAND`      | string | No       | `mvn --no-transfer-progress --file pom.xml clean package -DskipTests` | The command to build the project              |
| `WORKING_DIR`        | string | No       | `.`                                                                   | The working directory for the build command   |
| `OS_VERSION`         | string | No       | The operating system version for the workflow runtime.                | `ubuntu-24.04`                                |
| `JAVA_VERSION`       | string | No       | `11`                                                                  | The version of Java to use                    |
| `MAVEN_REGISTRY`     | string | No       | `maven.pkg.github.com`                                                | The registry to push packages to              |
| `ARTIFACT_NAME`      | string | No       | `target`                                                              | The name of the artifact to upload            |
| `ARTIFACT_PATH`      | string | No       | `target`                                                              | The path to the artifact to upload            |
| `ARTIFACT_RETENTION` | string | No       | `1`                                                                   | The retention period for the artifact in days |

## Secrets

| Name             | Required | Description                                    |
| ---------------- | -------- | ---------------------------------------------- |
| `MAVEN_USERNAME` | Yes      | The username to authenticate with the registry |
| `MAVEN_PASSWORD` | Yes      | The password to authenticate with the registry |

## Jobs

This job handles the process of building maven.

### Steps

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures Java using a custom action.
3. **Build Package**: Builds the Maven project, optionally skipping tests.
4. **Upload Artifact**: Uploads the built artifact.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```
