# Docker layer cache - 2026-10-17

## Changes and rationale

`build-docker.yml` accepts `CACHE_ENABLED`, defaulting to `false`, and an optional `CACHE_SCOPE`. Opted-in builds use a
Docker-container Buildx builder to import and export actual layer cache data. Configure Docker already caches
`~/.buildx`, but the original build commands never exported layer data there.

The backend uses separate `buildcache-<hash>` tags within the existing image repository. The existing GitLab registry
login can read and write them: no new secret, token permission, registry project or image repository is required. The
hash separates caller repositories, Git refs, Dockerfile paths, contexts and optional matrix scopes. It excludes the
changing short-SHA image tag and runtime build argument so consecutive Main builds can share their cache.

Opt-in keeps existing callers unchanged, including callers with other platforms or shell-expanded build arguments.
The new path accepts one literal build argument, matching agent-runner's `RUNTIME_IMAGE` assignment. It exports
intermediate layers with `mode=max` only for push events that already enable image publication. Missing caches fall
back to cold builds, and cache export failures do not fail an otherwise successful image build.

Application images still load into Docker and use the existing image push step, with the same requested tags. Cache
metadata is kept out of the application image, and no provenance or SBOM attestations are added. Retagging always
skips artifact download, Buildx setup and caching; Release continues to promote Main's image without rebuilding it.

The Docker workflow guide now describes the actual input defaults, unused compatibility inputs, cache isolation,
rollback, and the owner's digest and layer comparison procedure. agent-runner's Main build calls must opt in after the
shared workflow is published; no caller files were changed here.

## Verification boundary

Local validation passes workflow YAML parsing, Bash syntax, 13 mocked Docker shell cases and five build/retag path
selection cases. They cover stable and isolated cache scopes, literal build arguments, read-only imports, failure
propagation, cache/image target collisions and unchanged retagging. All 21 calls across the five agent repositories
match the reusable-workflow inputs and existing registry-secret mappings. The old inputs, defaults, secrets,
permissions and original step bodies are unchanged; 53 relative documentation links and the YAML example validate.
No workflow was run and no image or cache was built or pushed live.

KNR-17 remains in progress until the owner enables the cache and compares consecutive agent-runner Main builds. They
must verify reuse of unchanged compressed layer digests and sizes, exact-input cold/warm image digest parity, and
Release's digest-preserving promotion. The guide records the commands and acceptance boundary. Blueprint is unchanged.
