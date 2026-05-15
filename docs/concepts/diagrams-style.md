# Diagram style guide

**Version note:** Conventions for this repository’s Mermaid and SVG figures (operator docs, not normative protocol spec).

## Purpose

Diagrams explain Reticulum **behaviour** for visual learners. Normative detail stays in the [Reticulum manual](https://reticulum.network/manual/index.html). Each figure has a **Mermaid source** in the page that owns the concept; a few dense layouts also ship as SVG under [../assets/diagrams/](../assets/diagrams/).

## Rules

| Rule | Detail |
|------|--------|
| **Cast** | Use `NodeA`, `NodeB`, `TransportMid` in generic diagrams. Use **Emmanuel** / **Chris** only on [mesh-cli-examples.md](../guides/mesh-cli-examples.md) where the lab table already names them. |
| **Hashes** | Truncated placeholders in figures (`a1b2…c3d4`, `D1`, `D2`). Full lab hashes belong only in that lab page and its tables. |
| **Colours** | Do **not** use Mermaid `style`, `classDef`, or hard-coded fill colours (poor dark-mode rendering on GitHub and many viewers). |
| **Node IDs** | `camelCase` or `PascalCase`; no spaces in Mermaid node IDs. |
| **Placement** | Put the figure **immediately after** the paragraph it explains, with a one-line **caption** in bold below the block. |
| **Dual publish** | For crowded layouts: Mermaid in `.md` plus optional `docs/assets/diagrams/<name>.svg` referenced with `![alt text](../assets/diagrams/<name>.svg)`. Keep committed SVG count small. |
| **Secrets** | Never put real `rpc_key`, private keys, or production identity material in diagrams. |
| **Upstream** | Link the relevant manual section when a diagram summarises protocol behaviour. |

## Exporting SVG (optional)

From a `.mmd` file or stdin:

```bash
npx --yes @mermaid-js/mermaid-cli -i docs/assets/diagrams/source/one-node-many-destinations.mmd \
  -o docs/assets/diagrams/one-node-many-destinations.svg
```

After export, open the SVG in a viewer and check legibility. Update [visual-index.md](visual-index.md) when adding or renaming figures.

## See also

- [Visual index](visual-index.md) — catalog of all figures
- [AGENTS.md](../../AGENTS.md) — contributor rules including diagram updates
