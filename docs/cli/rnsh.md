# rnsh

**Version note:** Help text captured from `rnsh` **1.2.5** (see sample file).

## Synopsis

`rnsh` is the **Reticulum remote shell** utility: connect to a peer destination or listen in server mode for incoming shell sessions. It is listed among the “command-line nerd tools” in [n00q Reticulum Notes](https://n00q.net/blog/reticulum-notes/).

## Prerequisites

- Identities and destination hashes configured for your use case.
- Understanding that remote shell access is high risk: use `-a` / allowed identity files, avoid `--no-auth` except in closed lab setups.

## Example

Listen with a specific shell (server mode):

```bash
rnsh -l -- /bin/bash --login
```

Connect to a destination:

```bash
rnsh <destination_hash> -- ls -la /tmp
```

## Sample output

- [`--help`](../../samples/cli/rnsh-help-1.2.5-Darwin.txt)

## Troubleshooting

- **Auth failures:** Check `~/.rnsh/allowed_identities` or `~/.config/rnsh/allowed_identities` and `-a` hashes.
- **Timeout:** Adjust `-w` to suit high-latency links.

## See also

- [New node setup](../guides/new-node-setup.md)
- Upstream `rnsh` documentation / wiki (verify current install package name)
- [Reticulum manual](https://reticulum.network/manual/index.html)
