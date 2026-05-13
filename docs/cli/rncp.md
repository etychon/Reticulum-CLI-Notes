# rncp

**Version note:** Help text captured from `rncp` **1.2.5** (see sample file).

## Synopsis

`rncp` is the **Reticulum file transfer** utility: send a file to a destination hash, **listen** for incoming transfers, or **fetch** from a remote listener (when enabled).

## Prerequisites

- `rnsd` (or another shared RNS instance) with working interfaces.
- For anything except closed labs, **avoid `--no-auth`**: use `-a` or `~/.rncp/allowed_identities` per `rncp --help`.

## Example

**Listener** (receiver; identity path relative to home as in your example):

```bash
rncp -l -v -i ~/.reticulum/storage/identities/default --no-auth
```

**Typical log shape** (one completed resource):

```text
rncp listening on <destination_hash>
Starting resource transfer <resource_id> from <sender_identity_hash>
<resource_id/chunk_or_part_id> completed
```

Full transcript: [rncp-listen-verbose-raspberrypi4-example.txt](../../samples/cli/rncp-listen-verbose-raspberrypi4-example.txt).

**Sender** (opposite terminal; paths illustrative):

```bash
rncp myfile.bin <receiver_destination_hash>
```

Use `rncp -p -i ~/.reticulum/storage/identities/default` (or your identity) to print listener identity/destination info before testing.

## Sample output

- [`--help`](../../samples/cli/rncp-help-1.2.5-Darwin.txt)
- [Listen mode, verbose (Raspberry Pi 4)](../../samples/cli/rncp-listen-verbose-raspberrypi4-example.txt)

## Troubleshooting

| Symptom | What to check |
|---------|----------------|
| Hangs before “listening” | `rnsd` running; same `--config`; identity path readable. |
| Sender never connects | Destination hash is the **listener’s rncp destination** (`-p` on listener); path exists both ways. |
| Auth rejected | Drop `--no-auth`; add `-a <hash>` or identities file. |

## See also

- [New node setup](../guides/new-node-setup.md)
- [Reticulum manual](https://reticulum.network/manual/index.html)
