# Bitcoin Knots na TrueNAS

Katalog: [bitcoind-knots community app](https://apps.truenas.com/catalog/bitcoind-knots_community/)

## App vs git

App spravuje TrueNAS. V gitu je:

- `bitcoin/bitcoind-knots/README.md` — runtime fakta
- `bitcoin/bitcoind-knots/bitcoin.conf.example` — doporučené volby pro indexer/mempool
- `bitcoin/bitcoind-knots/.env.example` — jak se k RPC připojit z dalších stacků

Skutečné `rpc_password` jen v TrueNAS UI a v lokálním `.env`.

## Důležité volby app

| Klíč | Pro stack |
| --- | --- |
| `rpc_user` / `rpc_password` | electrs, mempool |
| `rpc_port` | default 8332, bind na LAN |
| `p2p_clearnet_port` | 8333 |
| `prune_size` | musí zůstat vypnuté/0 |
| `block_filter_index_enabled` | hodí se walletům |
| `public_rest_api_enabled` | nech vypnuté, dokud nevíš proč |
| `additional_flags` | `txindex=1`, ZMQ, `rpcbind` pokud UI nenabízí |

Knots defaultně filtruje spam; to je v pořádku. Neměň policy flags bez diskuse.

## bitcoin.conf doplňky pro indexer

Viz `bitcoin/bitcoind-knots/bitcoin.conf.example`. Po změně restart app a ověř:

```bash
bitcoin-cli -rpcuser=... -rpcpassword=... getblockchaininfo
bitcoin-cli ... getindexinfo
```

`initialblockdownload: false` a `verificationprogress` ~1. `txindex` synced.

## Datadir

TrueNAS volume (host path) namapuj do inventory jako `bitcoin.datadir`. electrs ho montuje **read-only**. Nesdílej datadir dvěma bitcoind procesy.

## Wallet

Pokud v Knots běží wallet: záloha mimo NAS snapshot (offline kopie). Snapshot datasetu nestačí jako jediná záloha seedu. Agent obsah `wallet.dat` nečte.
