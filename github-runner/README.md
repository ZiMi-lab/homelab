# GitHub Actions runner

Self-hosted runner pro soukromé repozitáře, ať CI nečerpá placené minuty GitHub Actions. Oficiální image `ghcr.io/actions/actions-runner`, režim **ephemeral**: každý job dostane čistý kontejner, po jobu se runner odregistruje a Docker spustí nový.

Jen odchozí spojení na GitHub (HTTPS 443). Žádné porty, ne NPM, ne tunnel.

## Kde běží

**Vlastní LXC** na Proxmoxu, ne Docker LXC s ostatními stacky. CI spouští kód z PR; v odděleném LXC nedosáhne na data a volume jiných služeb (třeba vývojové PostgreSQL) jinak než po síti.

1. LXC: Debian 12, unprivileged, 2 vCPU, 4 GB RAM, 20 GB disk. **Options → Features:** `nesting` a `keyctl` (bez nich Docker v unprivileged LXC nepoběží).
2. Docker podle oficiálního návodu pro Debian, address pools viz `.cursor/skills/proxmox-ve/lxc-docker.md`.
3. Portainer Agent v LXC a v Portaineru **Environments → Add environment → Docker Standalone → Agent** (příkaz `docker run` ukáže Portainer).
4. Systém LXC aktualizuje `unattended-upgrades`.

## Token

GitHub → **Settings → Developer settings → Fine-grained tokens**:

- Repository access: **Only select repositories**, jen repozitáře z `RUNNER_n_REPO`.
- Repository permissions: **Administration: Read and write** (vydání registračního tokenu runneru). Nic dalšího.
- Expirace např. rok; obnovit v Portainer env.

## Nasazení (Portainer)

1. Prostředí nového LXC → **Stacks → Add stack** → **Repository**.
2. URL `https://github.com/ZiMi-lab/homelab.git`, reference `refs/heads/main`, compose path `github-runner/docker-compose.yml`.
3. **Environment variables** podle `.env.example`:

| Proměnná | Účel |
| --- | --- |
| `GH_RUNNER_PAT` | Fine-grained PAT (viz výše). Jen v Portainer env. |
| `RUNNER_n_REPO` | `owner/repo`. Prázdné = slot spí (`sleep infinity`). |
| `RUNNER_n_NAME` | Prefix jména runneru v GitHubu; za ním je hostname kontejneru. |
| `RUNNER_n_LABELS` | Další štítky vedle automatických `self-hosted,linux,x64`. |
| `RUNNER_IMAGE_TAG` | Tag image, default `latest`. |

Osobní účet GitHub nemá runnery organizace, proto jeden slot na repozitář. Další repozitář = další služba `runner-3` v compose.

## Kontrola

- Portainer → kontejner `…-runner-1-1` → **Logs**: na konci `Listening for Jobs`.
- GitHub → repo → **Settings → Actions → Runners**: zelený runner, stav **Idle**.
- `401` v logu = neplatný token. `404` = token nemá přístup k repozitáři nebo je špatně `RUNNER_n_REPO`.

## Workflow

Ve workflow:

```yaml
runs-on: ${{ vars.CI_RUNNER || 'ubuntu-latest' }}
```

V repozitáři **Settings → Secrets and variables → Actions → Variables** nastav `CI_RUNNER=self-hosted`. Když LXC neběží, proměnnou smaž a CI jede zase na GitHubu (jinak joby čekají ve frontě až 24 h a pak spadnou).

## Aktualizace

- **Runner** se při startu jobu aktualizuje sám, když GitHub vydá novou verzi, takže CI se kvůli staré verzi nezasekne. Ephemeral kontejner ale novou verzi stahuje u každého jobu znovu, dokud se nestáhne nový image.
- **Image** (runner i systém pod ním): jednou za měsíc **Stacks → stack → Pull and redeploy** se zapnutým **Re-pull image**. Nebo v nastavení Git stacku **Automatic updates** (polling) se zapnutým **Re-pull image**.

## Bezpečnost

- Token vidí admini Portaineru a kdokoli s přístupem k Dockeru v LXC (`docker inspect`). Proto vlastní LXC.
- `docker.sock` se nepřipojuje. Uživatel `runner` je v image ve skupině `docker`, bez soketu mu to nic nedává.
- Jen soukromé repozitáře s důvěryhodnými přispěvateli. Na runner veřejného repa by kód z cizího PR běžel v homelabu.
- Kontejner padlý uprostřed jobu nechá v GitHubu offline runner; ephemeral runnery GitHub po čase odstraní sám, nebo je smaž ručně.
