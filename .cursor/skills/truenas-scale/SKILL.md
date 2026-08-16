---
name: truenas-scale
description: Operate this homelab's TrueNAS SCALE 26.10 storage, datasets, snapshots, and Apps (including bitcoind-knots) via JSON-RPC 2.0 WebSocket. Use when the user mentions TrueNAS, SCALE, ZFS, datasets, NAS apps, midclt, or NAS health.
---

# TrueNAS SCALE 26.10

Tento homelab běží na **TrueNAS SCALE 26.10**. REST API (`/api/v2.0/`) je **odstraněné**. Starý DDP handshake (`{"msg":"connect"}`) nepoužívej.

Nejdřív přečti `inventory/hosts.yml` a pokud existuje, `inventory/hosts.local.yml`. Tajemství nikdy necommituj.

## Kdy číst dál

- API, auth, midclt: [api.md](api.md)
- Apps a custom compose: [apps.md](apps.md)

## Zásady

- Zdroj pravdy pro compose je tento git, ne klikání v UI. TrueNAS Apps UI je runtime.
- Destruktivní akce (destroy dataset, delete app, rollback) jen po výslovném souhlasu.
- API klíč posílej jen přes `wss://` na **FQDN s Let’s Encrypt** (Mikrotik DNS / NPM). TLS ověřuj. HTTP revokuje klíč.
- Preferuj nejméně privilegovaný API klíč. Výchozí režim agenta je **read-only** (query/info), zápis jen na požádání.
- Bitcoin blockchain dataset je velký a citlivý na I/O. Nesdílej ho přes SMB/NFS zbytečně; indexery (electrs) drž na stejném NAS.

## Rychlý health check

Na TrueNAS (nebo vzdáleně s URI):

```bash
midclt --uri wss://TRUENAS_HOST/api/current -K "$TRUENAS_API_KEY" call system.info
midclt --uri wss://TRUENAS_HOST/api/current -K "$TRUENAS_API_KEY" call pool.query
midclt --uri wss://TRUENAS_HOST/api/current -K "$TRUENAS_API_KEY" call alert.list
midclt --uri wss://TRUENAS_HOST/api/current -K "$TRUENAS_API_KEY" call app.query
```

Shrň: verze, uptime, zdraví poolů, alerty, stav Apps (`bitcoind-knots`, `immich`).

## Datasets

Bitcoin a další Apps ukládej na dedicated dataset (komprese `lz4`, `atime=off` u blockchain dat, recordsize podle typu dat). ACL pro Apps obvykle preset **Apps** (uid/gid 568), pokud katalogová app neříká jinak.

Nový dataset jen po odsouhlasení jména poolu a cesty. Cestu zapiš do inventory.

## Bitcoin na TrueNAS

`bitcoind-knots` je **katalogová community app**, ne compose z tohoto repa. Konfiguraci a RPC dokumentuj v `bitcoin/bitcoind-knots/`. Další služby (electrs, mempool) jdou jako TrueNAS **custom app** ze souborů v `bitcoin/`.

Před indexerem ověř: `txindex=1`, uzel **není pruned**, RPC/ZMQ poslouchá na LAN, port 8332 **není** na internetu.
