# Test Java Maven Workflow

This workflow is designed to test Java Maven projects using GitHub Actions.

## Inputs

| Name            | Type    | Required | Default                | Description                |
| --------------- | ------- | -------- | ---------------------- | -------------------------- |
| JAVA_VERSION    | string  | No       | `11`                   | The version of Java to use |
| MAVEN_REGISTRY  | string  | No       | `maven.pkg.github.com` | The Maven registry to use  |
| MAVEN_SKIP_TEST | boolean | No       | `false`                | Whether to skip tests      |

## Secrets

| Name           | Required | Description                                    |
| -------------- | -------- | ---------------------------------------------- |
| MAVEN_USERNAME | Yes      | The username to authenticate with the registry |
| MAVEN_PASSWORD | Yes      | The password to authenticate with the registry |

## Jobs

### Test

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Java**: Configures Java using a custom action.
3. **Run Tests**: Executes the Maven test goals.

## Example Usage

```yaml

```
