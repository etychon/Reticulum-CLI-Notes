# rnprobe

**Version note:** Help text captured from `rnprobe` **1.2.5** (see sample file).

## Synopsis

`rnprobe` sends probe traffic to a destination (by full name or destination hash) to measure reachability and latency characteristics.

## Prerequisites

Working path / reachability to the destination; shared RNS instance running with appropriate interfaces.

## Example

```bash
rnprobe <destination_hash>
rnprobe example.dummy.destination <hash>
```

## Sample output

- [`--help`](../../samples/cli/rnprobe-help-1.2.5-Darwin.txt)

Capture a real probe transcript separately; probes generate traffic—use only on networks you operate or have permission to test.

## Troubleshooting

- **No response:** Confirm destination hash, outbound interfaces, and firewall rules for TCP/UDP transport where applicable.

## See also

- [Reticulum manual](https://reticulum.network/manual/index.html)
- [n00q — Reticulum Notes](https://n00q.net/blog/reticulum-notes/)
