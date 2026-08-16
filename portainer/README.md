# Portainer

UI nad Dockerem v Proxmox LXC. **Nespravuje YAML** — ten je v gitu. Portainer vidí kontejnery přes docker.sock.

- UI: `https://<LXC>:9443` (jen LAN, ne tunnel)
- Skill: `.cursor/skills/proxmox-ve/lxc-docker.md`

```bash
cp .env.example .env
docker compose up -d
```
