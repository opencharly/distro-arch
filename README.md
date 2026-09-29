# distro-arch

The **Arch Linux image family** for [OpenCharly](https://github.com/opencharly/charly) —
the root of the pac-based box hierarchy.

This repo is mounted as a git submodule at `box/arch` of the main repo. It OWNS
the Arch base/builder stack locally, so every image here bases on a bare local
`arch` and every multi-stage builder on a bare local `arch-builder` — no
namespace import. The shared candy layers are **not** vendored: each is an
`@github.com/opencharly/<layer-*|pod-*|plugin-*>[:subdir]:<tag>` ref into its
standalone candy repo, pinned to an explicit CalVer tag.

## What's here

| Kind | Entries |
|---|---|
| Base / builder | `arch` (base), `arch-builder` (pixi/npm/cargo/yay multi-stage builder), `cuda-arch-builder` (CUDA-enabled builder) |
| Images | `arch-coder` (kitchen-sink dev box), `charly-arch` (charly toolchain + MCP gateway), `arch-test`, `vscode-test` |
| Bootstrap pair | `arch-pacstrap-builder` (privileged), `arch-pacstrap` (`from: builder:pacstrap`) |
| VMs | `arch` (cloud-image, `source.kind: cloud_image`), `arch-pacstrap` (bootstrap, `source.kind: bootstrap`) |
| Local candies | `arch-pac-test`, `arch-aur-test` (under `candy/`) |
| Check beds | `check-arch-vscode-pod`, `check-tmux-pod`, `check-agent-pod`, `check-arch-pacstrap-vm`, `check-arch-vm` |

## Build

```bash
# Inside the submodule (the build verb defaults to charly.yml):
charly box build arch-coder

# From the parent opencharly repo:
charly -C box/arch box build arch-coder

# Standalone, against the published repo:
charly --repo opencharly/distro-arch box build arch-coder
```

The first build resolves the upstream github references into
`~/.cache/charly/repos/` and materializes the referenced layers under
`.build/_layers/`.

## The bootstrap VM (`arch-pacstrap`)

`arch-pacstrap` builds a bootable Arch rootfs from scratch via `pacstrap` inside
the privileged `arch-pacstrap-builder` container. Its disposable R10 bed is
`check-arch-pacstrap-vm`:

```bash
charly check run check-arch-pacstrap-vm
```

The Docker-Hub/cloud-image path is the faster default; the pacstrap variant is
for offline / air-gapped builds.

## Requirements

A build of any image here fetches from the upstream repo, so it needs network
access and a `charly` recent enough to understand the config's schema version
(`charly` hard-fails with a "newer than this charly supports" message if the
config schema is newer than the binary supports).

## Layout

- `charly.yml` — the root manifest: the `discover:` tree, the inline VM and
  check-bed entities, and the embedded `skill:` entities.
- `box/<name>/charly.yml` — one manifest per image / builder / test box.
- `candy/<name>/charly.yml` — the arch-local test candies.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skills: `/charly-distros:arch`, `/charly-distros:arch-builder`,
  `/charly-distros:arch-test`, `/charly-distros:arch-pac-test`,
  `/charly-distros:arch-aur-test`, `/charly-coder:arch-coder`,
  `/charly-coder:charly-arch`
- Bootstrap VM: `/charly-vm:arch-cloud-vm`
- Consumers: `/charly-distros:cachyos`, `/charly-distros:omarchy` (both import
  this repo under the `arch` namespace)
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
