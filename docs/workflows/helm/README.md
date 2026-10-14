# Helm workflows

The house provides [Build Helm](build.md) and [Release Helm](release.md) through reusable `workflow_call` workflows.

Build validates one chart per call: strict lint, a full render and a package, uploaded as the `<chart>-<version>.tgz`
artifact. Repository callers run it on feature and main pushes, usually as a matrix over their charts. Release
packages the chart at the house release version and publishes it to the target named by `HELM_REPOSITORY`:

| Target                               | `HELM_REPOSITORY`                  | Upload                     |
| ------------------------------------ | ---------------------------------- | -------------------------- |
| OCI registry, default GHCR           | `oci://ghcr.io/<owner>/charts`     | `helm push`                |
| Chart repository: Nexus, Artifactory | `https://<host>/repository/<repo>` | `PUT <url>/<package>`      |
| Chart repository: ChartMuseum        | `https://<host>`                   | `POST <url>/api/charts`    |

Both targets get the same version, immutability and notes checks. A repository that needs another target can publish
Build's artifact from its own job instead.

One version names a release: tag `v<version>`, image tag `<version>`, chart version, chart `appVersion` and
`docs/release-notes/<version>.md` with a `## Version <version>` heading. Nothing is overridden: the release commit
updates `Chart.yaml` and adds the notes together, and every check fails on a mismatch. A chart that deploys its own
image should default the image tag to `.Chart.AppVersion`, so each chart version deploys the image released with it.

`deploy-helm.yml` is a separate legacy workflow and is not part of this contract.

## Caller example

```yaml
charts:
  name: Chart ${{ matrix.chart }}
  needs:
    - secret-detection
  strategy:
    fail-fast: false
    matrix:
      chart:
        - example-crds
        - example
  uses: bayudwiyansatria/.github/.github/workflows/build-helm.yml@master
  with:
    HELM_CHART_NAME: ${{ matrix.chart }}
    KUBE_VERSION: "1.34.0"
    HELM_SET: watchNamespace=example
    ARTIFACT_ENABLED: false
```

See [Release Helm](release.md) for the publication callers.
