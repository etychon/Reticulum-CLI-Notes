# Install a LoRa RNode (USB) on Linux

**Version note:** **`rnodeconf`** and **`RNodeInterface`** as shipped with **RNS 1.2.x** (`pip install rns`). Board families and `rnodeconf` options change over time—confirm with `rnodeconf --help` on your install.

**Legal:** Frequencies, power, duty cycle, and antennas must comply with **your** jurisdiction. The example below uses **869.525 MHz** (EU868-style planning); do not copy RF values blindly.

## Overview

1. Connect the board over USB and find a stable **`port`** path.
2. **Stop `rnsd`** (and anything else using that serial device), then install or update **RNode firmware** with **`rnodeconf`**.
3. Add an **`[[RNode LoRa Interface]]`** block to `~/.reticulum/config` (keep `enabled = no` until firmware verifies).
4. **Start `rnsd`** and confirm the interface comes up without `Could not detect device`.

**Order matters:** firmware and `rnodeconf -i` run **before** `rnsd` owns the port. Running both at once causes serial conflicts (see §3).

USB permissions and cable issues: [rnode-usb.md](rnode-usb.md).

**Diagrams:** [visual index](../concepts/visual-index.md)

```mermaid
flowchart LR
  Host[Linux host rnsd]
  USB[USB serial port]
  RNode[RNode MCU and LoRa radio]
  RF[LoRa RF to peers]
  Host --> USB --> RNode --> RF
```

**Figure: host to air interface path**

## 1. Prerequisites

- Reticulum venv with **`rns`** on `PATH` (see [new-node-setup.md §1](../guides/new-node-setup.md#1-install-the-stack)).
- User can access the serial device (udev / `dialout` / `setfacl`—see [rnode-usb.md](rnode-usb.md)).
- **`rnodeconf`** is installed with `rns` (same `bin` directory as `rnsd`).

## 2. Find the serial port

Prefer a **stable by-id path** so the device name does not change when you plug into another USB port:

```bash
ls -l /dev/serial/by-id/
ls -l /dev/ttyACM* /dev/ttyUSB* 2>/dev/null
```

Example (ESP32-class boards often show **Espressif** in the name):

```text
/dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_A4:CB:8F:A7:2C:68-if00 -> ../../ttyACM0
```

Use the full **`/dev/serial/by-id/...`** path in config, not only `ttyACM0`.

## 3. Firmware installation and verification

`rnodeconf` can **autoinstall**, **flash**, and **update** firmware on supported hardware. Run it against the serial **port** (by-id path recommended).

### Stop `rnsd` (and release the serial port) first

**Do not run firmware install or `rnodeconf -i` while `rnsd` is running** (or while any other program has the RNode serial port open). Reticulum’s `RNodeInterface` opens the same `/dev/serial/by-id/...` device; only one process can use it at a time.

If `rnsd` already has that interface enabled in `~/.reticulum/config`, it will grab the port on startup and **`rnodeconf` will fail** to flash or probe—often with misleading errors (see below).

**Before `rnodeconf -a`, `-u`, `-f`, or `-i`:**

1. Stop the daemon:
   - Foreground: **Ctrl+C** in the terminal where `rnsd -v` is running.
   - systemd user service: `systemctl --user stop rnsd.service` (adjust unit name if yours differs).
2. Confirm nothing else holds the port:

```bash
# Replace with your by-id path
PORT=/dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_A4:CB:8F:A7:2C:68-if00
lsof "$PORT" 2>/dev/null || fuser -v "$PORT" 2>/dev/null
```

If a process is still listed, stop it before continuing.

3. For a **first-time firmware install**, set `enabled = no` on the `[[RNode LoRa Interface]]` block in config (or omit the block) until **`rnodeconf -i`** succeeds—then enable the interface and start `rnsd` (§4–§5).

Community reports describe the same class of problem: a running Reticulum config that opens the board’s port can block firmware tooling from finishing ([RNode_Firmware issue #125](https://github.com/markqvist/RNode_Firmware/issues/125)).

### Autoinstall (first-time / blank board)

```bash
~/.venvs/reticulum/bin/rnodeconf -a /dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_A4:CB:8F:A7:2C:68-if00
```

Replace the path with yours. Follow on-screen prompts; the device may reboot. Unplug/replug if the port disappears.

### Show device info (verification)

With **`rnsd` stopped** (§3), run `-i` on your port (`ttyACM0` or a **by-id** path):

```bash
~/.venvs/reticulum/bin/rnodeconf -i /dev/ttyACM0
# preferred: ~/.venvs/reticulum/bin/rnodeconf -i /dev/serial/by-id/...
```

You should see the port open, **Device connected**, EEPROM/signature checks, then a **Device info** block (product, firmware version, modem chip, frequency range, device mode). Example successful output (Heltec LoRa32 v4): [rnodeconf-info-heltec-lora32-v4-example.txt](../../samples/cli/rnodeconf-info-heltec-lora32-v4-example.txt).

```text
[10:41:43] Opening serial port /dev/ttyACM0...
[10:41:46] Device connected
[10:41:46] Current firmware version: 1.86
[10:41:47] EEPROM checksum correct
[10:41:47] Device signature validated
[10:41:47]      Product            : Heltec LoRa32 v4 850 - 950 MHz (c3:c8:3f)
[10:41:47]      Firmware version   : 1.86
[10:41:47]      Modem chip         : SX1262
[10:41:47]      Device mode        : Normal (host-controlled)
```

If this fails, fix USB/permissions or a busy port (`rnsd` still running) before editing Reticulum config.

### Update existing firmware

```bash
~/.venvs/reticulum/bin/rnodeconf -u /dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_A4:CB:8F:A7:2C:68-if00
```

Other useful flags (see `rnodeconf --help`): `--fw-version`, `-f` / `--flash`, `-N` normal mode, `-c` print device configuration.

### Flash failed: “try … lower baud rate”

When the flasher subprocess fails, `rnodeconf` may print something like:

```text
Error from flasher (…) while writing.
Some boards have trouble flashing at high speeds, and you can
try flashing with a lower baud rate, as in this example:
rnodeconf --autoinstall --baud-flash 115200
```

That message is **real** for some boards at the default flash speed (**921600** per `rnodeconf --help`). Retry with a lower rate:

```bash
rnodeconf -a --baud-flash 115200 /dev/serial/by-id/...
# or: rnodeconf -u --baud-flash 115200 /dev/serial/by-id/...
```

**But:** if `rnsd` (or Sideband, another `rnodeconf`, etc.) still has the port open, flash can fail for that reason too—and the same “lower baud rate” hint may send you down the wrong path. **Always stop `rnsd` and check `lsof`/`fuser` first**; only then treat it as a true high-speed flash issue.

`rnodeconf -i` for verification also requires an exclusive serial port—stop `rnsd` before checking device info.

### Match radio parameters on the device (optional)

If you need the **on-device** LoRa parameters aligned before Reticulum opens the port, `rnodeconf` can set TNC-style values (example only—adjust for your plan):

```bash
rnodeconf --freq 869525000 --bw 125000 --txp 14 --sf 8 --cr 5 /dev/serial/by-id/...
```

Reticulum’s **`RNodeInterface`** block in `config` should use the **same** frequency, bandwidth, spreading factor, coding rate, and TX power as your mesh peers.

## 4. Reticulum configuration

Edit `~/.reticulum/config` and add or enable a block under the interfaces section. **Parameter names must match your `rnsd --exampleconfig` output** (`enabled = yes` vs `interface_enabled = true` on other stacks).

Example (operator lab, EU868-style—**verify legally**):

```ini
  [[RNode LoRa Interface]]
    type = RNodeInterface
    enabled = yes
    port = /dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_A4:CB:8F:A7:2C:68-if00
    frequency = 869525000
    bandwidth = 125000
    txpower = 14
    spreadingfactor = 8
    codingrate = 5
    flow_control = False
```

Copy of this snippet (with a placeholder `port`): [rnode-lora-interface-eu868-example.txt](../../samples/config/rnode-lora-interface-eu868-example.txt).

| Field | Role |
|-------|------|
| `port` | Serial device; use **by-id** path |
| `frequency` | Center frequency in **Hz** (`869525000` = 869.525 MHz) |
| `bandwidth` | LoRa bandwidth in Hz (often `125000`) |
| `txpower` | TX power in **dBm** (hardware and law may cap this) |
| `spreadingfactor` | SF7–SF12 (higher = slower, more robust) |
| `codingrate` | CR 5–8 (denominator; `5` = 4/5 in common notation) |
| `flow_control` | Serial flow control; often `False` on USB RNodes |

**Peers must agree** on frequency, bandwidth, SF, and CR (and compatible firmware) or you will see paths fail and probes time out.

Set `enabled = no` on this block until firmware and `rnodeconf -i` succeed, then enable and restart `rnsd`.

## 5. Verify with `rnsd`

With **`lxmf`** installed if you use interface discovery (see [new-node-setup.md](../guides/new-node-setup.md)):

```bash
rnsd -v
```

**Good signs:**

- `[Notice] Opening serial port /dev/serial/by-id/...`
- `RNodeInterface[RNode LoRa Interface] hardware MTU set to ...`
- No `[Error] Could not detect device for RNodeInterface[...]` (or it clears after replug)

**Bad sign:**

```text
[Error] Could not detect device for RNodeInterface[RNode LoRa Interface]
```

→ Wrong `port`, permissions, firmware not running, or board in wrong mode—re-run **`rnodeconf -i`** on that path.

In another terminal:

```bash
rnstatus
```

Confirm the RNode interface appears and shows traffic when the mesh is active.

## Troubleshooting

| Symptom | Check |
|---------|--------|
| Flash fails; suggests `--baud-flash 115200` | Stop **`rnsd`** and confirm port free; then retry lower baud if still failing (§3) |
| `Could not open the specified serial port` during `rnodeconf` | Port busy (`lsof`), permissions, or `rnsd` still running |
| `Could not detect device` | `port` path, cable, `rnodeconf -i` (with `rnsd` stopped), `enabled` only after firmware OK |
| `Permission denied` on open | [rnode-usb.md](rnode-usb.md) |
| Port changes after reboot | Use `/dev/serial/by-id/...` not `ttyACM0` alone |
| Link works on one machine only | Matching SF/BW/CR/frequency with peers; TX power and antenna |
| `rnodeconf` not found | Same venv `bin` as `rnsd`; extend `PATH` in [new-node-setup.md §1](../guides/new-node-setup.md#add-reticulum-cli-tools-to-your-path) |

## See also

- [Visual index](../concepts/visual-index.md)
- [rnode-usb.md](rnode-usb.md) — USB discovery and permissions
- [interfaces.md](../config/interfaces.md) — interface recipes index
- [mesh-cli-examples.md](../guides/mesh-cli-examples.md) — LoRa path/probe examples
- [Reticulum manual — interfaces](https://reticulum.network/manual/interfaces.html)
- [unsigned.io guides](https://unsigned.io/guides/) — RNode hardware and firmware notes
- [n00q — Reticulum Notes](https://n00q.net/blog/reticulum-notes/) — RNode on Linux tips
