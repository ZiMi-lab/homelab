# electrs

Electrum indexer nad Bitcoin Knots. Nasazuj jako TrueNAS **custom app** na stejném NAS, s read-only datadirem uzlu.

První sync indexu trvá dlouho. RPC a port 50001 jen LAN.

```bash
cp .env.example .env
# doplň RPC a BITCOIN_DATA
docker compose up -d
```

Sparrow: `NAS_LAN:50001` (TCP, bez TLS).

## Předpoklady Knots

- `txindex=1`
- prune vypnutý
- RPC dostupné z tohoto kontejneru (`host.docker.internal` nebo LAN IP)
