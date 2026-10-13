# Build Go Workflow

[`build-go.yml`](../../../.github/workflows/build-go.yml) checks out the caller's repository, configures Go, executes
the selected build command and optionally uploads its artifacts. It needs no secrets and grants `contents: read`.

## Inputs

| Name                    | Type    | Default          | Contract                                       |
| ----------------------- | ------- | ---------------- | ---------------------------------------------- |
| `GO_COMMAND`            | string  | `go build ./...` | Repository-owned build command                 |
| `WORKING_DIR`           | string  | `.`              | Command working directory                      |
| `OS_VERSION`            | string  | `ubuntu-24.04`   | Runner label                                   |
| `GO_VERSION`            | string  | Empty            | Version/range; overrides version file when set |
| `GO_VERSION_FILE`       | string  | `go.mod`         | Version file relative to repository root       |
| `CACHE_ENABLED`         | boolean | `false`          | Enable setup-go module/build cache             |
| `CACHE_DEPENDENCY_PATH` | string  | `go.mod`         | Cache key files relative to repository root    |
| `ARTIFACT_ENABLED`      | boolean | `false`          | Upload build output                            |
| `ARTIFACT_NAME`         | string  | `bin`            | Upload name                                    |
| `ARTIFACT_PATH`         | string  | `bin`            | Upload path relative to repository root        |
| `ARTIFACT_RETENTION`    | string  | `1`              | Retention in days                              |

The default build validates packages without requiring a binary artifact, which also supports library modules. A server
caller selects an executable command and enables upload. Missing output fails when artifact upload is enabled.

For a nested module, set `WORKING_DIR` to its directory and supply repository-relative version/cache/artifact paths.
Commands use Bash; non-Linux runners must provide it. Publication and deployment are separate caller decisions.

See [the combined caller example](README.md#caller-example).
