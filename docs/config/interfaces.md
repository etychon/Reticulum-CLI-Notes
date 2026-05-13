# Interface recipes

**Legal and safety:** RF examples below are **illustrative only**. You are responsible for lawful frequency, power, bandwidth, and antenna use. Event-specific hosts (for example FOSDEM demo infrastructure) may not be reachable year-round.

## Python RNS (`rnsd --exampleconfig` style, 1.2.5)

Upstream’s generated example uses **configobj** syntax: `Yes` / `No`, nested `[[Interface Name]]` sections, and keys such as `enabled = no` on interfaces in the sample output.

### TCP client (template)

See the verbatim upstream block in [samples/config/tcp-client-interface-snippet-rns-1.2.5.txt](../../samples/config/tcp-client-interface-snippet-rns-1.2.5.txt). Replace `target_host` / `target_port` with your peer or test server.

### RNode / LoRa (template)

See [samples/config/rnode-interface-snippet-rns-1.2.5.txt](../../samples/config/rnode-interface-snippet-rns-1.2.5.txt). Adjust `port`, `frequency`, spreading factor, bandwidth, and `txpower` for your hardware and jurisdiction.

The [FOSDEM 2026 slides](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) show an **EU868** example (`864.2 MHz`, SF10, etc.) for a conference mesh; treat that as a **lab/event** reference, not a universal preset.

## Alternate implementations (TOML)

Other daemons may require **strict TOML** (`true` / `false`, `[[interfaces]]` tables with `name = "..."`). See [config-format-portability.md](config-format-portability.md).

## USB path on Linux

If you use USB serial, see [rnode-usb.md](../linux/rnode-usb.md).

## See also

- [Reticulum manual — Configuring interfaces](https://reticulum.network/manual/interfaces.html)
- [n00q — RNode on Linux](https://n00q.net/blog/reticulum-notes/)
