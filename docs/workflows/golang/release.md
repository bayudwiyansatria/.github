# Release Go Workflow

[`release-go.yml`](../../../.github/workflows/release-go.yml) publishes a previously built Linux amd64 Go executable.
It uses the house three-component manual version input contract and packages the current run's Build artifact.

## Inputs and secret

| Input                 | Default              | Contract                                    |
| --------------------- | -------------------- | ------------------------------------------- |
| `MAJOR_VERSION`       | Required             | Nonnegative integer without leading zeroes  |
| `MINOR_VERSION`       | Required             | Nonnegative integer without leading zeroes  |
| `INCREMENTAL_VERSION` | Required             | Nonnegative integer without leading zeroes  |
| `MODULE_FILE`         | `go.mod`             | Repository-relative module file             |
| `RELEASE_NOTES_DIR`   | `docs/release-notes` | Committed `<version>.md`                    |
| `ARTIFACT_NAME`       | `bin`                | Current run's Build artifact                |
| `ARTIFACT_PATH`       | `bin`                | Repository-relative download directory      |
| `BINARY_NAME`         | `server`             | Plain executable basename in that directory |

The caller grants `contents: write` and maps a repository write token to `GITHUB_PAT`. The built-in
`secrets.GITHUB_TOKEN` works when repository rules permit tag and release creation. No registry credential is required.
The Ubuntu job expects a Linux amd64 binary, so callers must build that target deliberately.

## Validation and publication

The job requires default-branch dispatch, validates version syntax and the Go module major path, and requires nonempty
tracked release notes. It downloads the current run's artifact, restores executable permission, packages the selected
binary as `<repository>-v<version>-linux-amd64.tar.gz`, and creates `SHA256SUMS`.
Binary admission checks the x86-64 ELF header, and the archive explicitly records executable mode 0755.

The release and its Go `v` tag target `github.sha`, with the committed notes as the body. Existing releases and tags
pointing at another commit are rejected. Matching tags from incomplete attempts may be reused when no release exists.
Partial publication requires inspection before retrying; assets are never automatically replaced. Checksums establish
integrity, not a signature.

## Thin caller

Dispatch inputs are the three version fields. Build/Test must depend on secret detection, and this job waits for both:

```yaml
release:
  needs: [build, test]
  permissions:
    contents: write
  uses: bayudwiyansatria/.github/.github/workflows/release-go.yml@master
  with:
    MAJOR_VERSION: ${{ inputs.MAJOR_VERSION }}
    MINOR_VERSION: ${{ inputs.MINOR_VERSION }}
    INCREMENTAL_VERSION: ${{ inputs.INCREMENTAL_VERSION }}
    ARTIFACT_NAME: bin
    ARTIFACT_PATH: bin
    BINARY_NAME: server
  secrets:
    GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}
```

Build uses `CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -o bin/server ./cmd/server` and uploads `bin`.
For v2+, the module path must end in matching `/vN`. Publish the shared dependencies before enabling callers. Remote
artifact download, token and publication behavior is established only by an authorized Actions dispatch.
