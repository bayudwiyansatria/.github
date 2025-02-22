# Deploy Docker Workflow

This workflow is designed to deploy Docker containers using GitHub Actions.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                           | Type    | Required | Default                    | Description                                           |
| ------------------------------ | ------- | -------- | -------------------------- | ----------------------------------------------------- |
| `DOCKER_REPOSITORY`            | string  | No       | `${{ github.repository }}` | The Docker repository to push the image to.           |
| `DOCKER_IMAGE_TAG`             | string  | No       | `${{ github.sha }}`        | The tag for the Docker image.                         |
| `DOCKER_DEPLOY_TAG`            | string  | No       | N/A                        | The tag for deploying the Docker image.               |
| `WORKING_DIR`                  | string  | No       | `.`                        | The directory where the Docker context is located.    |
| `DOCKER_TAG_SHORT_SHA_ENABLED` | boolean | No       | `false`                    | Whether to tag the image with the short SHA.          |
| `DOCKER_TAG_REF_ENABLED`       | boolean | No       | `false`                    | Whether to tag the image with the branch or tag name. |
| `DOCKER_TAG_LATEST_ENABLED`    | boolean | No       | `false`                    | Whether to tag the image with `latest`.               |
| `OS_VERSION`                   | string  | No       | `ubuntu-24.04`             | The OS version for the workflow runtime.              |
| `DOCKER_VERSION`               | string  | No       | `20.10`                    | The Docker version to use.                            |
| `DOCKER_REGISTRY`              | string  | No       | `docker.pkg.github.com`    | The Docker registry to push the image to.             |

### Secrets

| **Secret**        | required | **Description**                              |
| ----------------- | -------- | -------------------------------------------- |
| `DOCKER_USERNAME` | yes      | Username for Docker registry authentication. |
| `DOCKER_PASSWORD` | yes      | Password for Docker registry authentication. |

## Jobs

### Deploy

This job handles the process of deploying the Docker image.

#### Steps

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Login to Registry**:
   - Logs in to the Docker registry.
3. **Configure Docker**:
   - Configures Docker using a custom action `bayudwiyansatria/.github/actions/configure-docker@master`.
4. **Build Docker Image**:
   - Build the Docker image with the configured tag.
5. **Push Docker Image**:
   - Pushes the Docker image to the registry.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml
jobs:
  docker:
  name: Build
  uses: bayudwiyansatria/.github/.github/workflows/deploy-docker.yml@master
  with:
    DOCKER_IMAGE_TAG: latest
    DOCKER_TAG_SHORT_SHA_ENABLED: true
  secrets:
    DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
    DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

## Notes
