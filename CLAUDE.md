# image/cachyos — signpost (not the rule-set)

This submodule is the **CachyOS** base image family (x86_64_v3-optimized Arch
derivative): a single `overthink.yml` that imports the main repo under the `ov`
namespace and `build.yml` flat. Main's `versa` image consumes cachyos via the
`cachyos` import namespace (the main↔cachyos mutual import is cycle-broken at
load).

**Load these skills FIRST (R0):**

- `/ov-distros:cachyos` — the CachyOS base image.
- `/ov-distros:cachyos-pacstrap`, `/ov-distros:cachyos-pacstrap-builder` — the
  bootstrap builder.
- `/ov-vm:cachyos` — the CachyOS bootstrap VM + its `kind: eval` bed.
- `/ov-local:ov-cachyos` — the operator workstation profile.

**Authoritative rules live in the `overthink` superproject's root `CLAUDE.md`**
(R0–R10, hard-cutover, AI attribution, git-workflow). This file only signposts
and restates no rule. The multi-agent workflow is in `/ov-internals:agents`.
History lives in `CHANGELOG.md`.
