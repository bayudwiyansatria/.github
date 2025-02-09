# Build Dotnet Workflow

This workflow is designed to build .NET projects using GitHub Actions.

## Inputs

| Name            | Type   | Required | Default                | Description                      |
| --------------- | ------ | -------- | ---------------------- | -------------------------------- |
| DOTNET_VERSION  | string | No       | `6`                    | The version of .NET to use       |
| DOTNET_REGISTRY | string | No       | `nuget.pkg.github.com` | The registry to push packages to |
| NAME            | string | Yes      | N/A                    | The name of the project to build |

## Secrets

| Name              | Required | Description                                 |
| ----------------- | -------- | ------------------------------------------- |
| DOTNET_AUTH_TOKEN | Yes      | The token to authenticate with the registry |

## Jobs

### Build

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures .NET using a custom action.
3. **Build Package**: Builds the .NET project in release configuration.

## Example Usage

```yaml

```
