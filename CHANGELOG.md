# Changelog

## [1.3.0](https://github.com/unmango/actions/compare/v1.2.0...v1.3.0) (2026-10-02)


### Features

* add podman-manifest action ([#94](https://github.com/unmango/actions/issues/94)) ([fff2717](https://github.com/unmango/actions/commit/fff2717148f5e682a1f497b53e70e49b5fc3ec17)), closes [#63](https://github.com/unmango/actions/issues/63)
* add podman-push action for images built elsewhere ([#93](https://github.com/unmango/actions/issues/93)) ([08095a6](https://github.com/unmango/actions/commit/08095a633e923296f99be531f1c29aecbea46abc)), closes [#62](https://github.com/unmango/actions/issues/62)
* add setup-podman action ([#92](https://github.com/unmango/actions/issues/92)) ([9a2bd86](https://github.com/unmango/actions/commit/9a2bd86378e807a80fc14f2087ebea01719281fc)), closes [#61](https://github.com/unmango/actions/issues/61)
* let callers choose runs-on for the reusable workflows ([#90](https://github.com/unmango/actions/issues/90)) ([bba6cd3](https://github.com/unmango/actions/commit/bba6cd322ef2762a7f4e8d5c91406ad12ff01944)), closes [#43](https://github.com/unmango/actions/issues/43)
* **release-please:** expose prs_created, pr, and paths_released outputs ([#89](https://github.com/unmango/actions/issues/89)) ([fbdcdef](https://github.com/unmango/actions/commit/fbdcdef59e43ecd9a2db2894025f6c00782b82f3)), closes [#55](https://github.com/unmango/actions/issues/55)


### Bug Fixes

* **release-please:** stop requesting GITHUB_TOKEN write for the job ([#88](https://github.com/unmango/actions/issues/88)) ([7dd2f4e](https://github.com/unmango/actions/commit/7dd2f4eabf436f14a2a76598082f949dd9c14804)), closes [#56](https://github.com/unmango/actions/issues/56)


### Documentation

* **release-please:** correct why an app token is preferred over a PAT ([#87](https://github.com/unmango/actions/issues/87)) ([061191c](https://github.com/unmango/actions/commit/061191c0c0db882dc8fd1403b18ecadd0a79b907)), closes [#44](https://github.com/unmango/actions/issues/44)

## [1.2.0](https://github.com/unmango/actions/compare/v1.1.1...v1.2.0) (2026-10-02)


### Features

* **setup-nix:** accept extra read-only Cachix caches ([#75](https://github.com/unmango/actions/issues/75)) ([939a10b](https://github.com/unmango/actions/commit/939a10b98bfd8e842b0b8e3e980869b71121f29b)), closes [#49](https://github.com/unmango/actions/issues/49)
* **setup-nix:** add skip_push input ([#76](https://github.com/unmango/actions/issues/76)) ([c7b2fb5](https://github.com/unmango/actions/commit/c7b2fb52f95f3d006c0bbe181affdc88e212af11)), closes [#52](https://github.com/unmango/actions/issues/52)
* **setup-nix:** default github_access_token to github.token ([#73](https://github.com/unmango/actions/issues/73)) ([003a4a5](https://github.com/unmango/actions/commit/003a4a5b3261fd1f53685083ab35dae803eff4e0)), closes [#50](https://github.com/unmango/actions/issues/50)
* **setup-nix:** optional magic-nix-cache ([#78](https://github.com/unmango/actions/issues/78)) ([798254e](https://github.com/unmango/actions/commit/798254e8c2a7d8b5a4c1495bb033cf8e880dafb5)), closes [#54](https://github.com/unmango/actions/issues/54)
* **setup-nix:** skip Cachix when cachix_name is empty ([#74](https://github.com/unmango/actions/issues/74)) ([7cbeebd](https://github.com/unmango/actions/commit/7cbeebd68ec5433f08240cb1238fb7da63b7ce7c)), closes [#51](https://github.com/unmango/actions/issues/51)
* **setup-nix:** support runners with Nix preinstalled ([#77](https://github.com/unmango/actions/issues/77)) ([4b38c93](https://github.com/unmango/actions/commit/4b38c9362ce87c33a2bfa572342b94a9fb483389)), closes [#53](https://github.com/unmango/actions/issues/53)

## [1.1.1](https://github.com/unmango/actions/compare/v1.1.0...v1.1.1) (2026-09-28)


### Bug Fixes

* **renovate:** reference shared presets by name ([#69](https://github.com/unmango/actions/issues/69)) ([463a597](https://github.com/unmango/actions/commit/463a597b63aa65ffb636e981e78660619e0c45f2)), closes [#67](https://github.com/unmango/actions/issues/67)


### Documentation

* add Hercules CI badge ([#68](https://github.com/unmango/actions/issues/68)) ([661c7af](https://github.com/unmango/actions/commit/661c7afc9c290012a87a65cb469f410d67c3da4e))

## [1.1.0](https://github.com/unmango/actions/compare/v1.0.1...v1.1.0) (2026-09-24)


### Features

* **setup-nix:** add use_daemon input ([#66](https://github.com/unmango/actions/issues/66)) ([f1aa94d](https://github.com/unmango/actions/commit/f1aa94d4f38f2b5bdedf5794d9a5ec676eb95e96))


### Continuous Integration

* add actionlint config and fix workflow_sha property reference ([#64](https://github.com/unmango/actions/issues/64)) ([32e0deb](https://github.com/unmango/actions/commit/32e0deb38c90466aac621bb3ce9fe765880653ac))

## [1.0.1](https://github.com/unmango/actions/compare/v1.0.0...v1.0.1) (2026-09-17)


### Bug Fixes

* **ci:** correct repo owner in self-referencing uses ([#33](https://github.com/unmango/actions/issues/33)) ([302a5d5](https://github.com/unmango/actions/commit/302a5d50084804f34c2665518464a2a331895bf4))


### Code Refactoring

* **flake.nix:** use `with inputs` to reduce repetition in imports list ([#41](https://github.com/unmango/actions/issues/41)) ([6bbd955](https://github.com/unmango/actions/commit/6bbd955be0091c67436f1dd4586dca0362e8ed72))

## 1.0.0 (2026-09-17)


### Features

* release-please workflow and repo versioning ([#10](https://github.com/unmango/actions/issues/10)) ([70f8e94](https://github.com/unmango/actions/commit/70f8e94158e6b0b6e458834d6fb7460e675229d5))


### Continuous Integration

* use thecluster bot app token for release-please ([#31](https://github.com/unmango/actions/issues/31)) ([1cd338e](https://github.com/unmango/actions/commit/1cd338e9068834a17e8359343daa3e820b2a0359))
