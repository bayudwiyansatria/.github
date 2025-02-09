# Docker Build GitHub Action Workflow

This GitHub Action workflow automates the process of building Docker images. It supports customizable configurations for Docker version, build command, registry, and more.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| **Parameter**          | **Description**                                                          | **Default Value**          |
| ---------------------- | ------------------------------------------------------------------------ | -------------------------- |
| `DOCKER_VERSION`       | Specifies the Docker version to use.                                     | `20.10`                    |
| `DOCKER_BUILD_COMMAND` | The command used for building the Docker image.                          | `docker build`             |
| `DOCKER_WORKING_DIR`   | The directory where the Docker context is located.                       | `.`                        |
| `DOCKER_FILE`          | The Dockerfile to use for building the image.                            | `Dockerfile`               |
| `DOCKER_REPOSITORY`    | The Docker repository to push the image to. default: `owners/repository` | `${{ github.repository }}` |
| `DOCKER_IMAGE_TAG`     | The tag for the Docker image. default: `short-sha`                       | `${{ github.sha }}`        |
| `DOCKER_REGISTRY`      | The Docker registry to push the image to.                                | `docker.pkg.github.com`    |
| `BUILD_ARTIFACT_NAME`  | Name of the build artifact.                                              | `target`                   |
| `BUILD_ARTIFACT_PATH`  | Path to the build artifact.                                              | `target`                   |

### Secrets

| **Secret**        | **Description**                              |
| ----------------- | -------------------------------------------- |
| `DOCKER_USERNAME` | Username for Docker registry authentication. |
| `DOCKER_PASSWORD` | Password for Docker registry authentication. |

## Jobs

### dockerized

This job handles the process of building and pushing the Docker image.

#### Steps

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Download Artifact**:
   - Downloads the specified build artifact using `actions/download-artifact@v4`.
3. **Configure Docker**:
   - Configures Docker using a custom action `bayudwiyansatria/.github/actions/configure-docker@master`.
4. **Build Package**:
   - Uses `docker/build-push-action@v6` to build the Docker image and push it to the specified registry with the configured tag.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml

```

## Notes
