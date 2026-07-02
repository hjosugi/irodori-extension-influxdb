# InfluxDB Connector

Adds InfluxDB connectivity as an installable connector extension.

This connector is listed in the public Irodori extension marketplace.

## Connector

- Extension ID: `irodori.influxdb`
- Engine ID: `influxdb`
- Wire: `influxdb`
- Default port: `8086`
- Native ABI: `irodori.connector.native.v1`
- Driver linked: `true`

A desktop adapter source snapshot is staged in `native/source/` from `db/influx.rs`.

Connector metadata lives in `connector.config.json` and `irodori.extension.json`.
The Rust code keeps native ABI exports in `src/lib.rs`, shared buffer/JSON helpers in `src/abi.rs`, and the InfluxDB HTTP driver in `src/driver.rs`.

## Connection Metadata

- Endpoint modes: `hostPort`, `connectionString`
- Transport modes: `direct`, `sshTunnel`, `socks5Proxy`, `httpConnectProxy`, `proxyChain`
- TLS supported: `true`
- Custom driver options: `true`

| Auth method | Label | Secret purposes |
|---|---|---|
| `none` | No authentication | none |
| `connectionString` | Connection string / DSN | none |
| `userPassword` | User/password | `password` |
| `bearerToken` | Bearer token | `token` |
| `apiKey` | API key | `token` |
| `clientCertificate` | Client certificate / mTLS | `privateKey`, `privateKeyPassphrase` |
| `customDriverOptions` | Custom driver options | `password`, `token`, `privateKey`, `privateKeyPassphrase` |

## Experience Metadata

- Domains: `timeSeries`
- Result views: `timeChart`, `table`, `heatmap`
- Inspired by: `InfluxDB Data Explorer`, `Flux aggregateWindow`, `InfluxDB tasks`

| Workflow | Result view | Templates |
|---|---|---|
| Time range query | timeChart | time-influx-aggregate-window |
| Downsample window | timeChart | time-influx-aggregate-window |
| Latest values | table | time-influx-latest |

| Template | Label | Language | Result view |
|---|---|---|---|
| `time-influx-aggregate-window` | Aggregate window | `flux` | `timeChart` |
| `time-influx-latest` | Latest values | `flux` | `table` |

## ABI Calls

The native driver handles these JSON requests:

| Method | Response |
|---|---|
| `health` / `ping` | Connector health, engine id, ABI version, and driver link status. |
| `describe` / `capabilities` | Embedded manifest and connector config. |
| `manifest` | Raw `irodori.extension.json`. |
| `config` | Raw `connector.config.json`. |
| `connect` | Builds the endpoint from URL or host/port, validates `/ping`, and stores the connection. |
| `query` | Executes InfluxDB 3 SQL via `/api/v3/query` and returns table-shaped rows. |
| `metadata` | Reads `information_schema.columns` and returns schema/object metadata. |
| `close` | Removes the stored connection. |

The implementation currently supports `queryType: "sql"` for InfluxDB 3 compatible servers. Flux templates remain catalog metadata for clients that support Flux execution elsewhere.

## Development


Generated extension repositories share `../target` across sibling repositories so Rust dependencies are compiled once per checkout. DuckDB and MotherDuck are driver-linked by default; set `IRODORI_CONNECTOR_LINK_DUCKDB=0` only when you need metadata-only DuckDB-compatible scaffolds.


```sh
make check
make build
```

Release packages place platform-specific native artifacts under `dist/native`.
