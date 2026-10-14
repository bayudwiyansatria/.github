# Release Helm Workflow

[`release-helm.yml`](../../../.github/workflows/release-helm.yml) publishes one chart at the house three-component
release version, to an OCI registry or a classic chart repository.

## Inputs and secrets

| Input                 | Default                        | Contract                                            |
| --------------------- | ------------------------------ | --------------------------------------------------- |
| `MAJOR_VERSION`       | Required                       | Nonnegative integer without leading zeroes          |
| `MINOR_VERSION`       | Required                       | Nonnegative integer without leading zeroes          |
| `INCREMENTAL_VERSION` | Required                       | Nonnegative integer without leading zeroes          |
| `HELM_CHART_NAME`     | Repository name                | Directory under `WORKING_DIR`                       |
| `WORKING_DIR`         | `./charts`                     | Repository-relative charts directory                |
| `HELM_SET`            | Empty                          | `--set` values for the release lint                 |
| `KUBE_VERSION`        | Empty                          | Kubernetes version for the release lint             |
| `RELEASE_NOTES_DIR`   | `docs/release-notes`           | Committed `<version>.md`                            |
| `HELM_REPOSITORY`     | `oci://ghcr.io/<owner>/charts` | `oci://` reference or `https://` chart repository   |
| `HELM_UPLOAD_METHOD`  | `PUT`                          | `https://` only: `PUT` or `POST`                    |
| `OS_VERSION`          | `ubuntu-24.04`                 | Runner image                                        |
| `HELM_VERSION`        | `latest`                       | Helm version for `azure/setup-helm`                 |

Secrets `HELM_USERNAME` and `HELM_PASSWORD` are required for both targets. The workflow declares no permissions and
inherits the caller job's grant.

## Targets

`HELM_REPOSITORY` decides how the chart is published:

- `oci://<registry>/<namespace>` logs in with `helm registry login` and runs `helm push`. The chart becomes
  `oci://<registry>/<namespace>/<chart>:<version>`. References must be lowercase.
- `https://<url>` adds the repository with `helm repo add`, which also proves the URL serves an `index.yaml` and the
  credentials can read it. `PUT` uploads to `<url>/<chart>-<version>.tgz`, as Nexus and Artifactory expect. `POST`
  sends the package to `<url>/api/charts`, as ChartMuseum expects. The job then waits up to 30 seconds for the
  repository index to list the new version. Plain `http://` is refused, because credentials would travel unencrypted.

## Validation and publication

1. The job requires default-branch `workflow_dispatch` and validates version syntax, chart name and repository URL.
2. It requires tracked `<RELEASE_NOTES_DIR>/<version>.md` with the heading `## Version <version>`.
3. It requires `Chart.yaml` `version` and `appVersion` to both equal the release version. Nothing is overridden.
4. It refuses a chart version that already exists in the target. Published versions are immutable, even where the
   server would allow a redeploy.
5. It lints and packages, then confirms the package's own `version` and `appVersion` equal the release version.
6. It publishes and records the repository, chart digest and install command in the job summary.

Callers should also run [Build Helm](build.md) with `EXPECTED_VERSION` before any publication job, so a mismatch fails
before the GitHub release or image promotion.

The job never builds, retags or inspects images. A chart whose image tag defaults to `.Chart.AppVersion` deploys the
image released at the same version, so publish charts after that image tag exists. Charts are not signed; signatures
and provenance are a separate decision.

## Thin callers

Run chart publication after the Go release and version image promotion. GHCR uses the workflow token:

```yaml
chart-release:
  name: Chart ${{ matrix.chart }}
  needs:
    - docker-release
  permissions:
    contents: read
    packages: write
  strategy:
    fail-fast: false
    matrix:
      chart:
        - example-crds
        - example
  uses: bayudwiyansatria/.github/.github/workflows/release-helm.yml@master
  with:
    MAJOR_VERSION: ${{ inputs.MAJOR_VERSION }}
    MINOR_VERSION: ${{ inputs.MINOR_VERSION }}
    INCREMENTAL_VERSION: ${{ inputs.INCREMENTAL_VERSION }}
    HELM_CHART_NAME: ${{ matrix.chart }}
  secrets:
    HELM_USERNAME: ${{ github.actor }}
    HELM_PASSWORD: ${{ secrets.GITHUB_TOKEN }}
```

A chart repository uses its own credentials and needs no package permission:

```yaml
chart-release:
  name: Chart ${{ matrix.chart }}
  needs:
    - docker-release
  permissions:
    contents: read
  uses: bayudwiyansatria/.github/.github/workflows/release-helm.yml@master
  with:
    MAJOR_VERSION: ${{ inputs.MAJOR_VERSION }}
    MINOR_VERSION: ${{ inputs.MINOR_VERSION }}
    INCREMENTAL_VERSION: ${{ inputs.INCREMENTAL_VERSION }}
    HELM_CHART_NAME: example
    HELM_REPOSITORY: https://nexus.example.com/repository/helm-hosted
  secrets:
    HELM_USERNAME: ${{ secrets.NEXUS_USERNAME }}
    HELM_PASSWORD: ${{ secrets.NEXUS_PASSWORD }}
```

Install from OCI with `helm install <release> oci://ghcr.io/<owner>/charts/<chart> --version <version>`, or from a
chart repository with `helm repo add <name> <url>` and `helm install <release> <name>/<chart> --version <version>`.
Registry visibility and pull credentials are an explicit repository setting. Publication behavior is established only
by an authorized Actions dispatch.
