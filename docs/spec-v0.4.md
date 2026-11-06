# Sporelink Protocol v0.4 — 2PC Transfers

## Endpoints

- `POST /transfer` — orchestrate transfer (sender side)
  body: `{to_node, from, to, amount}` → `{status, tx_id}`
- `POST /tx/prepare` — receiver reserves slot
  body: `{tx_id, to, amount, from_node}` → `{status:"prepared"}`
- `POST /tx/commit` — receiver credits (idempotent)
  body: `{tx_id}` → `{status:"committed"}`
- `POST /tx/rollback` — receiver cancels
  body: `{tx_id}` → `{status:"rolled_back"}`
- `GET /tx/status/<tx_id>` — inspect state

## Flow

1. Sender: `prepareOut` — atomic debit into escrow, record `prepared_out`
2. Sender → Receiver: `POST /tx/prepare`
3. Receiver: record `prepared_in`, respond 200
4. Sender → Receiver: `POST /tx/commit` (up to 3 retries)
5. Receiver: atomic credit, status `committed`
6. Sender: status `committed`

## Failure handling

- Receiver unreachable at prepare → sender `rollbackOut`, refund
- Receiver rejects prepare → sender `rollbackOut`, refund
- Commit fails 3× → sender returns 202 `commit pending`; transaction
  stays `prepared_out`. Recovery task (v0.5) will reconcile on restart.

## Not yet implemented (v0.5)

- Ed25519 signatures on `/tx/*` requests
- Recovery of `prepared_out` older than 60s
- Duplicate tx_id detection across restart
