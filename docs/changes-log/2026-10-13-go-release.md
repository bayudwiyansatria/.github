# Go release contract - 2026-10-13

## Changes and rationale

Added `release-go.yml` for the Go binary contract. The generic GitHub release workflow assumes Node-oriented packaging,
a pre-existing bare version tag and legacy filename conventions; those assumptions do not fit the Go starter.
A dedicated shared release job preserves the house three-input dispatch and thin callers while retaining legacy jobs.

Release packages the current run's Build artifact and checksum, then creates a GitHub release from committed notes at
the dispatch commit. Tags use Go `v<version>` syntax. Only Release has repository write permission and an explicit token
mapping.

## Verification boundary

Local checks cover YAML, caller inputs, shell syntax and isolated release contract/packaging behavior. No remote release
or tag is created during implementation. Shared dependencies must be published before callers can execute remotely.
