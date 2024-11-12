# Docker Compose Setup for PostgreSQL and External Node

This repository contains a Docker Compose setup for running a PostgreSQL database and an external node service. The setup is designed to be used with a specific external node version, and it configures the PostgreSQL instance with custom settings tailored for performance.

## Prerequisites

- Docker
- Docker Compose

## Usage

1. Clone this repository.
2. Ensure Docker and Docker Compose are installed.
3. Run `docker-compose --file testnet-external-node.yml up -d` to start the services.
4. The services will be up and running as per the configurations defined in the `testnet-external-node.yml` for the testnet.

## Services

### 1. PostgreSQL

- **Image:** `postgres:14`
- **Exposed Ports:** `5430`
- **Volumes:** `mainnet-postgres` (Persistent storage for PostgreSQL data)
- **Environment Variables:**
  - `POSTGRES_PASSWORD`: Password for the PostgreSQL user.
  - `PGPORT`: Port number on which PostgreSQL will listen.
- **Custom Configuration Parameters:**
  - `max_connections=200`
  - `log_error_verbosity=terse`
  - `shared_buffers=2GB`
  - `effective_cache_size=4GB`
  - `maintenance_work_mem=1GB`
  - `checkpoint_completion_target=0.9`
  - `random_page_cost=1.1`
  - `effective_io_concurrency=200`
  - `min_wal_size=4GB`
  - `max_wal_size=16GB`
  - `max_worker_processes=16`
  - `checkpoint_timeout=1800`
- **Healthcheck:**
  - Runs a query to check if the PostgreSQL instance is ready.

### 2. External Node

- **Image:** `matterlabs/external-node:2.0-v24.24.0-alpha`
- **Depends on:** PostgreSQL service (ensures the service starts after PostgreSQL is healthy)
- **Ports:**
  - `3060` (HTTP)
  - `3061` (WebSocket)
  - `3081` (Healthcheck)
  - `3322` (Prometheus)
- **Volumes:** `mainnet-rocksdb` (Persistent storage for RocksDB data)
- **Environment Variables:**
  - `DATABASE_URL`: Connection URL for the PostgreSQL instance.
  - `DATABASE_POOL_SIZE`: Size of the database connection pool.
  - `EN_HTTP_PORT`: Port for the HTTP server.
  - `EN_WS_PORT`: Port for the WebSocket server.
  - `EN_HEALTHCHECK_PORT`: Port for the health check.
  - `EN_PROMETHEUS_PORT`: Port for Prometheus metrics.
  - `EN_ETH_CLIENT_URL`: URL of the Ethereum client.
  - `EN_MAIN_NODE_URL`: URL of the main node.
  - `EN_L1_CHAIN_ID`: Layer 1 chain ID.
  - `EN_L2_CHAIN_ID`: Layer 2 chain ID.
  - `EN_L1_BATCH_COMMIT_DATA_GENERATOR_MODE`: Mode for batch commit data generator.
  - `EN_STATE_CACHE_PATH`: Path for the state cache.
  - `EN_MERKLE_TREE_PATH`: Path for the Merkle tree.
  - `EN_SNAPSHOTS_RECOVERY_ENABLED`: Enable or disable snapshots recovery.
  - `RUST_LOG`: Log level settings.

## Volumes

- `mainnet-postgres`: Stores PostgreSQL data.
- `mainnet-rocksdb`: Stores RocksDB data for the external node.
