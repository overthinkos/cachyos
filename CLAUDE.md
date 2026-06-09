# image/cachyos — signpost (not the rule-set)

This submodule is the **CachyOS** base image family (x86_64_v3-optimized Arch
derivative) PLUS the **CachyOS selkies streaming-desktop images** (`selkies-labwc`
CPU + `selkies-labwc-nvidia` / `selkies-kde` / `selkies-kde-nvidia` GPU): a
`charly.yml` (plus per-kind sibling files) that imports the main repo under the
`charly` namespace and `build.yml` flat. Main's `versa` consumes the cachyos base
and `android-emulator` roots on `base: cachyos.selkies-labwc`, both via the
`cachyos` import namespace (the main↔cachyos mutual import is cycle-broken at load).

**Load these skills FIRST (R0):**

- `/charly-distros:cachyos` — the CachyOS base image.
- `/charly-distros:cachyos-pacstrap`, `/charly-distros:cachyos-pacstrap-builder` — the
  bootstrap builder.
- `/charly-vm:cachyos` — the CachyOS bootstrap VM + its `kind: eval` bed.
- `/charly-local:charly-cachyos` — the operator workstation profile.
- `/charly-selkies:selkies-labwc`, `/charly-selkies:selkies-labwc-nvidia` — the CPU
  + GPU labwc streaming desktops; `/charly-selkies:selkies` for the engine.

**Authoritative rules live in the `opencharly` superproject's root `CLAUDE.md`**
(R0–R10, hard-cutover, AI attribution, git-workflow). This file only signposts
and restates no rule. The multi-agent workflow is in `/charly-internals:agents`.
History lives in `CHANGELOG.md`.
