# rnx

**Version note:** Help text captured from `rnx` **1.2.5** (see sample file).

## Synopsis

`rnx` is the **Reticulum remote execution** utility: run a command on a peer (client) or accept jobs (`-l` listen). It complements `rnsh` (interactive shell) with a request/result style.

## Prerequisites

Shared RNS instance, reachable paths, and **explicit authorization** on the listener (`-a` allowed identity hashes). Avoid `-n` / `--noauth` outside closed labs.

## Example

Listener (separate terminal from `rnsd`; identity path optional if defaults apply):

```bash
rnx -l -i ~/.reticulum/storage/identities/node.identity -a <initiator_identity_hash>
```

Client:

```bash
rnx <listener_destination_hash> uname -a
```

## Sample output

- [`--help`](../../samples/cli/rnx-help-1.2.5-Darwin.txt)

## Troubleshooting

| Symptom | What to check |
|---------|----------------|
| Timeouts | Latency, `-w` / `-W`, interface reachability, firewalls. |
| Auth rejected | Listener `-a` hashes; initiator identity path `-i`. |

## See also

- [rnsh.md](rnsh.md)
- [New node setup](../guides/new-node-setup.md)
- [Reticulum manual](https://reticulum.network/manual/index.html)
