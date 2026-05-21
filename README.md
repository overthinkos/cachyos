# overthinkos/cachyos

The **CachyOS image family** for [Overthink](https://github.com/overthinkos/overthink),
split into its own repository and mounted as a git submodule at `image/cachyos`
of the main repo.

## What's here

| Kind | Entries |
|---|---|
| `image:` | `cachyos` (base, in `cachyos-base.yml`), `cachyos-pacstrap-builder`, `cachyos-pacstrap` |
| `vm:` | `cachyos-vm` (bootstrap-from-scratch via pacstrap) |
| `deploy:` | `cachyos-vm-deploy`, `ov-cachyos` (operator workstation profile) |
| `local:` | `ov-cachyos` (kind:local template — the workstation layer-stack) |

## Composition by reference — nothing is vendored

This repo contains **no layers and no build-config of its own**. Everything is
pulled from `github.com/overthinkos/overthink` by **github reference**:

- every layer in `image.yml` / `deploy.yml` / `local.yml` is an
  `@github.com/overthinkos/overthink/layers/<name>:<tag>` ref;
- the shared build-config (`build.yml` — distro/builder/init, including the
  `cachyos` distro definition) and the `arch` base + `arch-builder` pair
  (`arch-base.yml`) are remote `include:`s in `overthink.yml`.

CachyOS is Arch-based, so `cachyos-pacstrap-builder` is `base: arch` and resolves
the `arch` base from the main repo's `arch-base.yml`. All references pin to a
single tag of the upstream repo, so a build is reproducible. There is exactly one
definition of every layer — no duplication.

## main ↔ cachyos coupling

The `cachyos` **base** image (`cachyos-base.yml`) is owned by THIS repo, but the
main repo's `versa` image is `base: cachyos`. So main's `overthink.yml`
remote-includes `cachyos-base.yml` from here:

```yaml
# main overthink.yml
include:
  - '@github.com/overthinkos/cachyos/cachyos-base.yml:<tag>'
```

This is a deliberate **main → cachyos** dependency (building `versa` on main
needs this repo reachable). It is NOT a resolution cycle: each side's `include:`
pulls a single file (main pulls `cachyos-base.yml`; this repo pulls
`build.yml` / `arch-base.yml`), and no included file re-enters the other repo's
`overthink.yml`. The image DAG is acyclic
(`versa → cachyos → docker.io/cachyos-v3`;
`cachyos-pacstrap-builder → arch → docker.io/archlinux`).

## Build

```bash
# Inside the submodule (the build verb defaults to overthink.yml):
ov image build cachyos
ov image build cachyos-pacstrap-builder

# From the parent overthink repo:
ov -C image/cachyos image build cachyos

# Standalone, against the published repo:
ov --repo overthinkos/cachyos image build cachyos
```

The first build resolves the upstream github references into `~/.cache/ov/repos/`
and materializes the referenced layers under `.build/_layers/`.

## Operator workstation profile (`ov-cachyos`)

Apply the kitchen-sink CachyOS dev profile to the current host:

```bash
ov -C image/cachyos update ov-cachyos
# or, anywhere:
ov --repo overthinkos/cachyos update ov-cachyos
```

(Before the 2026-05 migration this lived in the main repo and ran as
`ov update ov-cachyos` from the repo root.)

## Known limitation

`cachyos-pacstrap` and `cachyos-vm` (the pacstrap-from-scratch paths) are **not
currently usable**. The privileged pacstrap step fails in two pre-existing,
config/environment-inherent ways (both observed during R10):

1. An intermittent GPGME keyring/DB-sync error — `GPGME error: No data` /
   `failed to synchronize all databases (corrupted PGP signature)`.
2. When keyring init succeeds, a CachyOS `x86_64_v3` architecture rejection —
   `package architecture is not valid`. The `cachyos-v3` repos serve
   `x86_64_v3`-optimized packages (e.g. `linux-cachyos`) but the bootstrap
   pacman.conf never sets `Architecture = x86_64_v3`.

Both originate in the shared `build.yml` cachyos distro config (proven unchanged
by the submodule split — empty `git diff main` on `build.yml` + the pacstrap
runner), so they would fail identically from the old in-main location. Fixing
them is a separate upstream enhancement. The Docker-Hub-based `cachyos` base
(`cachyos-base.yml`) builds cleanly and is the recommended path.

## Requirements

A build of any image here fetches from the upstream repo, so it needs network
access and an `ov` recent enough to understand the config's schema version
(`ov` hard-fails with an "update ov" message if the config is newer than the
binary supports).

---
*Assisted-by: Claude*
