# box/arch — signpost (not the rule-set)

This submodule is the **Arch Linux** base image family: a self-contained
`charly.yml` that OWNS the arch base/builder stack locally (`arch`,
`arch-builder`, `cuda-arch-builder`) and pulls main's shared candy layers via
`@github` refs — no namespace import.

**Load these skills FIRST (R0):**

- `/charly-distros:arch` — the Arch base image; root of the pac-based hierarchy.
- `/charly-distros:arch-builder` — the pixi/npm/cargo/yay builder.
- `/charly-coder:arch-coder`, `/charly-coder:charly-arch` — the dev/charly images here.
- `/charly-image:image` + `/charly-image:layer` — composition + layer authoring.

**Authoritative rules live in the `opencharly` superproject's root `CLAUDE.md`**
(R0–R10, hard-cutover, AI attribution, git-workflow). This file only
signposts and restates no rule. The full multi-agent workflow (sub-agents,
`/verify-beds`, `/audit-deploy-configs`) is in `/charly-internals:agents`. History
lives in this repo's `CHANGELOG/` (one file per month).
