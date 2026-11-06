# Sporelink Protocol v0.2 — Federated Identity

## JWT

Launcher authenticates against `auth_homeserver` and receives a JWT.
Connector verifies the JWT locally using `auth_signing_key` from the
hub's well-known document. No per-login request to the hub is needed.

Header: `{"alg":"EdDSA","typ":"JWT","kid":"<server_id>"}`

Payload:
```json
{
  "iss": "hub-0001",
  "sub": "<uuid>",
  "name": "<nickname>",
  "iat": 1762459200,
  "exp": 1762545600,
  "aud": "sporelink"
}
```

Signature: Ed25519 over `base64url(header) + "." + base64url(payload)`.
JWT is **not** canonicalized — raw bytes are signed.

## Well-known changes

- `public_keys.auth_signing_key` added.
- `capabilities` includes `"auth"`.
- MVP: `auth_signing_key` may equal `server_signing_key`. v0.3 splits them.
