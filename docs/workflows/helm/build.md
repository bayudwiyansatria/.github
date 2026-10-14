# Build Helm Workflow

[`build-helm.yml`](../../../.github/workflows/build-helm.yml) validates and packages one chart. It never publishes.

## Inputs

| Input                     | Default         | Contract                                                         |
| ------------------------- | --------------- | ---------------------------------------------------------------- |
| `HELM_CHART_NAME`         | Repository name | Directory under `WORKING_DIR`; must equal `name` in `Chart.yaml` |
| `WORKING_DIR`             | `./charts`      | Repository-relative charts directory                             |
| `HELM_SET`                | Empty           | Comma-separated `--set` values for required chart values         |
| `KUBE_VERSION`            | Empty           | Kubernetes version for `kubeVersion` checks                      |
| `REQUIRE_ALIGNED_VERSION` | `false`         | Fail unless `appVersion` equals `version`                        |
| `EXPECTED_VERSION`        | Empty           | Fail unless `version` and `appVersion` both equal it             |
| `OS_VERSION`              | `ubuntu-24.04`  | Runner image                                                     |
| `HELM_VERSION`            | `latest`        | Helm version for `azure/setup-helm`; pin for reproducible builds |
| `ARTIFACT_ENABLED`        | `true`          | Upload the package as the artifact `<chart>-<version>.tgz`       |
| `ARTIFACT_RETENTION`      | `1`             | Artifact retention in days                                       |

No secret is required. The job has read-only repository permission.

`HELM_REPOSITORY`, `ARTIFACT_NAME`, `ARTIFACT_PATH` and the `HELM_AUTH_TOKEN` secret are deprecated. They are still
accepted, so existing callers keep working, but they have no effect.

## Outputs

| Output          | Value                                                         |
| --------------- | ------------------------------------------------------------- |
| `CHART_VERSION` | `version` from `Chart.yaml`                                   |
| `ARTIFACT_NAME` | `<chart>-<version>.tgz` when `ARTIFACT_ENABLED`, empty otherwise |

## Behavior

The job checks the chart name, path and requested version alignment, then runs `helm lint --strict`, `helm template`
and `helm package` with the same `--kube-version` and `--set` arguments. Any template error, schema violation or failed
`required` value fails the build. Chart metadata is read from top-level `Chart.yaml` keys only.

Release callers pass `EXPECTED_VERSION` with the dispatched version, so a chart that does not name the release fails
before any publication job runs. Feature and Main callers set `REQUIRE_ALIGNED_VERSION` to catch drift on every push.

The artifact holds only the package, at its root. A job that publishes it elsewhere downloads it by the
`ARTIFACT_NAME` output:

```yaml
publish:
  needs:
    - chart
  runs-on: ubuntu-24.04
  steps:
    - uses: actions/download-artifact@v7
      with:
        name: ${{ needs.chart.outputs.ARTIFACT_NAME }}
```

`HELM_SET` supplies example values only for validation. It must not contain credentials, because inputs appear in logs.
