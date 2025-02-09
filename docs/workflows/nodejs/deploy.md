# Deploy Maven Workflow

This workflow is designed to deploy Maven projects using GitHub Actions.

## Inputs

| Name              | Type   | Required | Default | Description                         |
| ----------------- | ------ | -------- | ------- | ----------------------------------- |
| MAVEN_SETTINGS    | string | Yes      | N/A     | The path to the Maven settings file |
| MAVEN_GOALS       | string | Yes      | N/A     | The Maven goals to execute          |
| MAVEN_OPTIONS     | string | No       | `-B`    | Additional options for Maven        |
| REGISTRY_URL      | string | Yes      | N/A     | The URL of the Maven registry       |
| REGISTRY_USERNAME | string | Yes      | N/A     | The username for the Maven registry |
| REGISTRY_PASSWORD | string | Yes      | N/A     | The password for the Maven registry |

## Secrets

| Name              | Required | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| REGISTRY_PASSWORD | Yes      | The password to authenticate with the registry |

## Jobs

### Deploy

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Maven**: Configures Maven using a custom action.
3. **Deploy Package**: Deploys the Maven package.

## Example Usage

```yaml

```
