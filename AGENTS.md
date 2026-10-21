# AGENTS.md — distro-arch

The **Arch Linux image family** — charly's `box/arch`. It OWNS the Arch
base/builder stack locally (`arch`, `arch-builder`, `cuda-arch-builder`) and the
Arch-rooted images and VMs, discovered from `box/` and `candy/`. Every shared
candy layer is an `@github.com/opencharly/<layer-*|pod-*|plugin-*>:<tag>` ref
into its standalone candy repo; there is **no namespace import** (`import: []`).

Canonical files:

- `charly.yml` — the root manifest: the `discover:` tree, the inline VM and
  check-bed entities, and the two `skill:` routing nodes this repo's own test
  candies own (`arch-pac-test`, `arch-aur-test`), each carrying
  `name`/`family`/`owner` and no body — and no `triggers` either (measured; the
  corpus's generated `DISPATCHER.md` carries no row for them, consistently).
- `candy/arch-skills/charly.yml` — the `arch-skills` bundle candy PLUS the five
  `skill:` routing nodes whose only other owner was an image (`arch`,
  `arch-builder`, `arch-test`, `arch-coder`, `charly-arch`), each with
  `owner: arch-skills`. A skill's `owner:` must be a CANDY: an image is a
  `candy:` node carrying `base:`/`from:`, so it has no source directory, its
  skill's prose can never be read and the node never reaches the
  `ai.opencharly.skill` label (opencharly/sdk#434). The candy is composed into
  every image that carries one of those skills.
- `<owning candy>/.agents/skills/<name>/SKILL.md` — the seven skill BODIES, one
  directory per node's `name:`, BESIDE the candy that owns the node
  (`candy/arch-skills/` for five, `candy/arch-pac-test/` and
  `candy/arch-aur-test/` for the other two — the reader is
  `<candy source dir>/.agents/skills/<name>/SKILL.md`, never the repo root). The
  files are the source of record for the prose.
- `box/<name>/charly.yml` — one manifest per image / builder / test box.
- `candy/<name>/charly.yml` — the arch-local test candies and the skill bundle.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:arch` — the Arch base image; root of the pac hierarchy.
- `/charly-distros:arch-builder` — the pixi/npm/cargo/yay builder.
- `/charly-distros:arch-test`, `/charly-distros:arch-pac-test`,
  `/charly-distros:arch-aur-test` — the test boxes and their pac/aur candies.
- `/charly-coder:arch-coder`, `/charly-coder:charly-arch` — the dev and charly
  toolchain images.
- `/charly-vm:arch-cloud-vm` — the cloud-image VM.
- `/charly-image:image` + `/charly-image:layer` — composition and candy
  authoring (`charly.yml` schema, `plan:` step verbs, service declarations).
- `/charly-check:check` — the disposable check beds and `plan:` authoring.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The functional evidence is the disposable check beds under `charly.yml`
  (`check-arch-vscode-pod`, `check-arch-vm`, `check-arch-pacstrap-vm`, …) and
  the `plan:` `check:` steps on every box and candy. A docs-only change runs no
  runtime bed — the documentation-only change class runs the non-runtime
  standards only.

## Modify this repo

- Edit a skill's body under its OWNING candy's `.agents/skills/<name>/` and its
  routing node in that same candy's `charly.yml` together — never the generated
  `SKILL.md` in the marketplace corpus; a corpus regeneration
  (`charly marketplace generate`) projects both.
- Package and behaviour changes go in the matching `distro:` arm and the box's
  `candy:` list; the `@github` refs pin to explicit CalVer tags.
- New behaviour claims belong in a `plan:` as an observable `check:` step, and
  in the owning skill.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
