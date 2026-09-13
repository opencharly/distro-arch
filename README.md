# opencharly/distro-arch

The **Arch Linux image family** for [OpenCharly](https://github.com/opencharly/charly),
split into its own repository and mounted as a git submodule at `box/arch`
of the main repo.

## What's here

| Kind | Entries |
|---|---|
| `image:` | `arch`, `arch-builder`, `cuda-arch-builder` (base/builder stack), `arch-coder`, `charly-arch`, `arch-test`, `vscode-test`, `arch-pacstrap-builder`, `arch-pacstrap` |
| `vm:` | `arch-pacstrap` (bootstrap), `arch` (cloud-image) |
| `deploy:` | `check-arch-pacstrap-vm`, `check-arch-vm` (+ nested `arch-host`, `charly-cachyos-tailscale-test`) |

## Composition — self-contained base, candies by reference

This submodule OWNS the Arch base/builder stack locally (the `arch` base, the
`arch-builder` multi-stage builder, and the CUDA-enabled `cuda-arch-builder`),
so every image's base is a bare local `arch` and **no namespace import is needed**.
The candy LAYERS are not vendored — each is pulled from its standalone
`opencharly/<layer-*|pod-*|plugin-*>` repo by github reference:
`@github.com/opencharly/<layer-*|pod-*|plugin-*>[:subdir]:<tag>` in every box's
`candy:` list. The two arch-local test candies (`arch-pac-test`, `arch-aur-test`)
live under `candy/`.

All candy references pin to explicit CalVer tags (`v<YYYY.DDD.HHMM>`), so a build
is reproducible. There is exactly one definition of every layer — no duplication.

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

## Requirements

A build of any image here fetches from the upstream repo, so it needs network
access and a `charly` recent enough to understand the config's schema version
(`charly` hard-fails with a "newer than this charly supports" message if the config
schema is newer than the binary supports).

## Landing & releases

Every change to this repo lands through a **pull request**, never by a direct
push to `main`. Two org-wide workflows drive it:

1. `.github/workflows/pr-validator.yml` dispatches `charly/pr-validator` on the
   PR head; on a PASS verdict the validator arms GitHub native auto-merge
   (squash).
2. `.github/workflows/tag-on-merge.yml` fires on the validator's completion,
   waits for the squash merge to land, then mints the release tag on the merged
   HEAD at the merge-time CalVer (`v<YYYY.DDD.HHMM>`) and writes the
   `CHANGELOG/<calver>.md` entry from the PR body.

`.github/scripts/auto-merge-*.sh` hold the de-templated run block and its local
RDD harness for the org auto-merge engine — scripts, not workflows.

---
*Assisted-by: Claude Code deepseek-v4-flash:cloud (analysed on a live system)*
