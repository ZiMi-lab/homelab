# Nginx Proxy Manager

Interní reverse proxy + Let’s Encrypt **DNS-01** (Cloudflare API). LAN hostname na Mikrotiku je CNAME na tento host.

- Portainer stack: `nginx-proxy-manager-stack`
- Runtime compose: `/data/compose/<id>/v<n>/` (nestabilní); kanonický YAML má být tento adresář
- Admin `:81` jen LAN/WireGuard. Healthcheck s interním FQDN do gitu nepatří (Portainer env / lokální override).
- Návod k TLS/NPM: https://zimacek.cz/dokumentace/home-lab/nextdns-mikrotik-https-home-lab/ (DNS v článku je zastaralé — resolver je Cloudflare DoH)

```bash
cp .env.example .env
# volumes jsou external — musí existovat na Docker LXC
```
