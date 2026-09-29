# AGENTS.md — pod-selkies-core

Standalone candy repo for the `selkies-core` candy — the compositor-agnostic core
of the selkies streaming desktop: the pixelflux transport plus the shared desktop
fixings consumed by both flavors (labwc and KDE Plasma), and the supervised
Chrome launcher both flavors share. The candy lives in `charly.yml` at the repo
root. There is no source tree.

Canonical files:

- `charly.yml` — the `selkies-core:` candy entity (description, `require`,
  `candy`, `service`, `plan`) and its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:selkies-core` — the owning skill: the composition, the Chrome
  supervision, and what is shared vs flavor-specific in the selkies stack. Load
  before editing, building, deploying, or troubleshooting this candy.
- `/charly-selkies:selkies` — the pixelflux transport the core composes.
- `/charly-selkies:selkies-desktop-layer` / `/charly-selkies:selkies-kde-desktop`
  — the two flavor metalayers that consume this core.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework and the `cdp:` / `wl:` probe
  verbs the plan uses (`charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the baked `[program:chrome]` supervisor
  entry and its `chrome-wrapper` exec, the running `chrome` service, that Chrome
  is alive and CDP-responsive 25s after deploy, the launch flags, the maximized
  window, and a non-uniform streamed frame.

## Modify this repo

- Edit the `selkies-core:` candy entity in `charly.yml`; the `skill:` entity in
  the same file is the owning skill's source — a candy change and its skill change
  land together.
- `selkies-core` owns the supervised `[program:chrome]` service; the per-flavor
  compositor autostarts do NOT launch Chrome. Keep the `wait_for` `wayland-0`
  precondition and the `restart`/`start_sec` fields in step — they are what keep
  Chrome alive past the nested-compositor startup race.
- The `require:` of `plugin-cdp` / `plugin-wl` is load-bearing: it puts the
  out-of-tree `cdp:` / `wl:` verbs in every composing image's scanned candy set.
- Keep the composed candy pins in step with their released tags.
- The `skill:` entity is the source for `/charly-selkies:selkies-core`; never edit
  the generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
