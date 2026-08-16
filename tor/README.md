# Tor SOCKS5

Klientský Tor daemon v Docker LXC (**Portainer**), **ne relay**. SOCKS5 na **9050** pro Knots i další povolené klienty na LAN (prohlížeč, `curl`, …).

Není to HTTP — **ne NPM, ne Cloudflare Tunnel**. Přístup je `DOCKER_LXC:9050` (FQDN/IP v `hosts.local.yml`).

## Obraz

`leplusorg/tor:3.24.1` je přesně ten vzor: **Alpine 3.24** + `apk add tor` (teď 0.4.9.11), `EXPOSE 9050`, user `tor`.

`apk` běží **při stavbě image**, ne při každém startu. Start jen vygeneruje `torrc` z env (`SOCKS_HOSTNAME`, `SOCKS_PORT`, `TORRC_APPEND`) a spustí `tor`. Healthcheck sahá na `check.torproject.org` přes SOCKS na `localhost:9050` — po bootstrapu Toru má být kontejner healthy.

Není to relay / výstupní uzel. Vlastní Dockerfile v tomto repu není potřeba, dokud pinutý image stačí.

## Nasazení (Portainer)

1. Stack z Git repository: `https://github.com/ZiMi-lab/homelab.git`
2. Compose path: `tor/docker-compose.yml`, větev `refs/heads/main`
3. Env z `.env.example` (žádná tajemství)

Kdo smí na `:9050`, řeší **MikroTik** (trusted VLAN / address-list, ne IoT/guest). Konkrétní listy jen v `hosts.local.yml`. Tor `SocksPolicy` pouští RFC1918 a zbytek zahodí; různé klientské IP mají oddělené okruhy (`IsolateClientAddr`, default).

```bash
curl -fsSL --socks5-hostname DOCKER_LXC:9050 https://check.torproject.org/api/ip
```

## Porty

| Port | Protokol | Kam |
| --- | --- | --- |
| 9050 | SOCKS5 | LAN / WireGuard, jen povolené klienty |

## Klienti

| Kdo | Nastavení |
| --- | --- |
| Knots (TrueNAS) | `proxy=<DOCKER_LXC>:9050` — ne `127.0.0.1` |
| Prohlížeč / systém | SOCKS5 host = Docker LXC, port 9050, **remote DNS** (SOCKS5h) |
| CLI | `curl --socks5-hostname DOCKER_LXC:9050 …` |

Šablona Knots: `bitcoin/bitcoind-knots/bitcoin.conf.example`. Po `proxy=` Bitcoin vypne listen, pokud `listen=1` nezapneš. `onlynet=onion` nedávej během IBD.

Inbound onion (ControlPort) je až další krok.
