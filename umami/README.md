# Umami

Vlastní webová analytika (blog zimacek.cz). Z internetu přes Cloudflare Tunnel, ne port-forward.

Návod: https://zimacek.cz/dokumentace/hugo/webova-analytika-umami/

Tajné `POSTGRES_*` a `APP_SECRET` jen v Portainer env / `.env`.

Na LXC běží i samostatný stack `postgres`. Před použitím tohoto compose ověř, jestli Umami nechodí do sdílené DB — pak sem `db` službu nedávej.
