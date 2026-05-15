# RNode on Linux over USB

**Captured / scope:** Operator checklist distilled from [n00q: Reticulum Notes](https://n00q.net/blog/reticulum-notes/) (RNode tips) and common Linux serial practice. Verify against your board and distro.

**Diagrams:** [visual index](../concepts/visual-index.md)

```mermaid
flowchart LR
  User[operator user]
  Dev[/dev/ttyACM0 or by-id path]
  Tools[rnodeconf and rnsd]
  User -->|read write permission| Dev
  Tools --> Dev
```

**Figure: USB permissions gate serial access**

## Find the device

```bash
lsusb
ls -l /dev/ttyACM* /dev/ttyUSB* 2>/dev/null
```

RNode often appears as **`/dev/ttyACM0`** (CDC ACM) or **`/dev/ttyUSB0`** (USB-serial), depending on firmware and adapter.

## Permissions

If tools cannot open the serial device (`Permission denied`):

### Option A: ACL on the device node (quick test)

From n00q (replace `user` and device path):

```bash
sudo setfacl -m u:yourlogin:rw /dev/ttyUSB0
```

Use the real path (`ttyACM0` vs `ttyUSB0`). `setfacl` requires ACL support on the filesystem that holds `/dev` (common on systemd systems with `/dev` on tmpfs).

### Option B: udev rule (persistent)

Prefer a dedicated `udev` rule so your user is in a group that owns the device, or use `MODE`/`GROUP` with the RNode `idVendor`/`idProduct` from `lsusb -v`. **Do not** copy a rule blindly: extract IDs from your hardware, then test with `udevadm test` / replug.

Placeholder pattern (edit attributes after `lsusb`):

```text
# /etc/udev/rules.d/99-rnode.rules
# ATTRS{idVendor}=="????", ATTRS{idProduct}=="????", MODE="0660", GROUP="dialout"
```

Add your user to `dialout` (or the group you choose), then log out and back in.

## Cable sanity

Use a **data** USB cable. Charge-only cables cause mysterious “device never shows up” or flaky tty behavior; try another cable before debugging software.

## Point Reticulum at the port

In your Reticulum config, RNode-style interfaces use a `port` (or equivalent) setting pointing at the device path. See [interfaces.md](../config/interfaces.md) and [samples/config/](../../samples/config/).

For **firmware install**, **LoRa parameters**, and a full **`[[RNode LoRa Interface]]`** example, see [rnode-lora-install.md](rnode-lora-install.md).

## See also

- [Install LoRa RNode](rnode-lora-install.md)
- [Reticulum manual — interfaces](https://reticulum.network/manual/interfaces.html)
- [unsigned.io guides](https://unsigned.io/guides/) (RNode material referenced from n00q)
- [FOSDEM 2026 slides — LoRa snippet / EU868 table](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) (illustrative RF parameters only)
