# Proxmox VE 9

Cluster **proxmox1 + proxmox2 + qdevice** (TrueNAS LXC). HA přes **replikaci**. Notifikace Gmail SMTP.

- Inventura: [`inventory/hosts.yml`](../inventory/hosts.yml)
- Návody: [docs/SOURCES.md](../docs/SOURCES.md)
- Docker LXC: [docker-lxc.md](docker-lxc.md)

## Docker LXC

Stacky dnes v Portaineru: `/data/compose/<id>/v<n>/docker-compose.yml` (`v<n>` se mění). Cíl: git + Portainer Git repository.

Portainer a NPM admin jen LAN / WireGuard, HTTPS přes NPM.
