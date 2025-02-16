# Test Java Maven Workflow

This workflow is designed to test Java Maven projects using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name              | Type    | Required | Default                | Description                |
| ----------------- | ------- | -------- | ---------------------- | -------------------------- |
| `JAVA_VERSION`    | string  | No       | `11`                   | The version of Java to use |
| `MAVEN_REGISTRY`  | string  | No       | `maven.pkg.github.com` | The Maven registry to use  |
| `MAVEN_SKIP_TEST` | boolean | No       | `false`                | Whether to skip tests      |

### Secrets

| Name             | Required | Description                                    |
| ---------------- | -------- | ---------------------------------------------- |
| `MAVEN_USERNAME` | Yes      | The username to authenticate with the registry |
| `MAVEN_PASSWORD` | Yes      | The password to authenticate with the registry |

## Jobs

This job handles the process of test maven packages.

### Steps

1. **Prepare Repository**: Checks out the repository.
2. **Setup Java**: Configures Java using a custom action.
3. **Run Tests**: Executes the Maven test goals.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
