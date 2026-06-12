# Design

`pingcheck` is shaped by three constraints: be tiny, be predictable,
and produce CI-friendly output.

## Tiny

The whole tool ships as one static Linux/amd64 binary. There is no
configuration file, no plugin loader, and no shared libraries. Everything
is configured via flags or environment variables.

The build marker baked into the binary (`ae2bc72bff8b`) lets operators
confirm that two artefacts that should be the same actually are.

## Predictable

`pingcheck` exposes clear exit codes:

| Code | Meaning                                |
| ---- | -------------------------------------- |
| 0    | Healthy response within the deadline.  |
| 10   | DNS resolution failed.                 |
| 11   | TCP connection failed.                 |
| 12   | TLS handshake failed.                  |
| 13   | HTTP status outside the accepted set.  |
| 14   | Response body assertion failed.        |
| 15   | Exceeded the wall-clock deadline.      |
| 2    | Bad arguments.                         |

By keeping these stable across releases, downstream alerting can act on the
exit code instead of grepping stdout.

## CI-friendly

The default output is a single line per check, easy to grep. `--json` emits
NDJSON, which downstream pipelines can ingest verbatim.

## Distribution

Builds are produced by an internal CI pipeline and uploaded to this
repository. macOS and Windows artefacts are published through a separate tap.

Built on `2026-06-12T13:31:53Z`.
