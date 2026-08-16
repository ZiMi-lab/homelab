# TrueNAS Apps

SCALE 26.10 používá Docker (ne k3s). Jsou dva typy:

1. **Katalogové apps** (community/stable) — např. `bitcoind-knots`. Konfigurace žije v TrueNAS UI; do gitu patří jen dokumentace, porty, dataset cesty a `.example` soubory.
2. **Custom apps** — Docker Compose z tohoto repozitáře. YAML z `bitcoin/` (nebo jiné služby) vložit jako custom app, nebo nasadit na Proxmox LXC s Dockerem.

## Katalog vs git

| Služba | Kde běží | Co je v gitu |
| --- | --- | --- |
| bitcoind-knots | TrueNAS catalog app | `bitcoin/bitcoind-knots/` |
| immich | TrueNAS catalog app | `immich/` (docs) |
| electrs, mempool | custom app nebo LXC | `bitcoin/electrs/`, `bitcoin/mempool/` |
| Docker LXC stacky | Portainer na PVE | kořenové adresáře; runtime zatím `/data/compose/<id>/v<n>/` |

Při změně katalogové app zapiš do README služby: verze app, RPC port, dataset, prune/txindex, ZMQ.

## Custom app z compose

1. Compose v gitu musí používat `.env.example`, ne tajemství.
2. Volume cesty musí sedět na TrueNAS datasety (`/mnt/<pool>/...`).
3. Síť: RPC Bitcoin uzlu jen LAN / docker network, ne publish na `0.0.0.0` pokud to není nutné.
4. Po nasazení ověř `app.query` a logy kontejneru.

`app.create` / `app.update` umí `custom_compose_config` nebo `custom_compose_config_string`. Preferuj nasazení z git checkoutu na NAS (klon repo → custom app z YAML), ať se UI a git nerozejdou.

## UID/GID

Katalogové apps často běží jako **568:568** (`apps`). Volume musí mít odpovídající ACL. Electrs čtoucí `bitcoin` datadir potřebuje read oprávnění na stejný dataset.

## Portainer

Portainer běží na Proxmox LXC, ne jako náhrada TrueNAS Apps. TrueNAS stack nespravuj z Portaineru, pokud k tomu není výslovný důvod (dva orchestry nad stejnými kontejnery = chaos).
