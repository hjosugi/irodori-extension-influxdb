# Native Source

The initial source snapshot was copied from `db/influx.rs` in the desktop app.

Source SHA-256: `f872ddef744c7ad5e9f4cee85cfba9bb364a1e81e04c1ff7d23300e1e410717c`.


This directory is a migration staging area for `irodori.influxdb`. The active native
ABI shim lives in `src/lib.rs`; engine-specific connect/query/metadata behavior
should move here as the connector runtime contract is wired into the desktop app.

Engine status from `knowledge/engines.json`: `wired`.
