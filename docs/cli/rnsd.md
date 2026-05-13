# rnsd

**Version note:** Help text captured from `rnsd` **1.2.5** (see sample file).

## Synopsis

`rnsd` runs the Reticulum Network Stack as a daemon (foreground or service mode). Other local programs typically attach to the **shared instance** it creates.

## Prerequisites

- A valid Reticulum config directory (see [paths-and-example.md](../config/paths-and-example.md)).
- Write access to the config and storage paths configured for that instance.

## Example

Print the built-in verbose example configuration (large):

```bash
rnsd --exampleconfig | less
```

Run the daemon in the foreground with verbose logging:

```bash
rnsd -v
```

Use an alternate config root:

```bash
rnsd --config /path/to/reticulum/dir -v
```

## Sample output

- [`--help`](../../samples/cli/rnsd-help-1.2.5-Darwin.txt) (regenerate on Linux with `…-Linux.txt` per [samples/cli/README.md](../../samples/cli/README.md))
- Opening of [`--exampleconfig`](../../samples/config/rnsd-exampleconfig-head-1.2.5.txt)

## Troubleshooting

- **Nothing listens / other tools hang:** Confirm only one shared instance owns interfaces, or adjust `share_instance` / `instance_name` per the example config comments.
- **Wrong config picked up:** Check search order in [paths-and-example.md](../config/paths-and-example.md).

## See also

- [New node setup](../guides/new-node-setup.md)
- [Reticulum manual — Using Reticulum on your system](https://reticulum.network/manual/using.html)
- [n00q — Reticulum Notes](https://n00q.net/blog/reticulum-notes/) (ecosystem context)
- [FOSDEM 2026 slides — performance / implementations](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf)
