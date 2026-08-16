# Další Bitcoin služby

## electrs

Compose: `bitcoin/electrs/`.

Potřebuje:

- RPC + P2P adresa Knots
- read-only datadir (bloky)
- vlastní DB volume (desítky GB)
- první index = hodiny až dny, vysoké I/O — nespouštěj zároveň s jiným reindexem

Sparrow: `tcp://NAS_LAN:50001` (LAN only).

Alternativa **Fulcrum** je rychlejší na hodně walletů, žere víc RAM/SSD. Přidej až když electrs nestačí; nespouštěj oba na stejném datadir write.

## mempool

Compose: `bitcoin/mempool/`.

Potřebuje MariaDB + backend + frontend, `CORE_RPC_*` a `ELECTRUM_HOST=electrs`. Bez dokončeného electrs indexu UI lže.

Neexponuj mempool na Cloudflare, dokud není auth (VPN / SSO / aspoň basic auth).

## Lightning (později)

Až uzel + electrs běží týdny:

- LND nebo Core Lightning
- RTL / ThunderHub jen na LAN
- seed jen na papíře; agent ho negeneruje do chatu jako „ulož si to“

## Co teď nedělat

- Altcoiny, mosty, CEX API klíče v tomto stacku
- Veřejný Electrum server
- Pruning kvůli místu, když plánuješ electrs
- Druhý full node „pro jistotu“ bez důvodu (disk)
