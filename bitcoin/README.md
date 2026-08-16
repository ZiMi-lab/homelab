# Bitcoin stack

Vlastní uzel **Bitcoin Knots** na TrueNAS, další služby jako compose (custom app na NAS).

Agent skill: `.cursor/skills/bitcoin-sovereign/`

| Služba | Stav | Adresář |
| --- | --- | --- |
| bitcoind-knots | běží (TrueNAS catalog app) | [bitcoind-knots/](bitcoind-knots/) |
| electrs | šablona, k nasazení | [electrs/](electrs/) |
| mempool | šablona, po electrs | [mempool/](mempool/) |

## Pořadí

1. Doplň `bitcoin/bitcoind-knots/README.md` (porty, dataset, txindex, prune).
2. Uzel musí mít `txindex` a nesmí být pruned.
3. `electrs` na stejném NAS (read-only datadir).
4. Až electrs dosyncuje, `mempool`.
5. Lightning až po stabilním provozu.

RPC, Electrum ani mempool nepublikuj na Cloudflare Tunnel.
