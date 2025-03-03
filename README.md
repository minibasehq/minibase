# Minibase

Minibase is a small backend infrastructure designed for SMB SaaS companies.

<img
    alt="Minibase design"
    style="margin: 0 auto;"
    src="https://github.com/explodinglabs/minibase/blob/main/design.png?raw=true"
/>

## Postgres

Create a directory for data to persist to:

```sh
mkdir -p ~/data/myapp
```

Start a Postgres container for the app, being sure to expose a unique port:

```sh
docker run --detach --name myapp-db --network explodinglabs --volume $HOME/data/myapp:/var/lib/postgresql/data --publish 5xxx:5432 -e POSTGRES_PASSWORD="postgres" -e POSTGRES_DB="app" ghcr.io/explodinglabs/postgres
```

## GoTrue

TODO

## PostgREST

Generate a JWT secret:

```sh
openssl rand -base64 172 | tr -d '\n' | pbcopy
```

Edit `.env` to set the `PGHOST` and `JWT_SECRET`.

Also set the JWT secret in `seed_demo.sql`.

Source the `.env` file to load the environment variables:

```sh
source .env
```

Start a PostgREST container, being sure to expose a unique port:

```sh
docker run --detach --name myapp-api --network explodinglabs --publish 3xxx:3000 -e PGRST_DB_URI="postgres://authenticator:mysecretpassword@myapp-db:5432/app" -e PGRST_JWT_SECRET=$JWT_SECRET ghcr.io/explodinglabs/postgrest
```

## OpenResty

Build the Docker image:

```sh
RESTY_DEB_FLAVOR="-debug" docker build -t ghcr.io/explodinglabs/myapp-web .
```

Edit `conf.d-dev/app.conf` to set the correct PostgREST api container name. The
port should remain 3000.

Start the container, exposing a unique port:

```sh
docker run --detach --name myapp-web --network explodinglabs --publish 8xxx:80 -v ${PWD}/conf.d-dev:/etc/nginx/conf.d -v ${PWD}/docs:/usr/local/openresty/nginx/html ghcr.io/explodinglabs/myapp-web
```
