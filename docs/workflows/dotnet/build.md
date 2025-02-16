# Build .NET Workflow

This workflow is designed to build .NET projects using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name               | Type   | Required | Default                | Description                      |
| ------------------ | ------ | -------- | ---------------------- | -------------------------------- |
| `DOTNET_VERSION`   | string | No       | `6`                    | The version of .NET to use       |
| `DOTNET_REGISTRY ` | string | No       | `nuget.pkg.github.com` | The registry to push packages to |
| `NAME`             | string | Yes      | N/A                    | The name of the project to build |

### Secrets

| Name                | Required | Description                                 |
| ------------------- | -------- | ------------------------------------------- |
| `DOTNET_AUTH_TOKEN` | Yes      | The token to authenticate with the registry |

## Jobs

This job handles the process of building .NET.

### Steps

1. **Prepare Repository**: Checks out the repository.
2. **Setup Platform**: Configures .NET using a custom action.
3. **Build Package**: Builds the .NET project in release configuration.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
