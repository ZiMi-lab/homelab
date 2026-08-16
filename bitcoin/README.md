# Bitcoin stack

Vlastní uzel **Bitcoin Knots** na TrueNAS, další služby jako compose (custom app na NAS).

Agent skill: `.cursor/skills/bitcoin-sovereign/`

| Služba | Stav | Adresář |
| --- | --- | --- |
| bitcoind-knots | běží (TrueNAS catalog app) | [bitcoind-knots/](bitcoind-knots/) |
| electrs | šablona, k nasazení | [electrs/](electrs/) |
| mempool | šablona, po electrs | [mempool/](mempool/) |

Tor SOCKS5 pro uzel i LAN klienty je Portainer stack [`tor/`](../tor/), ne služba na NAS.

## Pořadí

1. Doplň `bitcoin/bitcoind-knots/README.md` (porty, dataset, txindex, prune).
2. Uzel musí mít `txindex` a nesmí být pruned.
3. Volitelně [`tor/`](../tor/) v Portaineru; v Knots `proxy=<DOCKER_LXC>:9050`.
4. `electrs` na stejném NAS (read-only datadir).
5. Až electrs dosyncuje, `mempool`.
6. Lightning až po stabilním provozu.

RPC, Electrum ani mempool nepublikuj na Cloudflare Tunnel.
