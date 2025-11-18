# Running a Mova mainnet full node

## Machine specs

| Role      | vCPUs | RAM   | Storage  |
|-----------|-------|-------|----------|
| sync-node | 8     | 16 GB | 1 TB SSD |

Supported OS: Ubuntu 22.04 or later.

Ports

- 26656: P2P / gossip (must be open to the public). If this port is blocked, your node's IP may be deprioritized by peers.
- 26658: EVM JSON-RPC

For lowest latency, run the node in Tokyo, Japan.

---

## Setup

### Prerequisites

- Docker
- Docker Compose

Follow Docker's official installation guide for your OS:

https://docs.docker.com/engine/install/ubuntu/

### Clone the repository

```bash
git clone https://github.com/MovaChain/mova-chain-node
cd mova-chain-node
```

## Bootstrap the node

There are two ways to start the node: start syncing from scratch, or speed up synchronization by using a snapshot.

### Option 1 — Start from scratch

To start syncing from scratch, run:

```bash
docker compose up -d
```

This will start the node and it will begin syncing from the network.

### Option 2 — Start from a snapshot (faster)

To speed up the initial sync, download and extract a snapshot of the node state, then start the node.

1. Download the latest snapshot (recommended). The default automatic snapshot time is 20:00 UTC.

```bash
curl -SL "https://dl.movachain.com/snapshots/node-data-$(date -u +'%Y%m%d')" -O ./node-data.tar.gz
```

2. Unpack the snapshot into the `node-data` directory:

```bash
tar -zxf node-data.tar.gz -C ./node-data/
```

Or download and extract in a single command:

```bash
curl -SL "https://dl.movachain.com/snapshots/node-data-$(date -u +'%Y%m%d')" | tar -xzf - -C ./node-data/
```

3. Start the node:

```bash
docker compose up -d
```

## Check node status

1) Check recent synced blocks by following the node logs:

```bash
tail -f ./node-data/stdout-movad.txt | grep Committed
```

2) Verify the EVM JSON-RPC is responding:

```bash
curl -X POST -H 'Content-Type: application/json' --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest", true],"id":1}' http://127.0.0.1:26658
```

If you run the node inside Docker and expose ports differently, adjust the RPC host/port accordingly.

## Updating the node software

To update the node version:

1. Edit `MOVA_VERSION` in `docker-compose.yml` to the desired release tag.
2. Pull the new images and restart the stack:

```bash
docker compose pull
docker compose down
docker compose up -d
```
