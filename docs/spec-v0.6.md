# Sporelink Protocol v0.6 — Node Policy & Player Auth

## Overview
Nodes are not just economic agents; they are gatekeepers. They verify
player JWTs locally against the Hub's public key and enforce local policies.

## `POST /auth/player`
Request: `{"jwt": "<jwt>"}`
Node fetches Hub's well-known, verifies the signature and `exp`.
Node applies policy `min_reputation_to_join`.
Response:
- 200 `{"status":"allowed","player_id":"...","name":"...","reputation":42}`
- 401 `{"error":"invalid jwt"}`
- 403 `{"error":"reputation too low","reputation":34,"required":50}`

## `/policy`
- `GET` returns current rules.
- `POST` updates rules (`{"min_reputation_to_join": 50}`).
