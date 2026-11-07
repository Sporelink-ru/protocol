# Sporelink Protocol v0.7 — Yggdrasil Compatibility

## Overview
Hub implements the Yggdrasil API required by `authlib-injector`.

## Endpoints
- `POST /sessionserver/session/minecraft/join`: Client registers intent to join.
- `GET /sessionserver/session/minecraft/hasJoined`: Server verifies session.
- `GET /sessionserver/session/minecraft/profile/{uuid}`: Fetches player profile (skins).
- `GET /`: Metadata (server name, RSA public key for skins).

This allows vanilla clients/servers to authenticate against Sporelink Hub
using standard `-javaagent:authlib-injector.jar=http://hub.url` flags.
