# Release Maven Workflow

This workflow is designed to release Maven projects using GitHub Actions.

## Inputs

| Name              | Type   | Required | Default | Description                         |
| ----------------- | ------ | -------- | ------- | ----------------------------------- |
| MAVEN_SETTINGS    | string | Yes      | N/A     | The path to the Maven settings file |
| MAVEN_GOALS       | string | Yes      | N/A     | The Maven goals to execute          |
| MAVEN_OPTIONS     | string | No       | `-B`    | Additional options for Maven        |
| REGISTRY_URL      | string | Yes      | N/A     | The URL of the Maven registry       |
| REGISTRY_USERNAME | string | Yes      | N/A     | The username for the Maven registry |
| REGISTRY_PASSWORD | string | Yes      | N/A     | The password for the Maven registry |
| GPG_PASSPHRASE    | string | Yes      | N/A     | The passphrase for GPG signing      |

## Secrets

| Name           | Required | Description                                    |
| -------------- | -------- | ---------------------------------------------- |
| MAVEN_USERNAME | Yes      | The username to authenticate with the registry |
| MAVEN_PASSWORD | Yes      | The password to authenticate with the registry |
| GPG_PASSPHRASE | Yes      | The passphrase for GPG signing                 |
| GITHUB_PAT     | Yes      | The GitHub Personal Access Token               |

## Jobs

### Release

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Maven**: Configures Maven using a custom action.
3. **Deploy Package**: Deploys the Maven package.
4. **Post Deployment**: Updates the version and commits the changes.
5. **Reconfigure Java**: Reconfigures Java and pushes the changes.

## Example Usage

```yaml

```
