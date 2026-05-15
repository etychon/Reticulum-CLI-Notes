# Diagram assets

Optional **SVG** exports for figures that are hard to read as inline Mermaid alone. **Source of truth** remains the Mermaid blocks in the linked Markdown pages.

## Layout

| Path | Purpose |
|------|---------|
| `*.svg` | Committed exports for the visual index |
| `source/*.mmd` | Mermaid CLI input (optional; keep in sync with doc blocks) |

## Regenerate

See [diagrams-style.md](../../concepts/diagrams-style.md). Example:

```bash
npx --yes @mermaid-js/mermaid-cli -i docs/assets/diagrams/source/one-node-many-destinations.mmd \
  -o docs/assets/diagrams/one-node-many-destinations.svg
```

## Index

Full list with captions: [visual-index.md](../../concepts/visual-index.md).
