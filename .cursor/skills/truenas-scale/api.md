# TrueNAS 26 JSON-RPC API

Dokumentace: [API v26](https://api.truenas.com/v26.0/jsonrpc.html)

## Endpoint

```text
wss://<TRUENAS_FQDN>/api/current
```

Tento homelab **nemá** self-signed cert jako default. Interní FQDN (Mikrotik DNS → NPM nebo přímo na NAS) používá **Let’s Encrypt** (ACME DNS-01 přes Cloudflare). TLS ověřuj (`TRUENAS_VERIFY_TLS=1`). API klíč posílej jen přes `wss://` / HTTPS, nikdy přes čistý `ws://` mimo loopback.

Když jde API přes Nginx Proxy Manager, na Proxy Hostu musí být povolený **WebSocket**.

## Auth (26.10)

Používej `auth.login_ex`, ne deprecated `auth.login_with_api_key` (zmizí v 27).

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "auth.login_ex",
  "params": [
    {
      "mechanism": "API_KEY_PLAIN",
      "username": "TRUENAS_API_USER",
      "api_key": "TRUENAS_API_KEY"
    }
  ]
}
```

Úspěch: `result.response_type == "SUCCESS"`. Pak volat metody:

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "system.info",
  "params": []
}
```

Batch requesty API nepodporuje.

## Preferovaný klient

1. **`midclt`** na NAS nebo z LAN s `--uri wss://FQDN/api/current` a `-K`.
2. Python `truenas_api_client` (`Client(uri="wss://FQDN/api/current")`, ověřený TLS).
3. Ruční WebSocket jen když midclt není k dispozici.

Env (lokálně, ne v gitu):

```bash
TRUENAS_URL=https://nas.example.cz
TRUENAS_API_USER=admin
TRUENAS_API_KEY=...
TRUENAS_VERIFY_TLS=1
```

FQDN ber z `inventory/hosts.local.yml`, ne z `.lan` placeholderu.

## Časté metody

| Účel | Metoda |
| --- | --- |
| Systém | `system.info` |
| Pooly | `pool.query` |
| Datasety | `pool.dataset.query` |
| Snapshot | `zfs.snapshot.query`, `zfs.snapshot.create` |
| Alerty | `alert.list` |
| Apps | `app.query`, `app.get_instance`, `app.start`, `app.stop` |
| Docker | `docker.config`, `docker.status` |

Zápisové metody (`*.create`, `*.update`, `*.delete`, `app.start/stop`) jen na výslovnou žádost.

## Časté chyby

- Volání REST `/api/v2.0/` → 404 / neexistuje na 26.x.
- DDP `{"msg":"connect","version":"1"}` → špatný protokol.
- HTTP místo HTTPS → revokace API klíče.
- `curl -k` / `rejectUnauthorized: false` — tady zbytečné a špatně; cert je důvěryhodný.
- `auth.login_with_api_key` bez username → na 26.x preferuj `auth.login_ex`.
