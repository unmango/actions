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

The repo has no tags, so reference actions at `@main`.

### Composite actions

```yaml
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
jobs:
  image:
    uses: unmango/actions/.github/workflows/podman-build-push.yml@main
    with:
      image: ghcr.io/${{ github.repository }}
      platforms: linux/amd64,linux/arm64
      push: ${{ github.event_name != 'pull_request' }}
      sbom: true
    secrets:
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

## Feature support matrix

Legend:

- ✅ supported
- ⚠️ accepted for interface parity, logs a warning, and is ignored
- ❌ not available

### `podman-build-push` vs `docker/build-push-action`

| Input | Support | Notes |
| --- | --- | --- |
| `context` | ✅ | |
| `file` | ✅ | |
| `platforms` | ✅ | Multiple platforms build a manifest list; run `setup-qemu` first |
| `tags` | ✅ | |
| `labels` | ✅ | |
| `annotations` | ✅ | |
| `build-args` | ✅ | |
| `build-contexts` | ✅ | |
| `secrets` | ✅ | `id=id,src=path` form only |
| `no-cache` | ✅ | |
| `cache-from` | ✅ | A single registry ref or local directory, not buildx `type=...` syntax; forces `--layers` |
| `cache-to` | ✅ | Same as `cache-from` |
| `pull` | ✅ | |
| `network` | ✅ | |
| `add-hosts` | ✅ | |
| `cgroup-parent` | ✅ | |
| `shm-size` | ✅ | |
| `ulimit` | ✅ | |
| `push` | ✅ | |
| `sbom` | ✅ | Requires `push`; needs `syft` and `cosign` on the runner |
| `provenance` | ⚠️ | No native SLSA provenance generation in buildah or podman |
| `ssh` | ⚠️ | No buildkit SSH agent forwarding equivalent |
| `no-cache-filters` | ⚠️ | buildah cache invalidation is all-or-nothing |
| `allow` | ⚠️ | No buildkit entitlement model |
| `attests` | ❌ | |
| `builder` | ❌ | |
| `call` | ❌ | |
| `load` | ❌ | Built images are already in podman's local store |
| `outputs` | ❌ | |
| `secret-envs` | ❌ | |
| `secret-files` | ❌ | Use `secrets` |
| `target` | ❌ | |
| `github-token` | ❌ | |

| Output | Support | Notes |
| --- | --- | --- |
| `imageid` | ✅ | |
| `digest` | ✅ | Only set when `push` is true |
| `metadata` | ✅ | Contains `containerimage.imageid`, `containerimage.digest`, and `image.tags`, a subset of the buildx shape |

Boolean inputs are strings (`'true'` and `'false'`) because composite actions have no typed inputs.

### `podman-login` vs `docker/login-action`

| Input | Support | Notes |
| --- | --- | --- |
| `registry` | ✅ | |
| `username` | ✅ | |
| `password` | ✅ | |
| `ecr` | ✅ | `auto`, `true`, or `false`; needs `aws-actions/configure-aws-credentials` first |
| `logout` | ⚠️ | Composite actions have no post-job hook |

### `setup-qemu` vs `docker/setup-qemu-action`

| Input | Support | Notes |
| --- | --- | --- |
| `platforms` | ✅ | |
| `image` | ✅ | |
| `cache-image` | ❌ | |
| `cache-binary` | ❌ | |

### `podman-build-push.yml` vs `docker-build-push.yml`

| Input | Docker | Podman | Notes |
| --- | --- | --- | --- |
| `image` | ✅ | ✅ | |
| `platforms` | ✅ | ✅ | Podman workflow runs `setup-qemu` when more than one platform is listed |
| `push` | ✅ | ✅ | |
| `build-args` | ✅ | ✅ | |
| `file` | ✅ | ✅ | |
| `context` | ✅ | ✅ | |
| `dockerhub_token` (secret) | ✅ | ✅ | |
| `secrets-list` | ❌ | ✅ | Build secrets in `id=id,src=path` form |
| `cache` | ❌ | ✅ | `none`, `registry`, or `local` |
| `cache-image` | ❌ | ✅ | Registry ref used when `cache` is `registry` |
| `cache-dir` | ❌ | ✅ | Directory used when `cache` is `local`, persisted with `actions/cache` |
| `sbom` | always on when pushing | ✅ | Podman workflow installs `syft` and `cosign` and attaches an SPDX SBOM |
| `provenance` | always on when pushing | ⚠️ | |
| GHA cache backend (`type=gha`) | always on | ❌ | Use `cache: registry` or `cache: local` instead |

## Development

Enter the dev shell with `nix develop`, or let direnv load it from `.envrc`.

```sh
make check   # nix flake check, includes actionlint
make fmt     # nix fmt
```
