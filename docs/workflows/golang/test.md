# Test Go Workflow

[`test-go.yml`](../../../.github/workflows/test-go.yml) checks formatting and module metadata, runs vet and the test
command, then uploads coverage. It needs no secrets and grants `contents: read`.

## Inputs

| Name                      | Type    | Default                                          | Contract                                                 |
| ------------------------- | ------- | ------------------------------------------------ | -------------------------------------------------------- |
| `GO_COMMAND`              | string  | `go test -race -coverprofile=coverage.out ./...` | Repository-owned test command                            |
| `WORKING_DIR`             | string  | `.`                                              | Module working directory                                 |
| `OS_VERSION`              | string  | `ubuntu-24.04`                                   | Runner label; race tests require a supported C toolchain |
| `GO_VERSION`              | string  | Empty                                            | Version/range; overrides version file when set           |
| `GO_VERSION_FILE`         | string  | `go.mod`                                         | Version file relative to repository root                 |
| `CACHE_ENABLED`           | boolean | `false`                                          | Enable setup-go module/build cache                       |
| `CACHE_DEPENDENCY_PATH`   | string  | `go.mod`                                         | Cache key files relative to repository root              |
| `TEST_ARTIFACT_ENABLED`   | boolean | `true`                                           | Upload test output                                       |
| `TEST_ARTIFACT_NAME`      | string  | `coverage`                                       | Upload name                                              |
| `TEST_ARTIFACT_PATH`      | string  | `coverage.out`                                   | Upload path relative to repository root                  |
| `TEST_ARTIFACT_RETENTION` | string  | `1`                                              | Retention in days                                        |

Formatting checks tracked Go files below the working directory, excluding vendored source. Module checking runs
`go mod tidy` and fails on changed module metadata or a newly generated untracked checksum file. It works for a
standard-library-only module with no `go.sum`.

Tests build from source and do not download the binary build artifact. A customized command must produce the selected
artifact or explicitly disable artifact upload. Missing enabled output fails rather than reporting an empty upload.

For nested modules, supply the module working directory and repository-relative version/cache/artifact paths. The
default coverage file is written inside that working directory, so the upload path must include it.

See [the combined caller example](README.md#caller-example).
