# rnodeconf

**Version note:** Help text from **RNS 1.2.5** (`rnodeconf` ships with `pip install rns`).

## Synopsis

`rnodeconf` configures RNode hardware: firmware **autoinstall**, **update**, **flash**, Bluetooth/WiFi/display options, and on-device LoRa parameters. Use it **before** relying on `RNodeInterface` in `rnsd`.

## Prerequisites

- USB serial access to the board ([rnode-usb.md](../linux/rnode-usb.md)).
- **`rnsd` must not be running** on that port during flash or `-i` (serial conflict). See [rnode-lora-install.md §3](../linux/rnode-lora-install.md#stop-rnsd-and-release-the-serial-port-first).
- Full guide: [rnode-lora-install.md](../linux/rnode-lora-install.md).

## Example

Autoinstall firmware on the device at `port`:

```bash
rnodeconf -a /dev/serial/by-id/usb-…-if00
```

Device info (verification):

```bash
rnodeconf -i /dev/serial/by-id/usb-…-if00
```

Update firmware:

```bash
rnodeconf -u /dev/serial/by-id/usb-…-if00
```

## Sample output

- [Successful `rnodeconf -i` (Heltec LoRa32 v4)](../../samples/cli/rnodeconf-info-heltec-lora32-v4-example.txt)

Capture your own `rnodeconf -i` on your hardware and store under `samples/cli/` when you contribute transcripts.

## Troubleshooting

| Symptom | What to check |
|---------|----------------|
| Cannot open port | **`rnsd` stopped?** `lsof` on by-id path; permissions; data USB cable |
| Flash error, “lower baud rate” example | Port not busy first; then `--baud-flash 115200` (default flash baud is 921600) |
| Autoinstall unsupported | Board family; see `rnodeconf --help` and [unsigned.io guides](https://unsigned.io/guides/) |

## See also

- [Install LoRa RNode](../linux/rnode-lora-install.md)
- [Reticulum manual — interfaces](https://reticulum.network/manual/interfaces.html)
