# Reticulum CLI notes

Operator-focused notes for the [Reticulum Network Stack](https://reticulum.network/): CLI usage, configuration paths, Linux and USB RNode bring-up, and captured command output. This complements the [official manual](https://reticulum.network/manual/index.html); it does not replace the protocol specification or upstream docs.

This repository is **community-maintained documentation**, not an official Reticulum project publication. Trademarks and the stack itself belong to their respective owners.

## Upstream vs this repo

| Upstream | This repo |
|----------|-----------|
| Manual, wiki, per-app settings | Curated checklists, Linux serial/USB friction, CLI transcripts |
| Full interface and API reference | Minimal “first link” examples with compliance disclaimers |
| Single source of truth for behavior | Version-pinned snapshots; re-run captures when upgrading |

We avoid long verbatim copies of upstream text. Summarize, link, and add **what you see on a real system** under [samples/](samples/).

## Starting points we build on

- [Reticulum Notes (n00q)](https://n00q.net/blog/reticulum-notes/) — Ecosystem overview, RNode on Linux tips, GrapheneOS notes, link hub.
- [Reticulum: What’s Next? (FOSDEM 2026, PDF)](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) — Implementation landscape, config format discussion, documentation gaps, community tooling.

## Governance and distribution (neutral pointer)

Licensing and upstream distribution channels have evolved. Some community builds and forks target package-manager-friendly licenses or alternate daemons. This repository **does not** endorse a particular stack; it documents commands and config in a way that works for **whichever build you run** (stock Python RNS, RetiNet, `rnsd-rs`, and so on). When behavior differs, we add a subsection and separate samples.

## Repository layout

| Path | Contents |
|------|----------|
| [docs/concepts/](docs/concepts/) | Behaviour notes (destinations, announces, …) |
| [docs/guides/](docs/guides/) | End-to-end operator flows (new node, …) |
| [docs/linux/](docs/linux/) | Serial/USB RNode, permissions, systemd hints |
| [docs/cli/](docs/cli/) | One page per CLI tool, linked to `samples/cli/` |
| [docs/config/](docs/config/) | Config paths, example config, interface recipes, TOML notes |
| [docs/ecosystem.md](docs/ecosystem.md) | Pointers to apps and alternate implementations |
| [docs/gaps.md](docs/gaps.md) | What is still missing (for this repo and upstream) |
| [samples/cli/](samples/cli/) | Plain-text `--help` and runtime captures |
| [samples/config/](samples/config/) | Redacted config snippets |
| [AGENTS.md](AGENTS.md) | Rules for contributors and automation |

## Tested stack (fill in as you validate)

| Field | Example |
|-------|---------|
| OS | Debian 13 / Fedora 42 / … |
| RNS / daemon | e.g. `rnsd 1.2.5` from `pip install rns` |
| Hardware | RNode on `/dev/ttyACM0`, TCP only, … |

Initial CLI help samples in this repo were captured from **RNS 1.2.5** on **Darwin** (same `rnsd`/`rnstatus` help text as on Linux). Add Linux-specific transcripts when you can.

## Quick path for a new Linux operator

1. Follow **[docs/guides/new-node-setup.md](docs/guides/new-node-setup.md)** (install, identity, `rnsd`, probes, `rnsh` / `rnx` / `rncp`, Nomad Network, systemd, troubleshooting).
2. Read [docs/config/paths-and-example.md](docs/config/paths-and-example.md) if you need config search order and `rnsd --exampleconfig`.
3. If you use USB RNode: [docs/linux/rnode-usb.md](docs/linux/rnode-usb.md).
4. Command reference: [docs/cli/](docs/cli/) (for example [rnsd](docs/cli/rnsd.md), [rnstatus](docs/cli/rnstatus.md), [rnid](docs/cli/rnid.md), [rncp](docs/cli/rncp.md)).
5. How listening relates to announces: [docs/concepts/destinations-announces-listeners.md](docs/concepts/destinations-announces-listeners.md).

## See also

- [Reticulum manual — Using Reticulum on your system](https://reticulum.network/manual/using.html)
- [Reticulum GitHub wiki](https://github.com/markqvist/Reticulum/wiki/)

## Contributing and security

- [CONTRIBUTING.md](CONTRIBUTING.md) — how to submit changes and samples.
- [SECURITY.md](SECURITY.md) — reporting sensitive leaks in this repo.

## License

Text and samples in this repository are licensed under the [MIT License](LICENSE) unless individual files state otherwise.

## Publishing on GitHub

1. Create a new **empty** repository on GitHub (no README/license template, or you will need to reconcile histories).
2. Add the remote and push: `git remote add origin https://github.com/<you>/<repo>.git` then `git push -u origin main`.
3. In the repo **Settings → General**, optionally enable **Private vulnerability reporting** (aligns with [SECURITY.md](SECURITY.md)).
4. Review **Settings → Actions** if you add workflows later; this repo ships without CI by default.
