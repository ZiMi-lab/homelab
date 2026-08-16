# Docker LXC + Portainer

**Teď** jsou stacky v Portaineru. Compose je v kontejneru `portainer`:

```text
/data/compose/<id>/v<n>/docker-compose.yml
```

`v<n>` se mění s každou uloženou verzí — **nepoužívej ji v gitu ani v inventory**. Aktuální soubor: Portainer Editor, nebo `docker compose ls` + `docker exec portainer cat …`.

**Cíl:** YAML v tomto repo, stack v Portaineru jako **Git repository** (env zůstane v UI). Cursor edituje git, ne `/data/compose`.

Runbook: `.cursor/skills/proxmox-ve/lxc-docker.md` (včetně Docker `default-address-pools` na LXC — Portainer pooly nestaví). Síť/TLS: `.cursor/skills/homelab-network/`. Zdroje: [docs/SOURCES.md](../docs/SOURCES.md).

## Přístup

| Účel | Jak |
| --- | --- |
| LAN HTTPS | Mikrotik DNS → NPM (Let’s Encrypt) |
| Internet | Cloudflare Tunnel stack `cf_tunnel` |
| Portainer / NPM admin | jen LAN nebo WireGuard |
