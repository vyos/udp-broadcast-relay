# CLAUDE.md

## Project purpose

Tiny C daemon that listens on a UDP broadcast port and re-emits packets onto other interfaces preserving the original sender. Used on VyOS to relay LAN-discovery broadcasts (game LAN, DHCP-style flows) across interfaces, including ppp links. Ships as the `udp-broadcast-relay` Debian package with a `udp-broadcast-relay@.service` systemd unit.

## Tech stack

- Plain C. Single source file (`udp-broadcast-relay.c`).
- `Makefile` (no autotools). Debian packaging in `debian/`.
- License: GPL-2.0 (per `COPYING`).

## Build / test / run

```sh
make                       # gcc -O0 udp-broadcast-relay.c -o udp-broadcast-relay
dpkg-buildpackage -us -uc  # produces udp-broadcast-relay.deb
```

Runtime: `systemctl start udp-broadcast-relay@<id>.service` after configuring `/etc/default/udp-broadcast-relay`. PPP integration via `ppp-if.up-local`.

## Repository layout

- `udp-broadcast-relay.c` — daemon source.
- `udp-broadcast-relay.8` — man page.
- `udp-broadcast-relay.default`, `udp-broadcast-relay@.service`, `ppp-if.up-local` — configuration / integration files.
- `Makefile`, `debian/`.

## Cross-repo context

Listed in `VyOS-Networks/vyos-build-packages/repos.toml` — pulled into the ISO via `vyos/vyos-build`. Configured at runtime by `vyos-1x` op-mode/conf-mode scripts (e.g. `service broadcast-relay`).

## Conventions

- Default branch `current`. Active workflows: `cla-check.yml`, `trigger-rebuild-repo-package.yml` — wired into the rebuild-dispatch chain.
- Commit / PR title format: `component: T12345: description` (Phorge task ID at https://vyos.dev).
- Keep diffs against `nomeata/udp-broadcast-relay` minimal; the upstream branch is tracked as `upstream/master`.

## Mirror relationship

Mirror twin: `VyOS-Networks/udp-broadcast-relay`. Canonical side is here. Mirror pipeline is **not** wired up (no `pr-mirror-repo-sync.yml` workflow); cross-org sync handled manually if needed. The VyOS-Networks twin's default branch is the experimental `git-actions` (per relations doc §8.2) — ignore that side for canonical state.

## Notes for future contributors

- `Makefile` builds with `-O0` deliberately for debuggability; if you change CFLAGS, check the Debian package still passes lintian.
- Single file means changes touch the whole daemon; coordinate with `vyos-1x` if the CLI surface changes.

---

This file is mirrored on Confluence: [`vyos/udp-broadcast-relay`](https://internal.confluence.vyos.com/wiki/spaces/VYOS/pages/817889537). The Confluence page also carries the per-repo audit data (settings, workflows, secret counts, hygiene) that complements this CLAUDE.md. Edit either side; resync via the documentation pipeline.
