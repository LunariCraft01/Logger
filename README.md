# SigNoz

This directory contains a Docker Compose deployment of SigNoz, including its OpenTelemetry Collector, PostgreSQL metadata store, ClickHouse telemetry store, and ClickHouse Keeper.

## Requirements

- Docker Engine
- Docker Compose v2 (`docker compose`)

## Start the stack

Run these commands from this directory so the Compose file can find its mounted configuration files:

```bash
docker compose -f compose.yaml up -d
docker compose -f compose.yaml ps
```

The first startup may take a few minutes while the database services initialize and SigNoz runs its migrations.

## Access SigNoz

- Web UI: <http://localhost:8080>
- OTLP over gRPC: `localhost:4317`
- OTLP over HTTP: `localhost:4318`

Applications running directly on the host can send OTLP data to `http://localhost:4318`. Applications running in containers attached to the `signoz-network` Docker network can use `http://signoz-ingester:4318` for OTLP over HTTP. Configure the SDK or collector to send logs, metrics, and traces to the appropriate OTLP endpoint.

For example, to export logs over OTLP/HTTP, use:

```env
OTEL_EXPORTER_OTLP_LOGS_ENDPOINT=http://signoz-ingester:4318/v1/logs
```

To follow service logs:

```bash
docker compose -f compose.yaml logs -f
```

## Stop the stack

```bash
docker compose -f compose.yaml down
```

This stops and removes the containers but preserves the named data volumes. Removing the volumes also deletes the stored telemetry and metadata.

## Network and security

The Compose file publishes the SigNoz UI and OTLP receiver ports on the host. Restrict access with your host firewall or reverse proxy when running outside a trusted local environment. The Compose configuration uses default PostgreSQL credentials; replace them and update dependent connection settings before using this stack in a shared or production environment.
