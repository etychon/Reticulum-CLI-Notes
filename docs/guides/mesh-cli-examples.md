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

`rnprobe` accepts either a **raw destination hash** or a **full destination name** plus hash (see `rnprobe --help`). For the built-in **probe** aspect, the name form looks like:

```bash
rnprobe rnstransport.probe 28a479e075763f02c03522a5f95b7a08
```

Here `28a479e075763f02c03522a5f95b7a08` is **Chris’ probe responder** hash from the cheat sheet. Multiple probes:

```bash
rnprobe rnstransport.probe 28a479e075763f02c03522a5f95b7a08 -n 10
```

Example reply line:

```text
Valid reply from <28a479e075763f02c03522a5f95b7a08>
Round-trip time is 930.411 milliseconds over 1 hop [RSSI -26 dBm] [SNR 12.75 dB] [Link Quality 100.0%]
```

Pick the responder hash from **`rnstatus -v`** on the peer when you are unsure which hash answers probes.

See [rnprobe.md](../cli/rnprobe.md).

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
