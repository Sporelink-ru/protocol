# Sporelink Protocol v0.5 — Signed 2PC + Recovery

## Node identity
Each node generates Ed25519 key on first start, stored at
`{data-dir}/node-key.txt` (base64). Public key at:

    GET /.well-known/sporelink-node
    → {"node_id": "...", "public_key": "ed25519:...", "endpoint": "..."}

## Signed requests
All `/tx/*` requests carry:
- `X-Sporelink-Node-Id:  <node_id>`
- `X-Sporelink-Timestamp: <unix seconds>`
- `X-Sporelink-Signature: <base64 Ed25519>`

Signature over:
```
SPORELINK-TX-V1\n<METHOD>\n<PATH>\n<TIMESTAMP>\n<sha256_hex(BODY)>
```

Timestamp window ±300s. Unknown peers fetched via `from_node`'s
well-known and cached in memory.

## Concurrency
Node MUST use a thread pool executor. Single-threaded HttpServer
deadlocks during cross-node peer discovery (A waits for B, B waits
for A's well-known).

## Recovery
Every 15s:
- find `prepared_out` older than 30s
- retry signed `POST /tx/commit` (with `from_node` in body)
- on 2xx → `committed`; on network error → retry next cycle

Recovery guarantees: if a node crashes between prepare and commit,
the survivor completes the transaction on restart. No funds stuck,
no double-credit.
