# CLI samples

Plain-text captures for reproducibility and for agents that lack a local RNS install.

## Naming

Use:

`{tool}-help-{RNS_VERSION}-{OS}.txt`

Examples: `rnsd-help-1.2.5-Linux.txt`, `rnstatus-help-1.2.5-Darwin.txt`.

For non-`--help` output, describe the scenario:

`rnstatus-no-shared-instance-1.2.5-Linux.txt`

For multi-command lab logs (identity, paths, probe, `rnsh`), use descriptive names such as:

`mesh-lab-rnid-rnpath-rnprobe-rnsh-transcript.txt`

## Regeneration

From a venv with `rns` installed:

```bash
rnsd --help 2>&1 | tee samples/cli/rnsd-help-1.2.5-Linux.txt
rnstatus --help 2>&1 | tee samples/cli/rnstatus-help-1.2.5-Linux.txt
rnid --help 2>&1 | tee samples/cli/rnid-help-1.2.5-Linux.txt
rnx --help 2>&1 | tee samples/cli/rnx-help-1.2.5-Linux.txt
rncp --help 2>&1 | tee samples/cli/rncp-help-1.2.5-Linux.txt
```

Replace version and OS in the filename after checking `rnsd --version`.
