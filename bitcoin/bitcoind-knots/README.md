# Bitcoin Knots (TrueNAS app)

Katalogová app `bitcoind-knots`, ne Docker Compose z tohoto adresáře.

Doplň runtime fakta (bez hesel):

| Položka | Hodnota |
| --- | --- |
| App verze | TODO |
| Knots verze | TODO |
| Dataset / host path | TODO `/mnt/POOL/...` |
| RPC host (LAN) | TODO |
| RPC port | 8332 |
| P2P port | 8333 |
| ZMQ rawblock | 28332 (pokud zapnuto) |
| ZMQ rawtx | 28333 (pokud zapnuto) |
| txindex | TODO ano/ne |
| prune | TODO vypnuto/velikost |
| Inbound P2P | TODO |
| Tor SOCKS | TODO `DOCKER_LXC:9050` (`tor/`) |

RPC připojení pro další stacky: zkopíruj `.env.example` do `.env` (gitignored).

Doporučené volby pro electrs/mempool jsou v `bitcoin.conf.example`. Do app je zadej přes UI (`additional_flags` / ekvivalent), ne commituj ostrý `bitcoin.conf`.
