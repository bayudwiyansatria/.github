# Build Maven Workflow

This workflow is designed to build Java projects using Maven with GitHub Actions.

## Inputs

| Name               | Type    | Required | Default                | Description                                   |
| ------------------ | ------- | -------- | ---------------------- | --------------------------------------------- |
| JAVA_VERSION       | string  | No       | `11`                   | The version of Java to use                    |
| MAVEN_REGISTRY     | string  | No       | `maven.pkg.github.com` | The registry to push packages to              |
| MAVEN_SKIP_TEST    | boolean | No       | `false`                | Whether to skip tests during build            |
| ARTIFACT_NAME      | string  | No       | `target`               | The name of the artifact to upload            |
| ARTIFACT_PATH      | string  | No       | `target`               | The path to the artifact to upload            |
| ARTIFACT_RETENTION | string  | No       | `1`                    | The retention period for the artifact in days |

## Secrets

| Name           | Required | Description                                    |
| -------------- | -------- | ---------------------------------------------- |
| MAVEN_USERNAME | Yes      | The username to authenticate with the registry |
| MAVEN_PASSWORD | Yes      | The password to authenticate with the registry |

## Jobs

### Build

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures Java using a custom action.
3. **Build Package**: Builds the Maven project, optionally skipping tests.
4. **Upload Artifact**: Uploads the built artifact.

## Example Usage

```yaml

```
