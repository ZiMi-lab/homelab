# Homarr

Dashboard. Portainer stack `homarr-stack`. Přístup jen LAN/NPM, ne tunnel.

`SECRET_ENCRYPTION_KEY` je 64 hex znaků. Vygeneruj ho lokálně a ulož jen do Portainer env / `.env`, ne do gitu:

```bash
openssl rand -hex 32
```

Po změně klíče Homarr nepřečte data zašifrovaná tím původním.
