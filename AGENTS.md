# Agent and contributor notes

This repo is evidence-first operator documentation for Reticulum on Linux (and related tooling). When adding or changing material:

1. **Pin the stack** — At the top of a changed doc, note `RNS` / `rnsd` version (from `rnsd --version` or `python -c "import RNS; print(RNS.__version__)"`) and OS/distro.
2. **Capture CLI text** — Prefer real transcripts: `tool --help 2>&1 | tee samples/cli/<name>.txt`. Use the naming convention in [samples/cli/README.md](samples/cli/README.md).
3. **Redact** — Remove or replace hostnames, home paths, identity hashes, and RF parameters that could identify a live deployment unless they are clearly dummy values.
4. **Link upstream** — Each page should point to the relevant [Reticulum manual](https://reticulum.network/manual/index.html) chapter; cite [n00q Reticulum Notes](https://n00q.net/blog/reticulum-notes/) or the [FOSDEM 2026 community deck](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) when the content derives from them.
5. **No secrets** — Never commit API keys, `rpc_key` values, or real `remote_management_allowed` hashes.
6. **RF and compliance** — Example frequencies and power are illustrative only; operators must follow local regulations.
7. **Diagrams** — When you change protocol or operator behaviour described in docs, update the related Mermaid figure and an entry in [docs/concepts/visual-index.md](docs/concepts/visual-index.md). Follow [docs/concepts/diagrams-style.md](docs/concepts/diagrams-style.md) (no colour fills, redacted hashes).
