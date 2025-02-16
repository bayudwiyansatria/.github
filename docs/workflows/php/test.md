# Test PHP Workflow Documentation

## Overview

This workflow is designed to automate the testing and building of PHP projects using GitHub Actions. It integrates PHP package installation, build commands, unit tests, and artifact handling.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                      | Type   | Required | Default                 | Description                                            |
| ------------------------- | ------ | -------- | ----------------------- | ------------------------------------------------------ |
| `PHP_COMMAND`             | string | No       | `composer run test`     | The command to run unit tests.                         |
| `PHP_BUILD_COMMAND`       | string | No       | `composer run build:ci` | The command to build the PHP package.                  |
| `WORKING_DIR`             | string | No       | `.`                     | The directory where the PHP project is located.        |
| `OS_VERSION`              | string | No       | `ubuntu-24.04`          | The operating system version for the workflow.         |
| `PHP_VERSION`             | string | No       | `8.4`                   | The PHP version to use.                                |
| `BUILD_ARTIFACT_NAME`     | string | No       | `build`                 | The name of the build artifact.                        |
| `BUILD_ARTIFACT_PATH`     | string | No       | `build`                 | The path where the build artifact is located.          |
| `TEST_ARTIFACT_NAME`      | string | No       | `coverage`              | The name of the test artifact (e.g., coverage report). |
| `TEST_ARTIFACT_PATH`      | string | No       | `dist/coverage`         | The path to the test artifact.                         |
| `TEST_ARTIFACT_RETENTION` | string | No       | `1`                     | The retention period for test artifacts.               |

### Secrets

Currently, there are no required secrets for this workflow.

## Jobs

### Test Job

This job runs the PHP tests on the specified operating system and PHP version.

#### Steps:

1. **Prepare Repository**: Checkout the code using `actions/checkout@v3`.
2. **Download Build Artifact**: Download the build artifact from a previous step using `actions/download-artifact@v4`.
3. **Setup PHP**: Configure the PHP environment with the specified version using `bayudwiyansatria/.github/actions/configure-php@master`.
4. **Install PHP Packages**: Install PHP dependencies using Composer (`composer install`).
5. **Build PHP Packages**: Run the build command (`composer run build:ci`).
6. **Run Unit Test**: Execute the unit tests using the provided PHP command (`composer run test`).
7. **Upload Artifact**: Archive the test coverage results as an artifact for later use.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
