# Configure Go Action

[`actions/configure-go/action.yaml`](../../actions/configure-go/action.yaml) wraps `actions/setup-go` using the house
configuration-action pattern. It requires `contents: read` and no application secrets.

## Inputs

| Name                    | Default  | Contract                                                     |
| ----------------------- | -------- | ------------------------------------------------------------ |
| `go-version`            | Empty    | Version or range; overrides the version file when supplied   |
| `go-version-file`       | `go.mod` | Path relative to the checked-out repository root             |
| `cache`                 | `false`  | Enable module/build caching when useful                      |
| `cache-dependency-path` | `go.mod` | Dependency files relative to the checked-out repository root |

The action resolves the toolchain from `go.mod` unless a caller explicitly selects a version. Caching is disabled by
default so standard-library-only starters need no checksum file. Projects with external dependencies may enable it and
select `go.sum` as the cache key. These inputs follow the
[setup-go contract](https://github.com/actions/setup-go#usage).

## Example

```yaml
steps:
  - uses: actions/checkout@v7
  - uses: bayudwiyansatria/.github/actions/configure-go@master
    with:
      go-version: "1.26.x"
```

Publish the action to the referenced shared repository revision before consumers use it. Local files alone are not
available to remote workflow calls.
