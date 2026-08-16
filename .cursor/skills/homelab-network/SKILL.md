---
name: homelab-network
description: Homelab network as built — MikroTik VLANs, Cloudflare security DoH plus adlist, internal HTTPS via NPM, Cloudflare Tunnels, WireGuard for admin. Use when the user mentions DNS, TLS, NPM, Cloudflare, MikroTik, VLAN, DoH, adlist, or exposing a service.
---

# Homelab síť

Nejdřív `inventory/hosts.yml` / `hosts.local.yml`. Co nepatří do gitu: [docs/SECURITY.md](../../../docs/SECURITY.md). Návody: [SOURCES.md](SOURCES.md).

## Dva vstupy, nikdy port-forward

| Odkud | Jak | Příklady |
| --- | --- | --- |
| LAN / WireGuard | Mikrotik DNS → **Nginx Proxy Manager** (Let’s Encrypt DNS-01) | NAS, HA, Portainer, NPM |
| Internet | **Cloudflare Tunnel** (`cf_tunnel` na Docker LXC) | Immich, Umami |
| WAN přímo na službu | zakázáno | Immich app port, RPC, Portainer |

Interní hostname = CNAME na NPM. Veřejný hostname = tunnel na LAN `IP:port` (HTTP).

## DNS (aktuální stav)

Mikrotik je resolver pro LAN:

- **DoH:** `https://security.cloudflare-dns.com/dns-query` (Cloudflare security, 1.1.1.2 / 1.0.0.2), `verify-doh-cert=yes`
- **Adlist:** StevenBlack hosts (`/ip dns adlist`)
- **Force DNS:** klienti se ptají routeru na `:53`; DNS z WAN drop; odchozí DoT (`853`) drop; dstnat redirect `:53` na router (vybrané klienty lze vyjmout — seznam jen v `hosts.local.yml`)
- **IPv6:** LAN smí DNS na router; odchozí `:53` a DoT na WAN drop

NextDNS se **nepoužívá**. Článek na blogu platí pro NPM/HTTPS, ne pro DNS resolver.

Statické A/CNAME na Mikrotiku: `sluzba.domena` → NPM.

Do gitu nepatří: plný `/ip firewall` export, názvy address-list výjimek, WAN IP.

## TLS

Let’s Encrypt DNS-01 přes Cloudflare API v NPM. API TrueNAS/PVE na FQDN s ověřeným TLS. Přes NPM WebSocket pro `wss://`.

## VLAN (princip)

Důvěryhodná / IoT / guest / kamery / management. Přesná ID a CIDR jen v `hosts.local.yml`. Správa **WireGuard**, ne tunnel na admin UI.

## Nová služba

1. LAN → NPM + Mikrotik DNS.
2. Internet → existující tunnel, ne nový WAN port.
3. Admin UI → LAN/WG.
4. Bitcoin RPC / Electrum → jen LAN.
