# myapp-db

Create a directory for the data to persist to:

```sh
mkdir -p ~/data/myapp
```

Start a Postgres container for the app, being sure to expose a unique port:

```sh
docker run --detach --name myapp-db --network explodinglabs --volume $HOME/data/myapp:/var/lib/postgresql/data -e POSTGRES_PASSWORD="postgres" --publish 50xx:5432 ghcr.io/explodinglabs/postgres
```

Create a database (the db name "app" is fine for all apps):

```sh
docker exec myapp-db createdb -U postgres app
```

Create a configuration file, which has the environment variables needed to
`sqitch deploy`.

```sh
cp .example.env .env
```

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

Initialise Sqitch:

```sh
./sqitch init myapp --uri https://github.com/explodinglabs/myapp-db --engine pg
```

Create migration files:

```sh
./create.sh
```

Deploy migrations:

```sh
sqitch deploy
```

Seed initial data:

```sh
docker exec -i myapp-db psql -U postgres -d app < seed_demo.sql
```

Start a PostgREST container, being sure to expose a unique port:

```sh
docker run --detach --name myapp-api --network explodinglabs --publish 30xx:3000 -e PGRST_DB_URI="postgres://authenticator:mysecretpassword@myapp-db:5432/app" -e PGRST_JWT_SECRET=$JWT_SECRET ghcr.io/explodinglabs/postgrest
```

## Test it

Login:

```sh
export ACCESS_TOKEN=$(curl --silent --show-headers -X POST -H "Content-Type: application/json" http://localhost:30xx/rpc/login --data '{"user_": "demo", "pass": "demo"}' |sed -nE 's/^Set-Cookie: access_token=([^;]*).*/\1/p')
```

Post:

```sh
curl --silent -X POST -H "Authorization: Bearer $ACCESS_TOKEN" -H "Content-Type: application/json" -w "%{http_code}" --data '[{"name": "Buy milk"}, {"name": "Feed the cat"}]' http://localhost:30xx/task |jq
```

Get:

```sh
curl --silent -X GET -H "Authorization: Bearer $ACCESS_TOKEN" http://localhost:30xx/task |jq
```
