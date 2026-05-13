# rnid

**Version note:** Help text captured from `rnid` **1.2.5** (see sample file).

## Synopsis

`rnid` manages **Reticulum identities**: generate, import/export keys, print hashes, **announce** destinations, and sign/encrypt files.

## Prerequisites

- For network operations (`-a`, `-R`), a running shared RNS instance (`rnsd` or another program that initialized the stack with the same `--config`).

## Example

Generate a new identity (path must **not** already exist):

```bash
mkdir -p ~/.reticulum/storage/identities
rnid --generate ~/.reticulum/storage/identities/node.identity -v
```

Print identity and public key fingerprint:

```bash
rnid -i ~/.reticulum/storage/identities/node.identity -p
```

Show destination hashes for an aspect (example default aspect `rns.id`):

```bash
rnid -i ~/.reticulum/storage/identities/node.identity -H rns.id
```

Announce the default destination for that identity (requires `rnsd` running):

```bash
rnid -i ~/.reticulum/storage/identities/node.identity -a
```

## Sample output

- [`--help`](../../samples/cli/rnid-help-1.2.5-Darwin.txt)
- [Generate + `-p` + `-H rns.id` (illustrative)](../../samples/cli/rnid-print-identity-example-1.2.5.txt)

## Troubleshooting

| Symptom | What to check |
|---------|----------------|
| `already exists. Not overwriting` | Pick a new path or remove the file (careful: do not delete the only copy of a live key). |
| Hang or errors on `-a` / `-R` | Same `--config` as `rnsd`; confirm `rnstatus` works. |
| Wrong hashes vs peer | Aspect string must match what the application uses (see app docs). |

## See also

- [New node setup](../guides/new-node-setup.md)
- [Reticulum manual](https://reticulum.network/manual/index.html)
