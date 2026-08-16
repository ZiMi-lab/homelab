---
name: proxmox-ve
description: Operate this homelab's two Proxmox VE 9 nodes, LXC (including Docker-in-LXC), VMs, backups, and Portainer. Use when the user mentions Proxmox, PVE, LXC, VMID, pvesh, Portainer, or the Proxmox cluster.
---

# Proxmox VE 9 (2 nody)

Homelab má **2× Proxmox VE 9** v clusteru a **qdevice** jako LXC na TrueNAS (třetí hlas, HA je v pořádku). Docker stacky z tohoto repa typicky běží v **LXC** s Dockerem; UI je **Portainer**, zdroj pravdy zůstává git.

Nejdřív `inventory/hosts.yml` a volitelně `inventory/hosts.local.yml`.

## Kdy číst dál

- Docker v LXC a Portainer: [lxc-docker.md](lxc-docker.md)
- Dva nody, quorum, backup: [cluster.md](cluster.md)

## Zásady

- Výchozí režim: **read-only** (status, list, config get). Start/stop/migrate/destroy jen na požádání.
- API token s least privilege; do gitu nikdy nepatří. `PVEAuditor` stačí na inventuru.
- Neměň `pve` úložiště, bridge ani cluster join bez explicitního úkolu.
- Compose soubory upravuj v gitu, v Portaineru jen deploy z git/stacku.

## Rychlý přehled

Na nodu:

```bash
pveversion
pvecm status
pvesh get /nodes
pvesh get /cluster/resources --type vm
```

Vzdáleně (API token, ne root heslo) na FQDN s Let’s Encrypt:

```text
GET https://PVE_FQDN:8006/api2/json/cluster/resources
Authorization: PVEAPIToken=USER@REALM!TOKENID=SECRET
```

Shrň nody, online/offline, LXC vs QEMU, disk/RAM tlak, failed úlohy.

## LXC vs VM

| Workload | Preferuj |
| --- | --- |
| Docker compose služby | unprivileged LXC + nesting |
| TrueNAS / ZFS NAS | už běží mimo PVE |
| Desktop / Windows / nestabilní kernel potřeby | VM |
| Bitcoin full node | TrueNAS app (I/O, kapacita), ne PVE disk |

## Bezpečnost

- Management UI (`8006`) jen LAN/VPN.
- Nested Docker: `nesting=1`, často `keyctl=1`, `fuse=1`. Privileged LXC jen když unprivileged nestačí.
- Zálohy (`vzdump`) na oddělené úložiště, ideálně na TrueNAS.
