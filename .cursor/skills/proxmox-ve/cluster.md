# Dva nody, quorum, zálohy

## Quorum

Tento cluster: **2 PVE nody + QDevice** (corosync-qnetd na TrueNAS LXC). Očekávané hlasy: 3. Návod: https://zimacek.cz/dokumentace/home-lab/proxmox-cluster-2-nody-qdevice/

```bash
pvecm status
```

QDevice je třetí hlas, ne compute node. Nesmíš ho omylem smazat, zastavit s oběma PVE nody, ani dát do HA skupiny, která by ho při výpadku NAS přestěhovala (běží na TrueNAS, mimo PVE).

Údržba PVE nodu: nejdřív zkontroluj quorum a qdevice (`Qdevice: Yes`), pak migruj/stopni guesťáky, pak reboot. Údržba TrueNAS = výpadek qdevice; oba PVE nody musí zůstat online (2 z 3 hlasů).

Notifikace: Datacenter → SMTP Gmail (App Password). https://zimacek.cz/dokumentace/home-lab/proxmox-gmail-notifikace/

## HA

HA je zapnutá (typicky Home Assistant). Disky se **replikují** mezi nody, není sdílené Ceph. QDevice na TrueNAS do PVE HA nedávej.

Live migrace QEMU bez sdíleného storage znamená kopírování RAM+disku; s replikací je failover rychlejší.

## Migrace

Live migrace QEMU potřebuje sdílené storage. LXC migrace často offline + bind mounty na TrueNAS zkontroluj (cesty musí existovat na cíli).

## Zálohy

- Job `vzdump` na NFS/SMB dataset na TrueNAS.
- Retention zapiš do inventory.
- Restore testuj na neprodukčním CTID.
- Docker volumes uvnitř LXC zálohuj buď jako součást CT, nebo zvlášť (bind-mount data na ZFS dataset = lepší).

## Co agent nesmí sám

- `pvecm add` / `pvecm delnode`
- Změna `corosync.conf`
- Smazání VM/CT
- Změna bridge VLAN na produkční síti
- Odebrání qdevice / `pvecm qdevice remove`
- HA pro qdevice LXC (neběží na PVE)
