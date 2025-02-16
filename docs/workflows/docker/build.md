# Docker Build GitHub Action Workflow

This GitHub Action workflow automates the process of building Docker images. It supports customizable configurations for docker version, build command, registry, and more.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| **Parameter**       | type    | required | **Description**                                                          | **Default Value**                            |
| ------------------- | ------- | -------- | ------------------------------------------------------------------------ | -------------------------------------------- |
| `DOCKER_REPOSITORY` | string  | No       | The Docker repository to push the image to. Default: `owners/repository` | `${{ github.repository }}`                   |
| `DOCKER_FILE`       | string  | No       | The Dockerfile to use for building the image.                            | `Dockerfile`                                 |
| `DOCKER_IMAGE_TAG`  | string  | No       | The tag for the Docker image. Default: `short-sha`                       | `${{ github.sha }}`                          |
| `DOCKER_BUILD_ARG`  | string  | No       | Specifies the Docker build argument.                                     | `null`                                       |
| `DOCKER_COMMAND`    | string  | No       | Not Applicable yet.                                                      | `docker build -t ${{ github.repository }} .` |
| `WORKING_DIR`       | string  | No       | The directory where the Docker context is located.                       | `.`                                          |
| `PUSH_ENABLED`      | boolean | No       | Whether to push the Docker image to the registry.                        | `false`                                      |
| `SBOM_ENABLED`      | boolean | No       | Whether to generate the Docker SBOM (Software Bill of Materials).        | `false`                                      |
| `ARTIFACT_ENABLED`  | boolean | No       | Whether to enable artifact download.                                     | `false`                                      |
| `OS_VERSION`        | string  | No       | The operating system version for the workflow runtime.                   | `ubuntu-24.04`                               |
| `DOCKER_VERSION`    | string  | No       | Specifies the Docker version to use.                                     | `20.10`                                      |
| `DOCKER_REGISTRY`   | string  | No       | The Docker registry to push the image to.                                | `docker.pkg.github.com`                      |
| `ARTIFACT_NAME`     | string  | No       | Name of the build artifact.                                              | `target`                                     |
| `ARTIFACT_PATH`     | string  | No       | Path to the build artifact.                                              | `target`                                     |

### Secrets

| **Secret**        | required | **Description**                              |
| ----------------- | -------- | -------------------------------------------- |
| `DOCKER_USERNAME` | yes      | Username for Docker registry authentication. |
| `DOCKER_PASSWORD` | yes      | Password for Docker registry authentication. |

## Jobs

This job handles the process of building and pushing the Docker image.

#### Steps

1. **Prepare Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Download Artifact** (optional):
   - Downloads the specified build artifact using `actions/download-artifact@v4`.
3. **Configure Docker**:
   - Configures Docker using a custom action `bayudwiyansatria/.github/actions/configure-docker@master`.
4. **Build Package**:
   - Build the Docker image with the configured tag.
5. **Push Package** (optional):
   - Push the Docker image to the specified registry with the configured tag.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system. Can be overrided by specify `OS_VERSION`.

## Example Usage

```yaml
jobs:
  docker:
  name: Build
  uses: bayudwiyansatria/.github/.github/workflows/build-docker.yml@master
  with:
    DOCKER_REGISTRY: docker.io
    DOCKER_FILE: Dockerfile
    DOCKER_IMAGE_TAG: latest
    DOCKER_PUSH_ENABLED: true
    ARTIFACT_ENABLED: false
  secrets:
    DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
    DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

## Notes

This workflow provides a flexible and customizable way to build and push Docker images using GitHub Actions. Adjust the input parameters and secrets as needed for your specific use case.

This workflow contain contextual github variables. Please refer to: [GitHub](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs).
