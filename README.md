# actions

Blessed GitHub Actions.

[![CI](https://github.com/unmango/actions/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/unmango/actions/actions/workflows/ci.yml)
[![Test Podman Actions](https://github.com/unmango/actions/actions/workflows/test-podman-actions.yml/badge.svg)](https://github.com/unmango/actions/actions/workflows/test-podman-actions.yml)
[![License](https://img.shields.io/github/license/unmango/actions)](LICENSE)
[![Nix flake](https://img.shields.io/badge/nix-flake-5277C3?logo=nixos&logoColor=white)](flake.nix)
[![Last commit](https://img.shields.io/github/last-commit/unmango/actions)](https://github.com/unmango/actions/commits/main)

Composite actions and reusable workflows shared across my repos.
The podman actions mirror the `docker/*` action interfaces so a workflow can swap between them with minimal changes.

## Actions

| Action | Purpose | Docker counterpart |
| --- | --- | --- |
| [`podman-build-push`](podman-build-push/action.yml) | Build and optionally push an image with podman, with multi-arch, cache, and SBOM support | [`docker/build-push-action`](https://github.com/docker/build-push-action) |
| [`podman-login`](podman-login/action.yml) | Log into a container registry with podman, with ECR auto-detection | [`docker/login-action`](https://github.com/docker/login-action) |
| [`setup-qemu`](setup-qemu/action.yml) | Register QEMU binfmt emulators via `tonistiigi/binfmt` for cross-platform builds | [`docker/setup-qemu-action`](https://github.com/docker/setup-qemu-action) |
| [`setup-nix`](setup-nix/action.yml) | Install Nix and configure Cachix | none |

## Reusable workflows

| Workflow | Purpose |
| --- | --- |
| [`docker-build-push.yml`](.github/workflows/docker-build-push.yml) | Build and push with buildx, tagged by `docker/metadata-action` |
| [`podman-build-push.yml`](.github/workflows/podman-build-push.yml) | Same interface as above, built with podman |
| [`goreleaser.yml`](.github/workflows/goreleaser.yml) | Run GoReleaser in release or snapshot mode |
| [`nix-flake-check.yml`](.github/workflows/nix-flake-check.yml) | Run `nix flake check` with Cachix |

## Usage

The repo has no tags.
The examples use `@main` for brevity; pin to a full commit SHA in real workflows.
`setup-qemu` registers `binfmt_misc` handlers with `sudo podman run --privileged`, so it needs `sudo` and a rootful podman.
GitHub-hosted Ubuntu runners meet both requirements.

### Composite actions

```yaml
permissions:
  contents: read
  packages: write

steps:
  - uses: actions/checkout@v4

  - uses: unmango/actions/setup-qemu@main

  - uses: unmango/actions/podman-login@main
    with:
      registry: ghcr.io
      username: ${{ github.actor }}
      password: ${{ github.token }}

  - uses: unmango/actions/podman-build-push@main
    with:
      platforms: linux/amd64,linux/arm64
      push: 'true'
      tags: ghcr.io/${{ github.repository }}:latest
```

### Reusable workflow

```yaml
permissions:
  contents: read
  packages: write

jobs:
  image:
    uses: unmango/actions/.github/workflows/podman-build-push.yml@main
    with:
      image: ghcr.io/${{ github.repository }}
      platforms: linux/amd64,linux/arm64
      push: ${{ github.event_name != 'pull_request' }}
      sbom: ${{ github.event_name != 'pull_request' }}
    secrets:
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

## Feature support matrix

<sub>✅ supported · ⚠️ accepted for interface parity but ignored · ❌ not available</sub>

### `podman-build-push` vs `docker/build-push-action`

| Input | Podman | Docker | Notes |
| --- | --- | --- | --- |
| `context` | ✅ | ✅ | |
| `file` | ✅ | ✅ | |
| `platforms` | ✅ | ✅ | Multiple platforms build a manifest list; run `setup-qemu` first |
| `tags` | ✅ | ✅ | |
| `labels` | ✅ | ✅ | |
| `annotations` | ✅ | ✅ | |
| `build-args` | ✅ | ✅ | |
| `build-contexts` | ✅ | ✅ | |
| `secrets` | ✅ | ✅ | Podman takes the `id=id,src=path` form only |
| `no-cache` | ✅ | ✅ | |
| `cache-from` | ✅ | ✅ | Podman takes a single registry ref or local directory, not buildx `type=...` syntax, and forces `--layers` |
| `cache-to` | ✅ | ✅ | Same as `cache-from` |
| `pull` | ✅ | ✅ | |
| `network` | ✅ | ✅ | |
| `add-hosts` | ✅ | ✅ | |
| `cgroup-parent` | ✅ | ✅ | |
| `shm-size` | ✅ | ✅ | |
| `ulimit` | ✅ | ✅ | |
| `push` | ✅ | ✅ | |
| `sbom` | ✅ | ✅ | Podman requires `push` and needs `syft` and `cosign` on the runner |
| `provenance` | ⚠️ | ✅ | No native SLSA provenance generation in buildah or podman |
| `ssh` | ⚠️ | ✅ | No buildkit SSH agent forwarding equivalent |
| `no-cache-filters` | ⚠️ | ✅ | buildah cache invalidation is all-or-nothing |
| `allow` | ⚠️ | ✅ | No buildkit entitlement model |
| `attests` | ❌ | ✅ | |
| `builder` | ❌ | ✅ | |
| `call` | ❌ | ✅ | |
| `load` | ❌ | ✅ | Podman images are already in the local store after a build |
| `outputs` | ❌ | ✅ | |
| `secret-envs` | ❌ | ✅ | |
| `secret-files` | ❌ | ✅ | Use `secrets` |
| `target` | ❌ | ✅ | |
| `github-token` | ❌ | ✅ | |

| Output | Podman | Docker | Notes |
| --- | --- | --- | --- |
| `imageid` | ✅ | ✅ | |
| `digest` | ✅ | ✅ | Podman only sets it when `push` is true |
| `metadata` | ✅ | ✅ | Podman emits `containerimage.imageid`, `containerimage.digest`, and `image.tags`, a subset of the buildx shape |

Podman boolean inputs are strings (`'true'` and `'false'`) because composite actions have no typed inputs.

### `podman-login` vs `docker/login-action`

| Input | Podman | Docker | Notes |
| --- | --- | --- | --- |
| `registry` | ✅ | ✅ | |
| `username` | ✅ | ✅ | |
| `password` | ✅ | ✅ | |
| `ecr` | ✅ | ✅ | `auto`, `true`, or `false`; needs `aws-actions/configure-aws-credentials` first |
| `logout` | ⚠️ | ✅ | Composite actions have no post-job hook; no warning is logged |

### `setup-qemu` vs `docker/setup-qemu-action`

| Input | Podman | Docker | Notes |
| --- | --- | --- | --- |
| `platforms` | ✅ | ✅ | |
| `image` | ✅ | ✅ | |
| `cache-image` | ❌ | ✅ | |
| `cache-binary` | ❌ | ✅ | |

### `podman-build-push.yml` vs `docker-build-push.yml`

| Input | Podman | Docker | Notes |
| --- | --- | --- | --- |
| `image` | ✅ | ✅ | |
| `platforms` | ✅ | ✅ | Podman runs `setup-qemu` when more than one platform is listed |
| `push` | ✅ | ✅ | |
| `build-args` | ✅ | ✅ | |
| `file` | ✅ | ✅ | |
| `context` | ✅ | ✅ | |
| `dockerhub_token` (secret) | ✅ | ✅ | |
| `secrets-list` | ✅ | ❌ | Build secrets in `id=id,src=path` form |
| `cache` | ✅ | ❌ | `none`, `registry`, or `local`; docker always uses the GHA cache backend |
| `cache-image` | ✅ | ❌ | Registry ref used when `cache` is `registry` |
| `cache-dir` | ✅ | ❌ | Directory used when `cache` is `local`, persisted with `actions/cache` |
| `sbom` | ✅ | ❌ | Docker always attaches an SBOM when pushing; podman installs `syft` and `cosign` and attaches an SPDX SBOM on request |
| `provenance` | ⚠️ | ❌ | Docker always generates provenance when pushing; podman accepts the input and ignores it |

## Development

Enter the dev shell with `nix develop`, or let direnv load it from `.envrc`.

```sh
make check   # nix flake check, includes actionlint
make fmt     # nix fmt
```
