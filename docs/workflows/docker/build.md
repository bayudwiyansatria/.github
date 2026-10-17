# Docker Build Workflow

`build-docker.yml` builds one image, or promotes an existing image without rebuilding it. The caller owns triggers,
the image name and tag, publication policy, and registry secret mapping. This workflow declares no additional token
permissions.

## Inputs and secrets

| Input                      | Type    | Default                                      | Contract                                                                                                               |
| -------------------------- | ------- | -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `DOCKER_COMMAND`           | string  | `docker build -t ${{ github.repository }} .` | Accepted for compatibility; unused.                                                                                    |
| `SOURCE_DOCKER_REPOSITORY` | string  | empty                                        | Source image, including registry and tag or digest; required for retagging.                                            |
| `DOCKER_REPOSITORY`        | string  | `${{ github.repository }}`                   | Image path within the registry.                                                                                        |
| `DOCKER_FILE`              | string  | `Dockerfile`                                 | Dockerfile relative to the checkout root.                                                                              |
| `DOCKER_IMAGE_TAG`         | string  | `${{ github.sha }}`                          | The exact tag to build or promote.                                                                                     |
| `DOCKER_BUILD_ARG`         | string  | `null`                                       | Existing build-argument input. The cache path accepts one literal `NAME=value` argument.                               |
| `WORKING_DIR`              | string  | `.`                                          | Build context relative to the checkout root.                                                                           |
| `PUSH_ENABLED`             | boolean | `false`                                      | Push the requested image after building or retagging.                                                                  |
| `RETAGING_ONLY`            | boolean | `false`                                      | Pull and retag the source; skip artifact download, builders and layer caching. Spelling is retained for compatibility. |
| `SBOM_ENABLED`             | boolean | `true`                                       | Accepted for compatibility; the existing workflow does not generate an SBOM.                                           |
| `ARTIFACT_ENABLED`         | boolean | `true`                                       | Download the caller's build artifact before building.                                                                  |
| `CACHE_ENABLED`            | boolean | `false`                                      | Opt in to registry layer caching with a Docker-container Buildx builder.                                               |
| `CACHE_SCOPE`              | string  | empty                                        | Additional discriminator for matrix entries sharing one Dockerfile and context.                                        |
| `OS_VERSION`               | string  | `ubuntu-24.04`                               | Runner operating system. Caching requires support for the Linux Docker-container builder.                              |
| `DOCKER_VERSION`           | string  | `20.10`                                      | Passed to Configure Docker; that action currently logs in but does not install an Engine version.                      |
| `DOCKER_REGISTRY`          | string  | `docker.pkg.github.com`                      | Registry hostname.                                                                                                     |
| `ARTIFACT_NAME`            | string  | `dist`                                       | Build artifact to download.                                                                                            |
| `ARTIFACT_PATH`            | string  | `dist`                                       | Artifact download path relative to the checkout root.                                                                  |

`DOCKER_USERNAME` and `DOCKER_PASSWORD` remain the two required secrets. Agent callers map their existing GitLab
registry username and token only in image jobs. No new secret, permission, registry project or image repository is
needed for caching.

## Existing callers and promotion

With caching disabled, both original `docker build` commands and their build-argument behavior are unchanged. Checkout,
artifact download, login, the requested image tag and the final `docker push` retain their existing contracts. Callers
using this workflow at `@master` do not opt in automatically.

`RETAGING_ONLY: true` always takes precedence over layer caching. Its image operations are only `docker pull`,
`docker tag` and, when enabled, `docker push`. Configure Docker still performs its existing login and `~/.buildx` cache
step. The source image is never rebuilt. Main's branch/latest aliases and Release's version/branch/latest
aliases continue to promote the image supplied by the caller. No cache manifest is used as a release source.

## Layer cache

Caching is opt-in because this shared workflow serves repositories with different builders, platforms and build
arguments. Enabling it changes the build path to Buildx and starts retaining intermediate build layers in the registry;
neither should be silently enabled for every caller. The agent repositories' current inputs work with this path.

The backend is a [separate registry cache](https://docs.docker.com/build/cache/backends/registry/), not inline cache or
GitHub cache storage. It uses the already-authenticated image repository:

```text
<DOCKER_REGISTRY>/<DOCKER_REPOSITORY>:buildcache-<scope hash>
```

The scope hash uses the caller repository, Git ref, Dockerfile path, context path and optional `CACHE_SCOPE`. The
runtime, Claude Code, Codex and Gemini Dockerfiles have different caches even though they share one GitLab image
repository. The changing short-SHA tag and `RUNTIME_IMAGE` build argument are excluded from the scope, so consecutive
Main builds can reuse layers. BuildKit still checks actual instructions, inputs and resolved base images before
reusing a layer. Add a distinct `CACHE_SCOPE` for other matrix variants sharing the same paths.

Cache manifests use a reserved `buildcache-` tag in that same repository. They do not overwrite a short-SHA, version,
branch or latest image tag. The workflow refuses an image target equal to its computed cache target. Registry cleanup
must retain the active cache tag if warm builds are wanted; deleting it is safe and makes the next build cold.

The cache exports `mode=max`, including intermediate stages. Only a push event with `PUSH_ENABLED: true` exports a
cache. Build-only and review events may import the current scope but do not write a cache. The cache is private under
the same registry access controls as the image; do not pass credentials through build arguments or copy them into build
layers. Cache misses continue with a cold build, and cache export errors are non-fatal through `ignore-error=true`.
An image build failure or image push failure still fails the job.

The cached build loads one image into Docker with `--load` and leaves publication to the existing Push Docker step.
It does not embed inline cache metadata, add labels, alter the requested tag, or push the application image through a
new exporter. It disables provenance and SBOM attestations to retain the existing single-image publication shape.
`SBOM_ENABLED` remains unused, as on the original path. No platform, compression or timestamp override is added.
The cache is an optimization, not a new source of application content or an alternative release artifact.

Configure Docker's older `~/.buildx` cache is unchanged. That directory alone is not a layer cache: the original build
commands never export their layers into it. This workflow supplies the missing BuildKit import/export path when the
caller opts in.

## Example caller

```yaml
jobs:
  docker:
    name: Docker GitLab
    permissions:
      contents: read
    uses: bayudwiyansatria/.github/.github/workflows/build-docker.yml@master
    with:
      DOCKER_REGISTRY: registry.gitlab.com
      DOCKER_REPOSITORY: bayudwiyansatria/images/agent-runner
      DOCKER_FILE: docker/agent/codex.Dockerfile
      DOCKER_BUILD_ARG: RUNTIME_IMAGE=registry.gitlab.com/bayudwiyansatria/images/agent-runner:runtime-<short-sha>
      DOCKER_IMAGE_TAG: codex-<short-sha>
      PUSH_ENABLED: true
      ARTIFACT_ENABLED: true
      ARTIFACT_NAME: bin
      ARTIFACT_PATH: bin
      CACHE_ENABLED: true
    secrets:
      DOCKER_USERNAME: ${{ secrets.GITLAB_REGISTRY_USERNAME }}
      DOCKER_PASSWORD: ${{ secrets.GITLAB_REGISTRY_TOKEN }}
```

For agent-runner, enable `CACHE_ENABLED: true` on Main's `docker-runtime` and `docker-agent` calls. Their Dockerfile paths
already separate the four caches; no scope override is needed. Leave alias and Release calls as retagging calls.
No caller files are changed by this shared-workflow implementation.

The cached path treats `DOCKER_BUILD_ARG` as one literal argument, not shell syntax or a list. The current agent caller
supplies one `RUNTIME_IMAGE=...` assignment. Callers relying on multiple shell-expanded arguments must keep caching off
until their argument contract is reviewed. `DOCKER_COMMAND` is not executed on either path.

## Owner verification for KNR-17

Local workflow checks do not establish registry storage savings or digest parity. After publishing the shared change
and opting in the two Main build calls, compare two consecutive agent-runner Main builds from the same branch:

1. Record their short SHAs, binary artifacts, Dockerfile revisions, CLI versions and resolved base-image digests. The
   useful comparison changes only the runner binary; moving base tags or CLI versions are different inputs.
2. In the first run, record the printed cache references and confirm successful cache exports. A missing initial cache
   is expected. In the second, confirm imports use the same four references and unchanged steps say `CACHED`.
3. Read each pushed image's actual manifest with the existing registry login. These commands inspect registry
   manifests without pulling image layers onto the host:

   ```powershell
   $imageRepository = 'registry.gitlab.com/bayudwiyansatria/images/agent-runner'
   $firstShortSha = '<first-main-short-sha>'
   $secondShortSha = '<second-main-short-sha>'

   foreach ($variant in @('runtime', 'claude-code', 'codex', 'gemini')) {
     $firstImage = "${imageRepository}:${variant}-${firstShortSha}"
     $secondImage = "${imageRepository}:${variant}-${secondShortSha}"

     docker buildx imagetools inspect $firstImage
     docker buildx imagetools inspect $secondImage

     $firstManifest = docker buildx imagetools inspect --raw $firstImage | ConvertFrom-Json
     $secondManifest = docker buildx imagetools inspect --raw $secondImage | ConvertFrom-Json

     if ($firstManifest.manifests -or $secondManifest.manifests) {
       throw 'Select and inspect matching linux/amd64 child manifests before comparing layers.'
     }

     Write-Output "${variant}: first build layers"
     $firstManifest.layers | Select-Object digest, size

     Write-Output "${variant}: second build layers"
     $secondManifest.layers | Select-Object digest, size

     Compare-Object $firstManifest.layers $secondManifest.layers -Property digest, size
   }
   ```

   If a registry returns an index, select its descriptor with `platform.os = linux` and `platform.architecture = amd64`,
   then inspect `${imageRepository}@<child-digest>` for each build. Compare ordered `layers[].digest` and `layers[].size`
   lists of those child manifests, not the index or cache manifest.

4. All runtime layers should match if its inputs stayed fixed. Each agent image should reuse the runtime and unchanged
   CLI layers; only the final binary layer should differ for a binary-only change. A CLI version bump may change its CLI
   layer too. Large unchanged layers must retain the exact compressed digest and size, and Push Docker should report
   them as already present. Compare unique layer blobs, not the sum of tag sizes, because tags share blobs.
5. For exact-input parity, build the same downloaded artifact and resolved base/CLI inputs with the same BuildKit
   version, once without cache import/export and once warm. Compare the image config, ordered layer digests and final
   registry manifest digest. Unexpected differences block enabling the cache; different source revisions or mutable
   base images are different inputs. No live parity check was performed by this change.
6. Confirm Main's aliases resolve to its short-SHA image digest. At the next owner-run Release, its version and alias
   digests must equal the selected Main image's digest. Retagging jobs must contain no build or cache step. Never rebuild
   to make a Release tag.

Rollback is `CACHE_ENABLED: false` in the caller. The existing build path returns and published images and Release
promotion remain available. Cache tags can be left in place; deleting them is not required for rollback.
