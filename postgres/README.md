# Postgres (vývojové DB)

Portainer stack `postgres`: jedna nebo více instancí **PostgreSQL 16** pro vývoj soukromých aplikací. Data v external volume, přístup **jen LAN** (ne Cloudflare Tunnel, ne veřejné NPM).

Do gitu nepatří názvy produkčních app, uživatelé ani hesla. Ostré hodnoty jsou v Portainer env / `.env`.

## Vzor

```bash
cp .env.example .env
# POSTGRES_PASSWORD vyplň lokálně
docker volume create postgres_data
docker compose up -d
```

Další aplikace = další služba ve stacku (vlastní volume, user, db, host port). Stejný image `postgres:16`.

Port `5432` na hostu nenechávej na `0.0.0.0` směrem do internetu — jen důvěryhodná VLAN / WireGuard.
