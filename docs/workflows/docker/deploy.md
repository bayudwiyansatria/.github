# Deploy Docker Workflow

This workflow is designed to deploy Docker containers using GitHub Actions.

## Inputs

| Name              | Type   | Required | Default  | Description                          |
| ----------------- | ------ | -------- | -------- | ------------------------------------ |
| DOCKERFILE_PATH   | string | Yes      | N/A      | The path to the Dockerfile           |
| IMAGE_NAME        | string | Yes      | N/A      | The name of the Docker image         |
| IMAGE_TAG         | string | No       | `latest` | The tag for the Docker image         |
| REGISTRY_URL      | string | Yes      | N/A      | The URL of the Docker registry       |
| REGISTRY_USERNAME | string | Yes      | N/A      | The username for the Docker registry |
| REGISTRY_PASSWORD | string | Yes      | N/A      | The password for the Docker registry |

## Secrets

| Name              | Required | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| REGISTRY_PASSWORD | Yes      | The password to authenticate with the registry |

## Jobs

### Deploy

Runs on `ubuntu-24.04` and performs the following steps:

1. **Prepare Repository**: Checks out the repository.
2. **Login to Registry**: Logs in to the Docker registry.
3. **Build Docker Image**: Builds the Docker image.
4. **Push Docker Image**: Pushes the Docker image to the registry.

## Example Usage

```yaml

```
