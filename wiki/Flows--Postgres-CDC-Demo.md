# Postgres CDC Demo — Data Generator

**Bucket:** `data-generator`
**File:** [`flows/data-generator/postgres-cdc-demo.json`](../flows/data-generator/postgres-cdc-demo.json)

Simulates random data generation with INSERTs, UPDATEs, and DELETEs across three tables (`customers`, `orders`, `order_items`). Designed to be used in combination with the CDC PostgreSQL Connector to demonstrate change data capture pipelines.

---

## Purpose

This flow provides a continuous stream of realistic database mutations against a PostgreSQL instance. It automatically creates the required schema, tables, and publication on first run — no manual SQL setup is needed.

## Components

| Component | Type | Description |
|-----------|------|-------------|
| ExecuteScript (init) | Processor | Creates schema, tables, and publication on first run |
| GenerateFlowFile | Processor | Triggers periodic data generation |
| ExecuteSQL | Processor | Runs randomised INSERT/UPDATE/DELETE statements |

## Required NARs

- `org.apache.nifi:nifi-standard-nar:2.8.0` — included with standard NiFi installations
- PostgreSQL JDBC driver (`postgresql-42.7.10.jar`) — uploaded as a Parameter Context Asset

## Parameters

| Parameter | Description |
|-----------|-------------|
| `Database Connection URL` | JDBC URL to the Postgres instance |
| `Database Name` | Postgres database name |
| `Database User` | Postgres username |
| `Database Password` | Postgres password (sensitive — use parameter provider) |
| `Schema Name` | Schema for the generated tables |
| `Database Driver` | JDBC driver asset (bound to the uploaded JAR) |

## Configuration

Deploy via the Environment CD pipeline using an `environments/<env>/config.yaml` entry, or test in CI using the test YAML at `flows/data-generator/tests/test_postgres_cdc_demo.yaml`.

For secrets (`Database Connection URL`, `Database Password`), use the auto-provisioned Snowflake Parameter Provider with `#{PARAM_NAME}` references.

## Expected Behaviour

Once started, the flow continuously generates random INSERT, UPDATE, and DELETE operations against the three tables. The PostgreSQL publication enables downstream CDC connectors to capture these changes in real time.

## Validation Tests

The test file at [`flows/data-generator/tests/test_postgres_cdc_demo.py`](../flows/data-generator/tests/test_postgres_cdc_demo.py) validates the flow deploys correctly and processes data against a live PostgreSQL instance provisioned via the ephemeral CI runtime.
