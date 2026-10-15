# Storage cleaner - 2026-10-14

## Changes and rationale

`github-storage-cleaner.yml` now cleans both kinds of storage GitHub bills for private repositories. It previously
removed only Actions artifacts, through the third-party `c-hive/gha-remove-artifacts` action, and no repository called
it. Both jobs now use the GitHub CLI preinstalled on hosted runners.

Container builds pushed on every Main run were never removed, so package storage grew by about the size of each build.
The package job keeps every version tagged with a release version, `main`, `master` or `latest`, keeps the newest builds
of each image prefix, and deletes older builds and untagged versions. `actions/delete-package-versions` was not used: it
matches container versions by digest, so it cannot protect versions by tag.

A `billing` job reads the repository visibility from the API and, by default, skips cleanup outside private
repositories, where storage is free. `DRY_RUN` defaults to true and every run writes its decision table to the job
summary. The artifact input changed from a fixed one-day age to `ARTIFACT_MAX_AGE_DAYS`; the workflow had no callers.

## Verification boundary

Local checks cover YAML parsing and shell syntax. The package plan ran read-only against the real `agent-runner`
package, and the deletion rules ran against a synthetic history with a stand-in CLI: releases, `main`, `latest` and the
newest builds per image were kept, older builds and untagged versions were planned for deletion, and a dry run made no
delete calls. The visibility check returned private and public correctly for real repositories. No artifact or package
was deleted during implementation.
