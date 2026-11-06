# Sporelink Protocol v0.3 — Multi-Hub Federation

## Gossip
Each hub fetches peer manifests in background, verifies signatures,
caches them. Request path never calls peers → no deadlock.

## Aggregated manifest
`/federation/manifest` returns `members[]` = self + all cached peers'
members, deduplicated by `server_id`, signed by receiving hub.

## JWT verification (multi-hub)
1. Read `kid` from JWT header
2. Lookup key in `Map<server_id, public_key>` from manifest members
3. Verify signature over `header.payload`
4. Verify `iss == kid`
5. Verify `exp`

Unknown `kid` → reject.
