# Co smí být ve veřejném gitu

Repo je veřejné. Uváděj **architekturu a šablony**, ne provozní otisk sítě.

## Patří sem

- Compose, `.env.example`, role služeb, verze platforem
- Princip sítě: VLAN, DoH, NPM, tunnel, WireGuard jako *typ* přístupu
- Veřejné URL blogu a katalogů (Let’s Encrypt, Cloudflare DoH, StevenBlack hosts)

## Neposílej do gitu

| Typ | Příklady |
| --- | --- |
| Tajemství | `.env`, API tokeny, tunnel token, OAuth secret, RPC heslo, `wallet.dat`, seed |
| Identita sítě | LAN/WAN IP, MAC, přesná VLAN ID/CIDR, FQDN admin UI |
| Výjimky firewallu | address-list klientů, kteří obcházejí DNS/NAT |
| Plný export | RouterOS `.rsc`, `pveum`, kompletní filter chain |
| Klíče | WireGuard, SSH, certifikáty, Cloudflare origin cert |
| Privátní app | názvy DB/uživatelů, mapování na konkrétní produkty, host porty Postgres |

Tyto věci patří do `inventory/hosts.local.yml` nebo Portainer env (gitignored / mimo git).

Agent **necommituje** RouterOS dump, i když ho uživatel vloží do chatu. Z dumpů si bere jen princip (DoH URL typu, adlist, force-DNS) a konkretní listy/IP nechá v `hosts.local.yml`.
