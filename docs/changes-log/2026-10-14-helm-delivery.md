# Helm delivery - 2026-10-14

## Changes and rationale

Rewrote `build-helm.yml` and `release-helm.yml`. The release workflow could not run: it was named after the deploy
workflow, ran `helm push` without a destination and read an undeclared secret.

Build validates a chart with strict lint, render and package, then uploads the package as `<chart>-<version>.tgz` by
default, as before, so existing callers that publish the artifact themselves, for example to Nexus, keep working. The
earlier `HELM_REPOSITORY`, `ARTIFACT_NAME` and `ARTIFACT_PATH` inputs and the `HELM_AUTH_TOKEN` secret are still
accepted but unused. Build exposes `CHART_VERSION` and `ARTIFACT_NAME` outputs for a downstream job.

Release follows the Go release contract: three numeric inputs, default-branch dispatch, immutable versions and a
version shared by tag, image tag and chart. Its target follows the `HELM_REPOSITORY` scheme. `oci://` pushes an OCI
artifact, by default to GHCR next to the images. `https://` uploads to a classic chart repository: `PUT` for Nexus and
Artifactory, `POST` to `api/charts` for ChartMuseum. Either way, an existing version is refused, and an HTTP upload
must appear in the repository index before the job succeeds. Release declares no permissions, so callers grant
`packages: write` only for GHCR. Charts reference their image by tag, defaulting to `.Chart.AppVersion`; the workflow
does not resolve or pin digests.

Versions are verified, never overridden. Release requires `Chart.yaml` `version` and `appVersion` to equal the release
version, checks the packaged metadata, and requires release notes with a `## Version <version>` heading. Build can
enforce the same alignment on every push and against the dispatched version before publication. Chart metadata is read
from top-level keys only, so maintainer names and dependency versions are never mistaken for the chart's own.
`release-go.yml` is unchanged and does not require the notes heading.

Remaining build behavior changes for existing callers: charts must pass `helm lint --strict` and render with only
`HELM_SET` values, and the chart directory must equal its `Chart.yaml` name. `deploy-helm.yml` is unchanged.

## Verification boundary

Local checks cover YAML parsing, shell syntax and caller inputs. The version and notes checks were exercised against a
scratch copy of agent-operator charts with matching, mismatched, drifted, dependency and copied-notes cases. The HTTP
path was exercised against a local authenticated mock repository: `PUT` and `POST` uploads, refusal of an existing
version and rejection of wrong credentials. No chart is pushed to a real repository during implementation. Shared
workflows must be published before callers can execute remotely.
