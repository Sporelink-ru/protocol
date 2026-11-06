# Спецификация v0.1
##

**Well-known endpoint** (`GET /.well-known/mc-federation`):
```json
{
  "protocol_version": "0.1",
  "server_id": "8f3a...c2e1",
  "server_name": "Sporelink Hub",
  "auth_homeserver": "https://hub.sporelink.ru",
  "public_keys": {
    "server_signing_key": "ed25519:<base64>"
  },
  "capabilities": ["discovery", "manifest"]
}
```

**Manifest** (`GET /federation/manifest`):
```json
{
  "manifest_version": "0.1",
  "federation_id": "sporelink",
  "issued_at": "2026-11-06T12:00:00Z",
  "members": [
    { "server_id": "...", "endpoint": "https://hub.sporelink.ru",
      "public_key": "ed25519:<base64>" }
  ],
  "rules": { "min_reputation_to_join": 0, "season_duration_days": 45 },
  "signatures": [
    { "signer": "8f3a...c2e1", "sig": "<base64>" }
  ]
}
```

**Каноническая форма подписи**: подписывается **сам манифест без поля `signatures`** в каноническом JSON (RFC 8785 JCS — отсортированные ключи, без пробелов, UTF-8). Строка для подписи:
```
SPORELINK-MANIFEST-V1\n<canonical_json>
```
Подпись — Ed25519, 64 байта, base64.

## Canonicalization

Подписывается JSON в канонической форме: рекурсивная сортировка ключей
по code points, минимум экранирования, без пробелов.

Префикс подписи: `SPORELINK-MANIFEST-V1\n`.

Реализации обязаны проходить общий test-vector из `test-vectors/`.
