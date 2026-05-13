# Ecosystem pointers

**Last curated:** 2026-05-13 (manual review of linked sources; implementations change frequently.)

This page lists **where to look next**, not full tutorials. It merges themes from [n00q: Reticulum Notes](https://n00q.net/blog/reticulum-notes/) and [Reticulum: What’s Next? (FOSDEM 2026, PDF)](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf).

## Operator guides in this repo

- [New node: initial configuration and services](guides/new-node-setup.md) — install, identity, `rnsd`, probes, `rnsh` / `rnx`, Nomad Network, systemd template, troubleshooting.
- [Mesh CLI worked examples](guides/mesh-cli-examples.md) — LoRa lab: `rnid -p`, peer hash table, `rnpath`, `rnprobe`, `rnsh` (with sample transcript).

## Core stack and spec

- [Reticulum website](https://reticulum.network/)
- [Reticulum manual](https://reticulum.network/manual/index.html)
- [Roadmap (upstream repo)](https://github.com/markqvist/Reticulum/blob/master/Roadmap.md)
- [Reticulum GitHub wiki](https://github.com/markqvist/Reticulum/wiki/) — includes [Awesome Reticulum](https://github.com/markqvist/Reticulum/wiki/Awesome-Reticulum)

## Protocols and applications (n00q-style map)

| Area | Examples |
|------|------------|
| Messaging | LXMF; clients such as Sideband, MeshChat |
| Voice | LXST |
| Nomad Network | Micron / “micro WWW” over Reticulum |
| Shell / CLI | `rnsh`, `rnx`, `rncp`, and related utilities |
| Radio hardware | RNode firmware and boards |

## Implementations (FOSDEM matrix, condensed)

Alternate stacks exist for Python parity, Rust, Go, embedded C++, and research Zig work. Names and maturity change; check each project’s README for **compatibility**, **license**, and **config format** before migrating.

**Practical note:** If you need configs to move between daemons, read [config-format-portability.md](config/config-format-portability.md).

## Community and clients called out in FOSDEM 2026

The deck mentions threaded chat (Zulip), native Android clients, Rust daemon work, telemetry experiments, and config conversion toward standard TOML. Treat links inside the PDF as authoritative for **event-specific** demo hosts and QR flows.

## Meshtastic comparison (context only)

n00q links an independent comparison: [Comparing Reticulum and Meshtastic](https://blog.erethon.com/blog/2024/01/31/comparing-reticulum-and-meshtastic/).

## Micron tooling

From n00q: [md2mu](https://github.com/randogoth/md2mu), [Micron Playground](https://rfnexus.github.io/micron-parser-js/).

## See also

- [docs/gaps.md](gaps.md) — what this repo still needs to document well.
