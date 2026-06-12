# Usage

```
uptime-watch [flags] <url>...
```

Each positional argument is a URL. If you pass several, `uptime-watch`
checks them in parallel (bounded by `--concurrency`) and reports the worst
exit code.

## Flags

| Flag              | Default        | Description                                   |
| ----------------- | -------------- | --------------------------------------------- |
| `--timeout`       | `10s`          | Per-request wall-clock deadline.              |
| `--method`        | `GET`          | HTTP method.                                  |
| `--header`        | (none)         | Repeatable `Name: Value`.                     |
| `--expect`        | `200-299`      | Acceptable status range, comma-separated.     |
| `--body-contains` | (none)         | Substring that must appear in the body.       |
| `--insecure`      | off            | Skip TLS verification.                        |
| `--concurrency`   | `4`            | Parallel checks when multiple URLs are given. |
| `--json`          | off            | NDJSON output (one object per URL).           |
| `--version`       | -              | Print version and exit.                       |

## Examples

Cron job that pings five endpoints in parallel:

```bash
uptime-watch --concurrency 5 \
    https://a.example.com/healthz \
    https://b.example.com/healthz \
    https://c.example.com/healthz \
    https://d.example.com/healthz \
    https://e.example.com/healthz
```

CI integration that fails the job on TLS regressions:

```bash
uptime-watch --expect 200 --timeout 5s https://prod.example.com/healthz
case $? in
  0)   echo "OK" ;;
  12)  echo "TLS handshake regression" >&2; exit 1 ;;
  *)   exit $? ;;
esac
```

## Environment variables

Anything set as `PINGCHECK_<FLAG>` is treated as a default for the
corresponding flag. Command-line flags still win.
