# Docker v LXC a Portainer

## Aktuální stav vs. cíl

**Teď:** stacky vznikly v Portaineru. Compose žije **uvnitř volume Portaineru**:

```text
/data/compose/<stack_id>/v<verze>/docker-compose.yml
```

`<verze>` se zvedne při každém uložení stacku (`v2` → `v7` → `v23`). To **není** stabilní cesta pro git, Cursor ani dokumentaci. Není to ani cesta na LXC hostu — je to filesystem **kontejneru `portainer`**.

**Cíl:** YAML v tomto GitHub repo. Portainer má stack napojený na git (Repository + compose path), nebo `docker compose` z clone. Env/tajemství zůstanou v Portainer env / lokálním `.env`, ne v gitu.

Cursor edituje git. Nenasazuje se do `/data/compose/...`.

## Kde teď najít běžící konfiguraci

1. `docker compose ls` — sloupec Config Files ukáže aktuální `vN` (platí jen do příštího uložení v UI).
2. Portainer → Stacks → stack → **Editor** (nejspolehlivější snapshot).
3. Z hostitele LXC:

   ```bash
   docker exec portainer cat /data/compose/7/v2/docker-compose.yml
   ```

   Čísla ber z `compose ls`, ne z paměti.
4. Labels kontejneru: `com.docker.compose.project.config_files`.
5. Volumes a sítě: `docker inspect`, ne YAML.

Po exportu do gitu je pravda `<služba>/docker-compose.yml` v tomto repu. Portainer si po pullu zase udělá nové `vN` — to je cache, ne zdroj.

## Napojení na Cursor (cílová cesta)

```text
Cursor  --edit-->  GitHub  --Portainer Git pull-->  /data/compose/<id>/v<n>/  (runtime kopie)
                         --nebo git pull + compose-->  /opt/homelab
```

Pro existující stack:

1. Z Portainer Editoru zkopíruj compose **bez secretů** do `<služba>/docker-compose.yml`.
2. Proměnné do `.env.example`; ostré hodnoty nech v Portainer Environment.
3. V Portaineru přepni stack na **Repository**: `https://github.com/ZiMi-lab/homelab.git`, compose path např. `netalertx/docker-compose.yml`.
4. Další změna jen v gitu, v UI už YAML needituj (jinak zase drift a nové `vN` mimo git).

Lokální dump z Portaineru patří do gitignored `/dev/`, do gitu jen po vyčištění secretů.

## Role

| Místo | Co tam patří |
| --- | --- |
| GitHub / Cursor | kanonický compose, `.env.example` |
| Portainer env / `.env` | hesla, tokeny |
| `/data/compose/<id>/v<n>/` | runtime kopie, nestabilní cesta |
| Docker volumes | data |

Portainer UI (LAN, HTTPS přes NPM) je provoz, ne git. Admin Portaineru ani `:81` NPM ne na Cloudflare Tunnel.

## LXC pro Docker (Proxmox 9)

Unprivileged CT, `nesting=1,keyctl=1,fuse=1`. Docker CE. Síť: důvěryhodná VLAN, statická IP, FQDN v Mikrotik DNS.

## Docker bridge pooly (ne Portainer)

Každý compose stack bez vlastní sítě dostane nový **bridge** od `dockerd`. Portainer UI na to pool nemá.

Výchozí Docker: nejdřív `172.17.0.0/12` po **/16** na síť (~65k adres). Až dojdou, sáhne na `192.168.0.0/16` po **/20** (`192.168.0.0/20`, `192.168.16.0/20`, …) — to koliduje s běžným LAN. Zakázaný rozsah se nedá vypsat; platí jen **povolené** `default-address-pools`. Co v seznamu není, Docker na nové sítě nepoužije.

Na LXC v `/etc/docker/daemon.json` (přesné CIDR jen v `hosts.local.yml`, ne do gitu):

```json
{
  "default-address-pools": [
    { "base": "172.30.0.0/16", "size": 24 }
  ]
}
```

- `default-address-pools` — z čeho se krájí sítě **stacků**. `size` je maska jedné sítě (`/24` stačí). `bip` **není potřeba**; ten mění jen default `docker0` (adresa hosta na tom bridge). Když `bip` vynecháš, `docker0` zůstane na 172.17.0.0/16. Když ho dáš, nesmí ležet uvnitř `base`.
- `base` nesmí překrývat VLAN, WG, NAS.

`systemctl restart docker`. Nové sítě berou nový pool. Staré (včetně `192.168.16.0/20`) zůstanou, dokud stack/síť nesmažeš a nevytvoříš znovu. Per-stack `ipam.subnet` v compose do veřejného gitu nedávej.

## Síť přístupu ke službám

- **LAN:** Mikrotik static DNS → Nginx Proxy Manager (`80`/`443`) → kontejner. Certifikát Let’s Encrypt DNS-01 (Cloudflare).
- **Internet:** Cloudflare Tunnel (`cf_tunnel`), žádný port-forward. Origin je HTTP na LAN IP:port.
- **Správa:** WireGuard / LAN. Portainer, Proxmox, NPM admin, TrueNAS API.

Podrobnosti: skill `homelab-network`.
