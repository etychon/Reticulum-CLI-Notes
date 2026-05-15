# Contributing

Thank you for helping improve these operator notes.

## What to contribute

- Corrections when behaviour or flags change between RNS versions.
- Linux-specific CLI captures under `samples/cli/` (see [samples/cli/README.md](samples/cli/README.md)).
- New troubleshooting rows or short guides that stay grounded in reproducible steps.

## Before you open a pull request

1. **No secrets** — Do not commit real `rpc_key` values, private keys, live identity material, or home-specific paths you are not comfortable publishing.
2. **Redact samples** — Replace hostnames, usernames in prompts, and sensitive hashes in transcripts unless they are clearly disposable test values.
3. **Pin versions** — Note which `rnsd --version` / OS you captured against in the sample header or the doc.
4. **Upstream first** — Protocol depth and normative behaviour belong in the [Reticulum manual](https://reticulum.network/manual/index.html); here we add operator evidence and checklists.

## Style

- Match the existing Markdown structure in nearby files.
- Prefer linking to the official manual over copying large excerpts.

## Diagrams

- Add or update **Mermaid** in the page that owns the concept; see [docs/concepts/diagrams-style.md](docs/concepts/diagrams-style.md).
- Register new figures in [docs/concepts/visual-index.md](docs/concepts/visual-index.md).
- Optional SVG export: `npx --yes @mermaid-js/mermaid-cli -i docs/assets/diagrams/source/<name>.mmd -o docs/assets/diagrams/<name>.svg`

## Licensing

By contributing, you agree your contributions are licensed under the same terms as this repository ([LICENSE](LICENSE)).
