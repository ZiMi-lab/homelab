# Tor SOCKS5

Klientský Tor daemon v Docker LXC (**Portainer**), ne relay. SOCKS5 pro Knots a povolené klienty na LAN.

Není to HTTP — **ne NPM, ne Cloudflare Tunnel**. Přístup: `DOCKER_LXC:9050` (FQDN/IP v `hosts.local.yml`).

## Nasazení (Portainer)

1. Stack z Git repository: `https://github.com/ZiMi-lab/homelab.git`
2. Compose path: `tor/docker-compose.yml`, větev `refs/heads/main`
3. Env podle `.env.example`

| Proměnná | Účel |
| --- | --- |
| `TOR_SOCKS_PORT` | Port na LXC (default 9050). Stejný port v Knots i u klientů. |
| `TOR_SOCKS_POLICY` | Tor `SocksPolicy` (čárkami, končí `reject *`). Přesné CIDR sem / do Portainer env, ne do gitu. |

Default policy je loopback + RFC1918 (`127.0.0.0/8` musí zůstat kvůli healthchecku). LAN zúž na trusted VLAN. IoT/guest ne. Kdo smí na `:9050`, řeší i MikroTik (address-list v `hosts.local.yml`).

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
| Prohlížeč / systém | SOCKS5 host = Docker LXC, port 9050, remote DNS (SOCKS5h) |
| CLI | `curl --socks5-hostname DOCKER_LXC:9050 …` |

Šablona Knots: `bitcoin/bitcoind-knots/bitcoin.conf.example`. Po `proxy=` Bitcoin vypne listen, pokud `listen=1` nezapneš. `onlynet=onion` až po dokončeném sync.
