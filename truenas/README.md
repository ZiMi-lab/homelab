# TrueNAS SCALE

Runtime: **TrueNAS SCALE 26.10**. Katalogové apps (Immich, Knots) se dokumentují; custom apps z gitu.

- Inventura: [`inventory/hosts.yml`](../inventory/hosts.yml)
- API: `wss://<FQDN>/api/current` s ověřeným Let’s Encrypt (ne self-signed, ne REST)
- Návody: [docs/SOURCES.md](../docs/SOURCES.md)

## Datasety

| Dataset | Účel |
| --- | --- |
| `immich/data` | Immich library (owner `apps`) |
| `immich/postgres` | Immich DB |
| `TODO/apps/bitcoind-knots` | blockchain Knots |

## QDevice

`corosync-qnetd` jako LXC na tomto NAS. Při údržbě TrueNAS musí zůstat oba PVE nody online.

## Poznámky

Portainer na Proxmox nespravuje TrueNAS Apps.
