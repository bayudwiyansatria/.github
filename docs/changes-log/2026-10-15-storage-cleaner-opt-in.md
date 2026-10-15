# Storage cleaner opt-in - 2026-10-15

## Changes and rationale

`ARTIFACT_CLEANUP_ENABLED` now defaults to `false`, matching `PACKAGE_CLEANUP_ENABLED`, so every cleanup in
`github-storage-cleaner.yml` is opt-in and a caller enables only the storage it uses. The `billing` job runs only when at
least one cleanup is enabled; with none enabled, every job is skipped and no API call is made.

The agent repositories moved their images to GitLab's container registry, so their callers enable artifact cleanup
alone. Callers still grant `packages: write`, because GitHub checks the permissions a called workflow's jobs declare
even for jobs that will be skipped.

The workflow had no released callers before this change, so no existing caller loses artifact cleanup silently. A new
caller that wants artifact cleanup must set `ARTIFACT_CLEANUP_ENABLED: true`.

## Verification boundary

Workflow YAML parses locally. No run has used the new defaults yet.
