# Documentation gaps (operator view)

This file tracks **pain that new Linux operators hit** and what **this repository** will try to cover. It mirrors themes from the [FOSDEM 2026 community deck](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) (documentation gaps, lost issue history) and [n00q Reticulum Notes](https://n00q.net/blog/reticulum-notes/) (many knobs, scattered guides).

## Upstream / ecosystem

| Gap | What would help | This repo’s role |
|-----|-------------------|------------------|
| No single beginner quick-start on Linux | Distro-specific install + first config + first `rnsd` + `rnstatus` | [new-node-setup.md](guides/new-node-setup.md) + [README](../README.md) + [paths-and-example.md](config/paths-and-example.md) + CLI pages |
| Docs assume protocol depth | Tiered “day 0 / day 7 / developer” handbooks | We stay **operator tier**; link manual for depth |
| Historical issues / discussion hard to find | Curated archives or mirrors (verify URLs before linking) | [ecosystem.md](ecosystem.md) pointers only; no full mirror here |
| Config portability across implementations | Clear TOML vs legacy syntax story + conversion tooling | [config-format-portability.md](config/config-format-portability.md) |

## Linux-specific

| Gap | This repo’s role |
|-----|------------------|
| USB serial permissions | [rnode-usb.md](linux/rnode-usb.md) |
| systemd user/service examples for `rnsd` | Template in [new-node-setup.md](guides/new-node-setup.md) §10 — split into `docs/linux/systemd-rnsd.md` after distro validation |
| “No shared RNS instance” when running `rnstatus` | [rnstatus.md](cli/rnstatus.md) + [sample output](../samples/cli/rnstatus-no-shared-instance-1.2.5-Darwin.txt) |

## RF and compliance

Examples in the wild (events, blog decks) use specific frequencies and spreading factors. **Operators must validate** band, power, duty cycle, and antenna against local law. We label snippets as illustrative; see [interfaces.md](config/interfaces.md).

## How to use this file

When you fix a gap in the docs, add a short “resolved by …” line or remove the row. Keep links current or mark them stale with a date.
