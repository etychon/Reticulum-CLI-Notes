# Visual index

Catalog of diagrams in this repository. **Mermaid** renders on GitHub and many Markdown viewers; **SVG** copies are optional for dense figures.

Style rules: [diagrams-style.md](diagrams-style.md).

---

## Addresses and destinations

| Figure | Page | Format |
|--------|------|--------|
| One node, many destinations (identity vs apps) | [destinations-announces-listeners.md](destinations-announces-listeners.md) | Mermaid + [SVG](../assets/diagrams/one-node-many-destinations.svg) |
| Not DNS vs Reticulum naming | [destinations-announces-listeners.md](destinations-announces-listeners.md) | Mermaid |
| Dotted name to destination hash | [destinations-announces-listeners.md](destinations-announces-listeners.md) | Mermaid |
| Lab cheat sheet → destination boxes | [mesh-cli-examples.md](../guides/mesh-cli-examples.md) | Mermaid |

---

## Routing and transport

| Figure | Page | Format |
|--------|------|--------|
| Proactive vs reactive routing | [routing-paths-and-announces.md](routing-paths-and-announces.md) | Mermaid |
| Three-node LoRa mesh (next hop only) | [routing-paths-and-announces.md](routing-paths-and-announces.md) | Mermaid + [SVG](../assets/diagrams/reactive-routing-mesh.svg) |
| Announce teaches next hop | [routing-paths-and-announces.md](routing-paths-and-announces.md) | Mermaid |
| Partial convergence (slow vs fast segment) | [routing-paths-and-announces.md](routing-paths-and-announces.md) | Mermaid |
| Path request flow | [routing-paths-and-announces.md](routing-paths-and-announces.md) | Mermaid |

---

## Node stack and interfaces

| Figure | Page | Format |
|--------|------|--------|
| Software stack (apps → rnsd → interfaces) | [paths-and-example.md](../config/paths-and-example.md) | Mermaid |
| Host → RNode → LoRa RF | [rnode-lora-install.md](../linux/rnode-lora-install.md) | Mermaid |
| USB permissions and serial path | [rnode-usb.md](../linux/rnode-usb.md) | Mermaid |
| Interface types into one instance | [interfaces.md](../config/interfaces.md) | Mermaid |

---

## Operator guides

| Figure | Page | Format |
|--------|------|--------|
| New node setup flow | [new-node-setup.md](../guides/new-node-setup.md) | Mermaid |
| rnprobe troubleshooting decision tree | [new-node-setup.md](../guides/new-node-setup.md) | Mermaid |
| LoRa lab CLI sequence | [mesh-cli-examples.md](../guides/mesh-cli-examples.md) | Mermaid |
| rnprobe two arguments | [mesh-cli-examples.md](../guides/mesh-cli-examples.md) | Mermaid |

---

## CLI at a glance

| Tool | Page |
|------|------|
| `rnsd` | [rnsd.md](../cli/rnsd.md) |
| `rnstatus` | [rnstatus.md](../cli/rnstatus.md) |
| `rnid` | [rnid.md](../cli/rnid.md) |
| `rnpath` | [rnpath.md](../cli/rnpath.md) |
| `rnprobe` | [rnprobe.md](../cli/rnprobe.md) |
| `rnsh` | [rnsh.md](../cli/rnsh.md) |
| `rncp` | [rncp.md](../cli/rncp.md) |
| `rnodeconf` | [rnodeconf.md](../cli/rnodeconf.md) |

---

## See also

- [Routing: paths, announces, and reactive reachability](routing-paths-and-announces.md)
- [Destinations, listeners, and announces](destinations-announces-listeners.md)
- [Reticulum manual — Understanding](https://reticulum.network/manual/understanding.html)
