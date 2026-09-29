# pod-selkies-core

The `selkies-core` candy of the OpenCharly candy library, as a standalone repo
(the candy de-submodule cutover, kind-prefixed naming). It is the single shared
streaming spine for every selkies flavor — the pixelflux transport plus the shared
desktop fixings consumed by both the labwc and KDE Plasma flavors.

## What it provides

Composes the compositor-agnostic core of the selkies streaming desktop: the
pixelflux WebRTC transport (`pod-selkies`), audio (`pod-pipewire`), Chrome + CDP,
fonts, the `wl-*` pixelflux screenshot/record/overlay tooling, accessibility
introspection, an X terminal, the terminal-recording stack, and `sshd`. There is
no compositor here and no compositor-specific panel/notifier — those live in the
per-flavor metalayer.

On top of the composed candies, `selkies-core` owns the supervised
`[program:chrome]` launcher, keeping the browser alive for both flavors where the
former per-flavor fire-once autostart only ever passed by racing Chrome's brief
alive window.

| Property | Value |
|---|---|
| Service | `chrome` (`~/.local/bin/chrome-wrapper`, `restart: always`, `wait_for` the `wayland-0` socket, priority 30) |
| Requires | `plugin-cdp` (the `cdp:` verb), `plugin-wl` (the `wl:` verb) |
| Composes | `pod-selkies`, `pod-pipewire`, `layer-chrome`, `pod-chrome-cdp`, `layer-desktop-fonts`, `layer-wl-tools`, `layer-wl-screenshot-pixelflux`, `layer-wl-overlay`, `layer-wl-record-pixelflux`, `layer-a11y-tools`, `layer-xterm`, `layer-tmux`, `layer-asciinema`, `layer-fastfetch`, `pod-sshd` |
| Flavors | `selkies-desktop` (labwc) = core + labwc + waybar-labwc + swaync + pavucontrol; `selkies-kde-desktop` (KDE) = core + kde-selkies |

## How to use it

It is the base of a flavor metalayer rather than a standalone deploy:

```yaml
my-flavor:
  candy:
    base: cachyos
    candy:
      - '@github.com/opencharly/pod-selkies-core:<tag>'
      - '@github.com/opencharly/pod-labwc:<tag>'
```

```bash
charly box build my-flavor
charly config my-flavor
charly start my-flavor
```

## Layout

- `charly.yml` — the `selkies-core:` candy entity (description, `require`,
  `candy`, `service`, `plan`) plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:selkies-core` — the composition, the Chrome
  supervision, and what is shared vs flavor-specific.
- `/charly-selkies:selkies` — the pixelflux transport at the heart of the core.
- `/charly-selkies:selkies-desktop-layer` / `/charly-selkies:selkies-kde-desktop`
  — the two flavor metalayers.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
