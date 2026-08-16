---
name: homelab-compose
description: Add and change Docker Compose services in this public homelab repo (IaC, .env.example, no secrets). Use when adding a stack, editing docker-compose.yml, or deploying to Portainer, Proxmox LXC, or TrueNAS custom apps.
---

# Homelab Compose IaC

Repozitář je **veřejný**. Platí README v kořeni a `.gitignore`.

## Layout

```text
<service>/
  docker-compose.yml
  .env.example
  README.md          # česky, účel + porty + kde běží
```

Bitcoin stack je pod `bitcoin/<služba>/` (víc služeb, společný uzel).
Infrastrukturní fakta jsou v `inventory/hosts.yml` (commituje se) a `inventory/hosts.local.yml` (gitignored).

## Pravidla compose

- Compose bez zbytečného `version:` (deprecated).
- `restart: unless-stopped`.
- Tajemství jen `${VAR}` z `.env`.
- Každá nová služba má `.env.example` se stejnými klíči a falešnými hodnotami.
- `TZ=Europe/Prague` jako default v example.
- Pojmenovaný volume nebo bind na dataset; bind cesty ber z inventory, v example použij placeholder `/mnt/POOL/...`.
- Porty v README: kontejner, host, zda LAN-only.
- `docker-compose.override.yml` je gitignored (lokální drift).

Pro existující Portainer stack: export YAML do gitu, tajemství do `.env.example` + Portainer env, stack přepnout na **Git repository**. Needituj compose v UI — `/data/compose/<id>/v<n>/` se s verzí mění a není zdroj pravdy.

## Kam služba patří

| Typ | Umístění v gitu | Runtime |
| --- | --- | --- |
| Obecný Docker stack | `/<služba>/` | Proxmox LXC + Portainer |
| Bitcoin doprovod | `/bitcoin/<služba>/` | TrueNAS custom app (prefer) |
| Katalogová TrueNAS app | `/bitcoin/<app>/` jen docs | TrueNAS Apps |
| Inventura hostů | `/inventory/` | — |

## Checklist nové služby

1. Adresář + compose + `.env.example` + README.
2. Žádné tokeny, webhook URL s tajemstvím, RPC hesla.
3. Doplň řádek do kořenového README (strom).
4. Do `inventory/hosts.yml` kdo to hostuje (až je známé).
5. Reverse proxy / cloudflared jen když má být veřejné; jinak LAN.

## Agent nesmí

- Commitovat `.env`, `hosts.local.yml`, certifikáty, `wallet.dat`, RouterOS dump, LAN IP.
- Publikovat Bitcoin RPC/Electrum na tunnel.
- Měnit produkční stack bez compose v gitu.
