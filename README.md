# Minibase

Minibase is a lightweight backend infrastructure.

<img
  alt="Minibase design"
  style="margin: 0 auto;"
  src="https://github.com/explodinglabs/minibase/blob/main/architecture.png?raw=true"
/>

## Postgres

Start Postgres:

```console
docker run
  --env POSTGRES_PASSWORD="postgres"
  --env POSTGRES_DB="app"
  --detach
  --restart=unless-stopped
  --name myapp-postgres
  --network myapp
  --volume ./data:/var/lib/postgresql/data:Z
  ghcr.io/myapp/postgres
```

## GoTrue

Start GoTrue:

```console
TODO
```

## PostgREST

Start PostgREST:

```console
docker run
  --env PGRST_DB_URI="postgres://authenticator:mysecretpassword@myapp-postgres:5432/app"
  --env PGRST_JWT_SECRET=$JWT_SECRET
  --env PGRST_APP_SETTINGS_JWT_SECRET=$JWT_SECRET
  --detach
  --restart=unless-stopped
  --name myapp-postgrest
  --network myapp
  ghcr.io/myapp/postgrest
```

## OpenResty

Edit the files in `openresty/conf.d` to set the correct container names, which
are specific to this application. The ports should remain unchanged.

Start OpenResty:

```console
docker run
  --detach
  --restart=unless-stopped
  --name myapp-openresty
  --network myapp
  --volume ${PWD}/conf.d-dev:/etc/nginx/conf.d
  --publish 8000:80
  ghcr.io/myapp/myapp-openresty
```
