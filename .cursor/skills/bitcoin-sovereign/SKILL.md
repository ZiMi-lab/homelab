---
name: bitcoin-sovereign
description: Design and operate a self-hosted Bitcoin stack on this homelab starting from TrueNAS bitcoind-knots (Electrum indexer, mempool, RPC, later Lightning). Use when the user mentions Bitcoin, Knots, bitcoind, electrs, Fulcrum, mempool, Sparrow, Lightning, LND, or self-custody node.
---

# Bitcoin sovereign stack

Uzel už běží: **Bitcoin Knots** jako TrueNAS catalog app `bitcoind-knots`. Cíl je vlastní ověřený uzel + lokální indexer + (volitelně) explorer, později Lightning. Žádné seed fráze, xprv, RPC hesla ani wallet soubory do gitu.

Inventory: `inventory/hosts.yml`. Služby: `bitcoin/`.

## Roadmapa (pořadí)

1. **Uzel zdravý** — sync, `txindex=1`, ne pruned, RPC jen LAN, záloha `wallet.dat` pokud wallet v nodu je.
2. **electrs** — Electrum protokol pro Sparrow/Electrum. Stejný NAS, read-only mount datadiru.
3. **mempool** — lokální explorer, závisí na RPC + electrs.
4. **Lightning** — až po měsíci stabilního uzlu (LND nebo Core Lightning). Nezačínej paralelně.
5. Mining/DATUM — jen na výslovnou žádost.

Detaily uzlu: [knots.md](knots.md). Další služby: [next-services.md](next-services.md).

## Bezpečnost (pevná pravidla)

- Port **8332** (RPC) a **ZMQ** nikdy na internet, Cloudflare Tunnel ani Portainer public endpoint.
- Port **8333** (P2P) může jít na WAN, pokud chceš inbound peery; jinak jen outbound.
- Electrum **50001** jen LAN/VPN; TLS (50002) až když je cert vyřešený.
- Tajemství v `.env` (gitignored). V gitu jen `.env.example`.
- Agent **negeneruje** seed, neukládá mnemonic, neexportuje wallet do chatu. Zálohy wallet jen lokální postup, bez obsahu souboru v konverzaci.
- `rpcallowip` omez na LAN CIDR, ne `0.0.0.0/0`.

## Co ověřit u existujícího Knots

Z TrueNAS app / `bitcoin.conf`:

- `txindex` zapnutý (bez něj electrs/mempool trpí nebo nejde)
- `prune_size` = 0 / prune vypnutý
- RPC user existuje (default app: `truenas`), heslo silné
- RPC bind na LAN IP NAS, ne jen localhost, pokud indexer běží v jiném kontejneru
- `zmqpubrawblock` / `zmqpubrawtx` pokud mempool ZMQ chce
- dataset cesta k blockchainu (stovky GB)

Do `bitcoin/bitcoind-knots/README.md` zapiš jen ne-tajné: porty, dataset, prune/txindex, verze app.

## Kde co běží

| Služba | Hostitel | Proč |
| --- | --- | --- |
| bitcoind-knots | TrueNAS app | disk I/O, kapacita, už nasazeno |
| electrs | TrueNAS custom app | potřebuje datadir + RPC vedle uzlu |
| mempool | TrueNAS custom app nebo Docker LXC | RPC+electrs; UI může být jinde, DB u indexeru |
| Sparrow | desktop na LAN | není serverová služba |
| LND/CLN | později, TrueNAS nebo VM | ne LXC bez zvážení |

## Když uživatel řekne „pokračuj v kryptu“

Navrhni **jeden** další krok (obvykle electrs), ověř předpoklady Knots, uprav compose v `bitcoin/electrs/`, neinstaluj Lightning ani altcoiny bez zeptání.
