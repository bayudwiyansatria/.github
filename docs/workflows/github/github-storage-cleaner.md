# GitHub Storage Cleaner Workflow

[`github-storage-cleaner.yml`](../../../.github/workflows/github-storage-cleaner.yml) removes old Actions artifacts
and old container package versions, the two kinds of storage GitHub bills for private repositories. Both jobs use the
GitHub CLI preinstalled on GitHub-hosted runners; no third-party action is involved.

## Inputs

| Input                      | Default         | Contract                                                              |
| -------------------------- | --------------- | --------------------------------------------------------------------- |
| `DRY_RUN`                  | `true`          | Report what would be deleted without deleting anything                |
| `PRIVATE_ONLY`             | `true`          | Skip everything unless the repository is private                      |
| `ARTIFACT_CLEANUP_ENABLED` | `false`         | Delete this repository's artifacts older than `ARTIFACT_MAX_AGE_DAYS` |
| `ARTIFACT_MAX_AGE_DAYS`    | `1`             | Whole days, at least 1                                                |
| `PACKAGE_CLEANUP_ENABLED`  | `false`         | Delete old versions of the container package `PACKAGE_NAME`           |
| `PACKAGE_NAME`             | Repository name | Container package, for example `charts/agent-operator`                |
| `PACKAGE_KEEP_LATEST`      | `10`            | Newest build versions kept per image, besides protected versions      |
| `PACKAGE_KEEP_TAGS`        | See below       | Regex; a version with any matching tag is never deleted               |
| `PACKAGE_DELETE_UNTAGGED`  | `true`          | Delete versions without tags; unsafe for multi-platform images        |
| `OS_VERSION`               | `ubuntu-24.04`  | Runner image                                                          |

No secret is required. Callers grant `actions: write` for artifacts and `packages: write` for packages. Grant
`packages: write` even when package cleanup is off: GitHub checks the permissions a called workflow's jobs declare
before it evaluates their `if` conditions.

## Behavior

Every cleanup is opt-in: both `_ENABLED` inputs default to `false`, and a caller enables only the storage it uses. With
neither enabled, every job is skipped and nothing calls the API.

A `billing` job reads the repository visibility through the API, because scheduled runs carry no repository payload.
Public repositories store artifacts and packages for free, so with `PRIVATE_ONLY` the cleanup jobs run only when the
repository is private.

The package job lists every version with its tags, newest first, and decides each one:

1. A version with a tag matching `PACKAGE_KEEP_TAGS` is kept. The default protects release versions, `main`, `master`
   and `latest`, with or without an image prefix: `1.0.0`, `codex-1.0.0`, `main`, `codex-latest`.
2. A version tagged with a commit SHA, such as `84dbbac` or `codex-84dbbac`, is a build. The newest
   `PACKAGE_KEEP_LATEST` builds of each image prefix are kept; older builds are deleted.
3. An untagged version is deleted when `PACKAGE_DELETE_UNTAGGED` is true. A multi-platform image stores its platform
   manifests as untagged versions, so set it to false for those packages.
4. Any other tagged version is kept.

The job refuses to delete every version of a package, and it writes the whole decision table to the job summary.
`actions/delete-package-versions` is not used: it matches the version name, which for containers is the digest, so it
cannot protect versions by tag.

## Thin caller

Run weekly, deleting on schedule and dry-running by default when started by hand:

```yaml
name: Cleanup

on:
  schedule:
    - cron: "0 3 * * 1"
  workflow_dispatch:
    inputs:
      DRY_RUN:
        type: boolean
        default: true

permissions:
  contents: read

jobs:
  cleanup:
    name: Storage Cleanup
    permissions:
      actions: write
      packages: write
    uses: bayudwiyansatria/.github/.github/workflows/github-storage-cleaner.yml@master
    with:
      DRY_RUN: ${{ github.event_name == 'workflow_dispatch' && inputs.DRY_RUN }}
      ARTIFACT_CLEANUP_ENABLED: true
      PACKAGE_CLEANUP_ENABLED: true
      PACKAGE_KEEP_LATEST: 10
```

A repository whose images live in another registry enables only `ARTIFACT_CLEANUP_ENABLED`.

Deleted versions cannot be pulled again. Rollbacks use release tags, which are always protected; an old non-release
commit can be rebuilt by running Main for it.
