# Docker Build Workflow

This GitHub Action workflow automates the process of building Docker images. It supports customizable configurations for docker version, build command, registry, and more.

## Workflow Configuration

The workflow is triggered manually using the `workflow_call` event, allowing for flexible inputs for different parameters.

### Input Parameters

| Name                | type    | required | Default                                    | Description                                                              |
| ------------------- | ------- | -------- | ------------------------------------------ | ------------------------------------------------------------------------ |
| `DOCKER_COMMAND`    | string  | No       | `docker build -t ${{ github.repository }}` | Not Applicable yet.                                                      |
| `DOCKER_REPOSITORY` | string  | No       | `${{ github.repository }}`                 | The Docker repository to push the image to. Default: `owners/repository` |
| `DOCKER_FILE`       | string  | No       | `Dockerfile`                               | The Dockerfile to use for building the image.                            |
| `DOCKER_IMAGE_TAG`  | string  | No       | `${{ github.sha }}`                        | The tag for the Docker image. Default: `short-sha`                       |
| `DOCKER_BUILD_ARG`  | string  | No       | `null`                                     | Specifies the Docker build argument.                                     |
| `WORKING_DIR`       | string  | No       | `.`                                        | The directory where the Docker context is located.                       |
| `PUSH_ENABLED`      | boolean | No       | `false`                                    | Whether to push the Docker image to the registry.                        |
| `SBOM_ENABLED`      | boolean | No       | `false`                                    | Whether to generate the Docker SBOM (Software Bill of Materials).        |
| `ARTIFACT_ENABLED`  | boolean | No       | `false`                                    | Whether to enable artifact download.                                     |
| `OS_VERSION`        | string  | No       | `ubuntu-24.04`                             | The operating system version for the workflow runtime.                   |
| `DOCKER_VERSION`    | string  | No       | `20.10`                                    | Specifies the Docker version to use.                                     |
| `DOCKER_REGISTRY`   | string  | No       | `docker.pkg.github.com`                    | The Docker registry to push the image to.                                |
| `ARTIFACT_NAME`     | string  | No       | `target`                                   | Name of the build artifact.                                              |
| `ARTIFACT_PATH`     | string  | No       | `target`                                   | Path to the build artifact.                                              |

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
   - Configures Docker using a custom action.
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
