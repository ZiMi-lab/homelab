# Immich

TrueNAS **catalog app**, ne compose z tohoto adresáře.

- Datasety: `immich/data`, `immich/postgres` (owner `apps`)
- Z internetu: Cloudflare Tunnel → LAN IP TrueNAS a port app (ne port-forward)
- LAN: NPM + Let’s Encrypt
- Auth: Google SSO, password login vypnutý; auto-register vypnutý

Návod: https://zimacek.cz/dokumentace/home-lab/immich-cloudflare-tunnels-google-sso/

Do `inventory/hosts.local.yml` doplň FQDN a origin `IP:port`. Client ID/secret do gitu ne.
