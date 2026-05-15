# Mesh CLI worked examples (lab)

**Version note:** Transcripts from a **Raspberry Pi 4 + RNode LoRa** lab (May 2026). Hashes are **real for that deployment**; in your network use **your** identities and destination hashes. Prompts use `operator@` and `/home/operator/` in the docs; the raw combined log is in [samples/cli/mesh-lab-rnid-rnpath-rnprobe-rnsh-transcript.txt](../../samples/cli/mesh-lab-rnid-rnpath-rnprobe-rnsh-transcript.txt).

This page ties together **`rnid`**, **`rnpath`**, **`rnprobe`**, and **`rnsh`** the way you use them on air: show local identity, keep a **cheat sheet** of peer aspects, check the **path table**, **probe** a responder, start **`rnsh`**, then confirm **path to one app destination**.

## Show identity (`rnid -p`)

```bash
rnid -i ~/.reticulum/storage/identities/default -p
```

Example output (timestamps and paths as captured):

```text
[2026-05-13 20:29:04] Loaded Identity <15a4005a03362483af44b20f9bd83e3d> from .reticulum/storage/identities/default
[2026-05-13 20:29:04] Public Key  : f3e29f2c2a31efa1677d9a609cb8b39d63eb18c00cdb5b0ec357bb734a63a8258c22d2cb4291e0dd6cd2ddb8f8e7023f8f1367e54c514f09c6f0dd4066d740fd
[2026-05-13 20:29:04] Private Key : Hidden
```

See [rnid.md](../cli/rnid.md).

## Peer hash cheat sheet (one lab)

Use `rnid -H …` on each peer when you are unsure of the aspect string; this table is a **snapshot** for one mesh only.

| Peer | Role | Hashes (hex, no angle brackets) |
|------|------|----------------------------------|
| **Emmanuel** | identity | `15a4005a03362483af44b20f9bd83e3d` |
| | probe | `8ee86c7476994f966ad0e622eff90e09` |
| | rnsh | `9363366a60acd789745f58a7563621cb` |
| | Nomad primary identity | `d63f7973d0175975076b446fc08f07a9` |
| | LXMF delivery | `b2a531452647b94596bf673f59f7cbec` |
| **Chris** | identity | `9839c928768eca38683eb556e72e854e` |
| | rnsh | `5e2b1e3934cc6af561124087ef6faaca` |
| | probe responder | `28a479e075763f02c03522a5f95b7a08` |

## Path table (`rnpath -t`)

Shows destinations the stack knows about and next-hop / interface (here **RNode LoRa**):

```bash
rnpath -t
```

```text
<c8c00ff5b04e3a19579855b5584ca0e1> is 1 hop  away via <4723257372e013f86c176f941742dce2> on RNodeInterface[RNode LoRa] expires 2026-05-20 20:23:33
```

See [rnpath.md](../cli/rnpath.md).

## Probe a responder (`rnprobe`)

`rnprobe` always needs **two** positional arguments: a **full destination name** in dotted notation, and a **destination hash** (see `rnprobe --help`). The usual connectivity test uses the built-in transport probe name:

```bash
rnprobe rnstransport.probe 28a479e075763f02c03522a5f95b7a08
```

Here `28a479e075763f02c03522a5f95b7a08` is **Chris’ probe responder** hash from the cheat sheet—not his identity hash and not his `rnsh` hash. Multiple probes:

```bash
rnprobe rnstransport.probe 28a479e075763f02c03522a5f95b7a08 -n 10
```

Example reply line:

```text
Valid reply from <28a479e075763f02c03522a5f95b7a08>
Round-trip time is 930.411 milliseconds over 1 hop [RSSI -26 dBm] [SNR 12.75 dB] [Link Quality 100.0%]
```

### Why `rnstransport.probe` is not a hostname (DNS mental model)

If you are used to IP networks, it is easy to read `rnstransport.probe` as something like `probe.example.com`—a name that **resolves** to an address. Reticulum does **not** work that way.

| IP / DNS habit | Reticulum equivalent |
|----------------|----------------------|
| Hostname → DNS → IP address | **No global DNS.** The mesh routes on a **destination hash** (16 bytes, shown as 32 hex digits). |
| `ping 192.0.2.1` (one address per host) | **Many destinations per node**—identity, transport probe, `rnsh`, LXMF, apps—each with its **own** hash. |
| One “machine” ≈ one IP | One **identity** can own **many** destinations; probing the wrong hash hits the wrong “socket,” not “the node” in general. |

The string `rnstransport.probe` is a **developer label**, not an address the network looks up. Reticulum splits it on dots into:

- **`rnstransport`** — **application name** (`app_name`). The reference stack hard-codes this for the Transport Instance (`APP_NAME = "rnstransport"` in `RNS/Transport.py`).
- **`probe`** — **aspect** (a sub-destination under that app). When `respond_to_probes = Yes` and transport is running, `rnsd` registers an incoming listener at `app_name` + aspect `"probe"`.

Together, `rnstransport.probe` means: “talk to the **probe responder service** that ships with the transport layer,” not “the human name of the radio” or “the operator’s identity.”

On the wire, packets are addressed to the **hash** you pass as the second argument. The dotted name tells `rnprobe` **which encrypted destination object to build** on your side (app + aspects + the remote public key recalled from that hash). The [Reticulum manual — Understanding](https://reticulum.network/manual/understanding.html) describes destinations as hashed from `app_name`, aspects, and identity; see also [destinations-announces-listeners.md](../concepts/destinations-announces-listeners.md).

### How to use the name and hash together

1. **On the node that should answer:** enable `enable_transport = Yes` and `respond_to_probes = Yes` in `[reticulum]`, restart `rnsd`, then run `rnstatus -v` and copy **`Probe responder at <hash> active`**. That hash is the only one that matches `rnstransport.probe` on that machine.
2. **On the initiator:** with `rnsd` running and a path over your interfaces, run:

   ```bash
   rnprobe rnstransport.probe <hash_from_rnstatus>
   ```

3. **Optional check:** `rnid -i <identityfile> -H rnstransport.probe` can derive the hash from the same dotted name on a given identity file. If it disagrees with **`rnstatus` on the machine where `rnsd` listens**, trust **`rnstatus`** (that is the live probe responder).

For this **built-in** probe, the **name is fixed** by the implementation: always `rnstransport.probe`. You do not invent a custom probe hostname; you only copy the **hash** from the peer you want to test.

### What goes wrong if the name or hash is wrong

`rnprobe` uses the **hash** for path discovery (`Transport.request_path`) and the **name** to construct the outbound `SINGLE` destination that encrypts probe packets. Both must match the service you intend.

| Mistake | Typical symptom | Why |
|---------|-----------------|-----|
| Wrong or unknown **hash** (no path yet) | `Path request timed out` | The mesh has no route to that destination. |
| **Hash** reachable but not a probe listener (e.g. identity or `rnsh` hash with `rnstransport.probe`) | `Probe timed out` | Path may exist, but nothing at that hash accepts transport probe traffic. |
| Wrong **name** (e.g. `rnsh.shell` + probe hash) | Often `Probe timed out` | Packets may still route to the hash, but the initiator builds the wrong destination type/aspects for that listener. |
| Target has no probe responder (`respond_to_probes = No` or `enable_transport = No`) | No `Probe responder at …` in `rnstatus -v`; remote `rnprobe` times out | The `rnstransport.probe` destination was never registered on that node. |

Examples that **fail** on the lab mesh (Chris’ probe hash is `28a479e075763f02c03522a5f95b7a08`):

```bash
# Identity hash — not the probe responder
rnprobe rnstransport.probe 9839c928768eca38683eb556e72e854e

# rnsh destination — different app/aspect entirely
rnprobe rnstransport.probe 5e2b1e3934cc6af561124087ef6faaca
```

To test **`rnsh`**, the peer runs `rnsh -l …` and you use **that** destination’s hash and the aspect name `rnsh` documents—not `rnstransport.probe` unless you mean the transport probe.

Full setup and troubleshooting: [new-node-setup.md §5](new-node-setup.md#5-announces-and-probes-rnprobe) and [§ `rnprobe` always times out](new-node-setup.md#rnprobe-always-times-out). CLI reference: [rnprobe.md](../cli/rnprobe.md).

## `rnsh` listen (lab only: `--no-auth`)

```bash
rnsh -l -v -i ~/.reticulum/storage/identities/default --no-auth
```

Verbose startup (paths anonymised in published transcript):

```text
[Notice]   rnsh listening for commands on <9363366a60acd789745f58a7563621cb>
[Info]     Using /bin/bash for default command.
```

The listening hash **`9363366a60acd789745f58a7563621cb`** matches **Emmanuel’s rnsh** row in the table above. **Do not use `--no-auth` outside a closed test.**

See [rnsh.md](../cli/rnsh.md).

## Path to one destination (`rnpath <hash>`)

Confirms how the stack reaches a **single destination** (often an **application-created** destination on the peer—in this case the same hash as Chris’ probe responder):

```bash
rnpath 28a479e075763f02c03522a5f95b7a08
```

```text
Path found, destination <28a479e075763f02c03522a5f95b7a08> is 1 hop away via <28a479e075763f02c03522a5f95b7a08> on RNodeInterface[RNode LoRa]
```

## See also

- [New node setup](new-node-setup.md)
- [Destinations, listeners, and announces](../concepts/destinations-announces-listeners.md)
- [samples/cli/mesh-lab-rnid-rnpath-rnprobe-rnsh-transcript.txt](../../samples/cli/mesh-lab-rnid-rnpath-rnprobe-rnsh-transcript.txt)
