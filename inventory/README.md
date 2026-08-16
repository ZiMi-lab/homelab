# Inventura homelabu

Popisuje **kde co běží**. Co je veřejné vs. tajné: [docs/SECURITY.md](../docs/SECURITY.md).

| Soubor | Git | Účel |
| --- | --- | --- |
| `hosts.yml` | ano | role, služby, principy (bez IP a výjimek) |
| `hosts.local.yml` | ne | LAN IP, FQDN, MAC, firewall výjimky |
| `.env.example` | ano | názvy env var |

```bash
cp inventory/hosts.yml inventory/hosts.local.yml
# doplň IP, FQDN, CIDR — tento soubor se necommituje
```

Agent čte nejdřív `hosts.yml`, pak overlay `hosts.local.yml`.
