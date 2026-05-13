# New node: initial configuration and services

**Version note:** Written for **RNS 1.2.x** (`pip install rns`). Other distributions (RetiNet, `rnsd-rs`) may differ; document what you actually run in the [README](../../README.md) tested-stack table.

This page is an **operator checklist**: install Reticulum, create storage and **identity**, enable **interfaces**, run **`rnsd`**, then layer **rnprobe**, **`rnsh` / `rnx` / `rncp`**, and **Nomad Network** where you need them.

## 1. Install the stack

```bash
python3 -m venv ~/.venvs/reticulum
~/.venvs/reticulum/bin/pip install rns
```

Use the same venv’s `bin` directory on your `PATH` for daily commands, or call tools with full paths.

## 2. Choose the config directory

Reticulum searches `/etc/reticulum`, then `~/.config/reticulum`, then `~/.reticulum`. Easiest for a single user:

```bash
mkdir -p ~/.reticulum
```

See [paths-and-example.md](../config/paths-and-example.md). Generate a commented template:

```bash
rnsd --exampleconfig > ~/.reticulum/config.new
diff -u ~/.reticulum/config ~/.reticulum/config.new || true
# Merge the sections you need into ~/.reticulum/config manually, then remove config.new.
# Avoid blind `mv` over a working config.
```

For a first LAN lab, enable a **TCP server** on one machine and a **TCP client** on another, pointing at the server’s IP and port (see [interfaces.md](../config/interfaces.md) and [samples/config/](../../samples/config/)).

## 3. Create an identity

Pick a directory for key material (back this up securely if it becomes production):

```bash
mkdir -p ~/.reticulum/storage/identities
rnid --generate ~/.reticulum/storage/identities/node.identity -v
rnid -i ~/.reticulum/storage/identities/node.identity -p
rnid -i ~/.reticulum/storage/identities/node.identity -H rns.id
```

Sample transcript shape: [rnid-print-identity-example-1.2.5.txt](../../samples/cli/rnid-print-identity-example-1.2.5.txt).

**Aspects:** Applications use different **aspect** strings when deriving destination hashes. Default announce for `rnid -a` without arguments uses `rns.id`. Always use `rnid -H <aspect>` to print the hash your peer must use for that app.

## 4. Start the daemon (`rnsd`)

Foreground (good for first bring-up):

```bash
rnsd -v
```

Leave this running. In another terminal:

```bash
rnstatus
```

If you see `No shared RNS instance available`, `rnsd` is not running or a different `--config` was used. See [rnstatus.md](../cli/rnstatus.md).

## 5. Announces and probes (`rnprobe`)

**Paths:** `rnprobe` needs a **routed path** to the destination. On a fresh node that usually means at least one working interface, time for announces to propagate, and compatible transport between peers.

With `rnsd` running, announce your node’s default destination (adjust identity path):

```bash
rnid -i ~/.reticulum/storage/identities/node.identity -a
```

Give your peer the **destination hash** from `rnid -H …` for the aspect they will probe. They run:

```bash
rnprobe <destination_hash>
```

**Troubleshooting probes:** see [Troubleshooting](#troubleshooting) below.

## 6. Remote shell (`rnsh`)

`rnsh` uses its own identity layout under `~/.rnsh` / `~/.config/rnsh` (see `rnsh --help`). Typical pattern:

**Listener (server):**

```bash
rnsh -l -- /bin/bash --login
# Or restrict callers:
rnsh -l -a <caller_identity_hash> -- /bin/bash --login
```

**Client:**

```bash
rnsh <destination_hash> -- ls -la
```

Print listener destination info (after configuring identity):

```bash
rnsh -p
```

Requires `rnsd` (or another shared instance) with working interfaces. See [rnsh.md](../cli/rnsh.md).

## 7. Remote execution (`rnx`)

Non-interactive “run this command” style. Listener must allow initiator hashes.

```bash
rnx -l -i ~/.reticulum/storage/identities/node.identity -a <initiator_hash>
```

See [rnx.md](../cli/rnx.md).

## 8. File receive (`rncp`)

`rncp` sends or receives files over Reticulum. On a **receiver**, run listen mode with an identity (path is often under `~/.reticulum/storage/identities/`).

**Lab-only (no authentication):**

```bash
rncp -l -v -i ~/.reticulum/storage/identities/default --no-auth
```

**Production-style:** omit `--no-auth` and use `-a <allowed_identity_hash>` or `~/.rncp/allowed_identities` (see `rncp --help`).

Example log (verbose listener on a Raspberry Pi, one completed transfer): [rncp-listen-verbose-raspberrypi4-example.txt](../../samples/cli/rncp-listen-verbose-raspberrypi4-example.txt).

See [rncp.md](../cli/rncp.md).

## 9. Nomad Network

**Nomad Network** is not bundled inside the `rns` wheel on all installs; it is usually a **separate** install:

```bash
pip install nomadnet
```

Then run `nomadnet` (TUI) per upstream docs. It uses Reticulum underneath; configure interfaces in the same Reticulum config `rnsd` uses, start `rnsd`, then start Nomad Network.

Pointers: [Nomad Network on GitHub](https://github.com/markqvist/NomadNetwork) and the [wiki](https://github.com/markqvist/Reticulum/wiki/).

## 10. Run `rnsd` under systemd (Linux)

**Template only** — validate paths and user on your distro.

`~/.config/systemd/user/rnsd.service`:

```ini
[Unit]
Description=Reticulum Network Stack (rnsd)
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=%h/.venvs/reticulum/bin/rnsd -q --service
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

Then:

```bash
systemctl --user daemon-reload
systemctl --user enable --now rnsd.service
systemctl --user status rnsd.service
journalctl --user -u rnsd.service -f
```

Use `--config` in `ExecStart=` if your config root is not the default. Transport nodes with LoRa must start **after** USB devices appear; consider a `udev`-triggered path or `After=dev-ttyACM0.device` once you know your stable symlink.

## Troubleshooting

### `rnstatus` / `rnpath`: no shared instance

- Start `rnsd` (or another app that starts the shared stack).
- Pass the same `--config /path/to/reticulum` to **every** CLI if you do not use the default search path.

### Serial / RNode: permission denied

- See [rnode-usb.md](../linux/rnode-usb.md).

### TCP peer never connects

- **Firewall:** open the `TCPServerInterface` `listen_port` (default often 4242 in examples) on the server host.
- **Address:** client `target_host` / `target_port` must match where the server listens (`0.0.0.0` vs specific IP).
- **Both sides:** at least one interface enabled (`enabled = yes` in your config version’s spelling).

### `rnprobe` always times out

- **No path:** no mutual connectivity, wrong destination hash, or aspect mismatch (peer probes a hash for a different aspect than you announced).
- **Transport:** both sides need compatible paths (same test network, or reachable transport nodes).
- **Latency:** increase `rnprobe` timeout flags (see [rnprobe.md](../cli/rnprobe.md)).

### `rnsh` / `rnx` authentication failures

- Listener `-a` must include the initiator’s **identity** hash (not always the same string as a destination hash — use `rnid -p` / `rnsh -p` as appropriate).
- Check `~/.rnsh/allowed_identities` paths documented in `rnsh --help`.

### `rncp` never shows “listening” or transfers stall

- Confirm `rnsd` and identity path; try `rncp -p -i …` on the listener.
- Sender must target the **listener destination hash** printed when listen starts (see sample transcript).
- Prefer authenticated mode instead of `--no-auth` once paths work.

### Nomad Network cannot reach the stack

- Confirm `rnsd` is running first, then launch Nomad Network.
- Align `--config` / `RNS` config directory with what Nomad Network expects (per its docs).

### Wrong config picked up

- Remember search order: `/etc/reticulum` wins over `~/.reticulum`. See [paths-and-example.md](../config/paths-and-example.md).

### RF / LoRa interfaces do not start

- Parameters must match hardware and **local law**; see [interfaces.md](../config/interfaces.md).

## See also

- [README — quick path](../../README.md)
- [Destinations, listeners, and announces](../concepts/destinations-announces-listeners.md)
- [rnid.md](../cli/rnid.md), [rnsd.md](../cli/rnsd.md), [rnstatus.md](../cli/rnstatus.md), [rnprobe.md](../cli/rnprobe.md), [rnsh.md](../cli/rnsh.md), [rnx.md](../cli/rnx.md), [rncp.md](../cli/rncp.md)
- [Reticulum manual — Using Reticulum on your system](https://reticulum.network/manual/using.html)
- [n00q Reticulum Notes](https://n00q.net/blog/reticulum-notes/)
