# Go workflows

The house provides [Build Go](build.md), [Test Go](test.md) and [Release Go](release.md) through reusable `workflow_call`
workflows. Repository
callers own triggers, branch filters, runtime selection, commands, artifacts and secret-detection dependencies.

Build and Test use [Configure Go](../../actions/configure-go.md), read-only permissions and no required secrets.
Tests compile their own packages from source and can run alongside the binary build after the secret scan.
Release requires repository write permission and an explicit token mapping. It packages the current run's Build
artifact after successful validation. See [the release contract](release.md).

## Caller example

```yaml
name: Main
on:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  secret-detection:
    uses: bayudwiyansatria/.github/.github/workflows/code-analysis-secret.yml@master
    with:
      SCAN_MODE: dir
  build:
    needs: secret-detection
    uses: bayudwiyansatria/.github/.github/workflows/build-go.yml@master
    with:
      GO_VERSION: "1.26.x"
      GO_COMMAND: go build -trimpath -o bin/server ./cmd/server
      ARTIFACT_ENABLED: true
      ARTIFACT_NAME: bin
      ARTIFACT_PATH: bin
  test:
    needs: secret-detection
    uses: bayudwiyansatria/.github/.github/workflows/test-go.yml@master
    with:
      GO_VERSION: "1.26.x"
      TEST_ARTIFACT_NAME: coverage
      TEST_ARTIFACT_PATH: coverage.out
```

Preserve each caller's existing branch contract. Publish the shared workflows and configuration action before merging
callers that reference them. A remote Actions run is required to verify execution and artifact upload; configuration
review does not establish that those operations succeeded.
