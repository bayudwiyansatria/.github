# Test Java Maven Workflow

This workflow is designed to test Java Maven projects using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                      | Type   | Required | Default                                                        | Description                                            |
| ------------------------- | ------ | -------- | -------------------------------------------------------------- | ------------------------------------------------------ |
| `MAVEN_COMMAND`           | string | No       | `mvn --no-transfer-progress --file pom.xml clean test package` | The command to build the project                       |
| `WORKING_DIR`             | string | No       | `.`                                                            | The working directory for the build command            |
| `OS_VERSION`              | string | No       | `ubuntu-24.04`                                                 | The operating system version for the workflow runtime. |
| `JAVA_VERSION`            | string | No       | `11`                                                           | The version of Java to use                             |
| `MAVEN_REGISTRY`          | string | No       | `maven.pkg.github.com`                                         | The registry to push packages to                       |
| `BUILD_ARTIFACT_NAME`     | string | No       | `target`                                                       | The name of the build artifact.                        |
| `BUILD_ARTIFACT_PATH`     | string | No       | `target`                                                       | The path where the build artifact is located.          |
| `TEST_ARTIFACT_NAME`      | string | No       | `coverage`                                                     | The name of the test artifact (e.g., coverage report). |
| `TEST_ARTIFACT_PATH`      | string | No       | `target/site/jacoco`                                           | The path to the test artifact.                         |
| `TEST_ARTIFACT_RETENTION` | string | No       | `1`                                                            | The retention period for test artifacts.               |

### Secrets

| Name             | Required | Description                                    |
| ---------------- | -------- | ---------------------------------------------- |
| `MAVEN_USERNAME` | Yes      | The username to authenticate with the registry |
| `MAVEN_PASSWORD` | Yes      | The password to authenticate with the registry |

## Jobs

This job handles the process of test maven packages.

### Steps

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Setup Java**:
   - Configures Java using a custom action.
3. **Run Tests**:
   - Executes the Maven test goals.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml
test:
  name: Test
  uses: bayudwiyansatria/.github/.github/workflows/test-java-maven.yml@master
  with:
    JAVA_VERSION: 21
  secrets:
    MAVEN_USERNAME: ${{ secrets.MAVEN_USERNAME }}
    MAVEN_PASSWORD: ${{ secrets.MAVEN_PASSWORD }}
```

## Notes
