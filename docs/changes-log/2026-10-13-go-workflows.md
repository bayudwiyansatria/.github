# Go reusable workflows - 2026-10-13

## Changes

- Added the `configure-go` composite action following the existing house configuration-action structure.
- Added `build-go.yml` and `test-go.yml` with Go/runtime, working-directory, cache and artifact inputs.
- Kept validation permissions read-only and made application credentials unnecessary for public Go modules.
- Documented the workflow contract and caller example in the developer book.

## Decisions

Build and test are separate reusable jobs. Callers own scan dependencies and branch filters. Tests compile from source
and run formatting, module checks, vet and race detection before uploading coverage. Build artifacts are optional so
libraries can use the default build without declaring an executable layout.

## Verification boundary

Local YAML, caller/input compatibility, artifact paths and documentation links are checked as part of this change.
Go source build, tests and vet were previously verified locally in `go-boilerplate`. Remote execution and artifact
upload remain unverified until these shared files are published and called by Actions.
