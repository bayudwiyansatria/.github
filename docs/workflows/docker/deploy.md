# Deploy Docker Workflow

This workflow is designed to deploy Docker containers using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                | Type   | Required | Default  | Description                          |
| ------------------- | ------ | -------- | -------- | ------------------------------------ |
| `DOCKERFILE_PATH`   | string | Yes      | N/A      | The path to the Dockerfile           |
| `IMAGE_NAME`        | string | Yes      | N/A      | The name of the Docker image         |
| `IMAGE_TAG`         | string | No       | `latest` | The tag for the Docker image         |
| `REGISTRY_URL`      | string | Yes      | N/A      | The URL of the Docker registry       |
| `REGISTRY_USERNAME` | string | Yes      | N/A      | The username for the Docker registry |
| `REGISTRY_PASSWORD` | string | Yes      | N/A      | The password for the Docker registry |

### Secrets

| **Secret**        | **Description**                              |
| ----------------- | -------------------------------------------- |
| `DOCKER_USERNAME` | Username for Docker registry authentication. |
| `DOCKER_PASSWORD` | Password for Docker registry authentication. |

## Jobs

### Deploy

This job handles the process of deploying the Docker image.

#### Steps

1. **Prepare Repository**: Checks out the repository.
2. **Login to Registry**: Logs in to the Docker registry.
3. **Build Docker Image**: Builds the Docker image.
4. **Push Docker Image**: Pushes the Docker image to the registry.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
