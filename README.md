# Minibase

Minibase is a lightweight backend infrastructure.

<img
    alt="Minibase design"
    style="margin: 0 auto;"
    src="https://github.com/explodinglabs/minibase/blob/main/design.png?raw=true"
/>

## Postgres

Start Postgres:

```sh
docker run --detach --name myapp-postgres --network explodinglabs --volume ./data:/var/lib/postgresql/data:Z --publish 5xxx:5432 -e POSTGRES_PASSWORD="postgres" -e POSTGRES_DB="app" ghcr.io/explodinglabs/postgres
```

## GoTrue

Start GoTrue:

```sh
TODO
```

## PostgREST

Start PostgREST:

```sh
docker run --detach --name myapp-postgrest --restart=unless-stopped --network explodinglabs --publish 3xxx:3000 -e PGRST_DB_URI="postgres://authenticator:mysecretpassword@myapp-postgres:5432/app" -e PGRST_JWT_SECRET=$JWT_SECRET -e PGRST_APP_SETTINGS_JWT_SECRET=$JWT_SECRET ghcr.io/explodinglabs/postgrest
```

## OpenResty

Edit the files in `openresty/conf.d` to set the correct container names, which
are specific to this application. The port should remain unchanged.

Start OpenResty:

```sh
docker run --detach --name myapp-openresty --network explodinglabs --publish 8xxx:80 --volume ${PWD}/conf.d-dev:/etc/nginx/conf.d ghcr.io/explodinglabs/myapp-openresty
```
