# Config format portability (configobj vs TOML)

Python Reticulum historically uses **configobj**, which *looks* like TOML but is **not** guaranteed to parse as strict TOML: `Yes`/`No` instead of JSON booleans, repeated `[[Section Name]]` patterns, and other differences.

The [FOSDEM 2026 community deck](https://fosdem.org/2026/events/attachments/9NCWUR-reticulum_community_meetup_implementations_migration_and_future/slides/267005/reticulum_dimz1j8.pdf) (“The Config Problem”) argues for **valid TOML everywhere** so the same file can move between implementations (Python, Rust, Go, embedded).

## Practical guidance

1. **Authoritative for your running daemon:** whatever `rnsd --exampleconfig` prints for that version.
2. **Cross-daemon moves:** validate with the target implementation’s parser; expect to translate booleans and interface table layout.
3. **Converter tooling:** the FOSDEM deck references a Reticulum-rs config converter example:

   ```text
   cargo run --example convert_config -- ~/.reticulum/config
   ```

   When you run this locally, save **sanitized** before/after snippets under [samples/config/](../../samples/config/) and link them here.

## Keys that differ between docs and versions

Slides and third-party posts may show `interface_enabled` while your `rnsd --exampleconfig` uses `enabled` on interface blocks. **Trust your generator output** for the version you installed.

## This repo

We store **Python 1.2.5-style** fragments from `rnsd --exampleconfig` as `.txt` snippets until strict-TOML samples are validated for another daemon.

For contrast, an illustrative **strict TOML** layout (may not match any single daemon verbatim): [samples/config/tcp-client-interface-strict-toml.example.toml](../../samples/config/tcp-client-interface-strict-toml.example.toml).

## See also

- [paths-and-example.md](paths-and-example.md)
- [interfaces.md](interfaces.md)
