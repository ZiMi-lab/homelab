# Přehled projektu

Repozitář slouží jako centrální úložiště pro správu služeb v **Homelabu** (IaC). Provozní návody: [docs/SOURCES.md](docs/SOURCES.md) (zimacek.cz).

Runtime: **TrueNAS SCALE 26.10**, **2× Proxmox VE 9** (cluster + qdevice), Docker LXC + **Portainer**, Mikrotik + Cloudflare DoH, NPM (Let’s Encrypt), Cloudflare Tunnel.

## 🔒 Tajemství

**Repozitář je veřejný.** Co sem patří a co ne: [docs/SECURITY.md](docs/SECURITY.md).

* **Šablony:** `.env.example` u každé služby.
* **Inventura:** `inventory/hosts.yml`. LAN IP a FQDN → gitignored `inventory/hosts.local.yml`.
* Portainer env a `/data/compose/...` obsahují tajemství — do gitu jen vyčištěný YAML.

## 📂 Struktura

```text
homelab-docker/
├── .cursor/skills/
├── docs/                    # odkazy na blog / už zprovozněné
├── inventory/
├── truenas/  proxmox/  bitcoin/
├── nginx-proxy-manager/     # interní HTTPS
├── cloudflared/             # tunnel (stack cf_tunnel)
├── netalertx/  homarr/  umami/  uptime-kuma/  portainer/
├── postgres/                # vývojové PostgreSQL (šablona, bez názvů app)
└── immich/                  # TrueNAS app, jen docs
```

## Portainer cesty

Běžící stacky mají compose v kontejneru Portaineru:

`/data/compose/<id>/v<n>/docker-compose.yml`

`v<n>` se mění při každém uložení. Kanonický soubor má být v gitu; Portainer stack napojit na GitHub Repository.

## Skills

| Skill | Kdy |
| --- | --- |
| `truenas-scale` | ZFS, Apps, JSON-RPC 26.10 |
| `proxmox-ve` | cluster, LXC, Portainer |
| `homelab-network` | Mikrotik, NPM, TLS, tunnel |
| `bitcoin-sovereign` | Knots → electrs → mempool |
| `homelab-compose` | přidávání stacků |

## 🚀 Nasazení (cíl)

1. Compose v gitu, `.env.example` bez secretů.
2. Portainer → stack → Git repository na tento repo, nebo `docker compose up -d` z clone.
3. Tajemství jen v Portainer Environment / lokálním `.env`.

## 📝 Licence

MIT (TBD).
