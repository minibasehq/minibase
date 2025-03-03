# Minibase

Minibase is a lightweight backend infrastructure.

<img
    alt="Minibase design"
    style="margin: 0 auto;"
    src="https://github.com/myapp/minibase/blob/main/design.png?raw=true"
/>

## Postgres

Start Postgres:

```sh
docker run --detach --restart=unless-stopped --name myapp-postgres --network myapp --volume ./data:/var/lib/postgresql/data:Z -e POSTGRES_PASSWORD="postgres" -e POSTGRES_DB="app" ghcr.io/myapp/postgres
```

## GoTrue

Start GoTrue:

```sh
TODO
```

## PostgREST

Start PostgREST:

```sh
docker run --detach --restart=unless-stopped --name myapp-postgrest --network myapp -e PGRST_DB_URI="postgres://authenticator:mysecretpassword@myapp-postgres:5432/app" -e PGRST_JWT_SECRET=$JWT_SECRET -e PGRST_APP_SETTINGS_JWT_SECRET=$JWT_SECRET ghcr.io/myapp/postgrest
```

## OpenResty

Edit the files in `openresty/conf.d` to set the correct container names, which
are specific to this application. The ports should remain unchanged.

Start OpenResty:

```sh
docker run --detach --restart=unless-stopped --name myapp-openresty --network myapp --publish 8000:80 --volume ${PWD}/conf.d-dev:/etc/nginx/conf.d ghcr.io/myapp/myapp-openresty
```
