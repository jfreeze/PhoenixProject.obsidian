I use a simple deployment strategy that uses two bash scripts. One to push the code to the deployment server and one for the deployment server to create the release.

The process uses the deploy server as the build server and requires that the deploy server has a github deployment key, which can be readonly.

Also, the setup requires that the original deploy needs to be done on the server.


## Add deploy scripts

The Elixir script that runs before deploy - `config/releases.exs`
```elixir
import Config

config :<my_app>, <MyApp>Web.Endpoint, secret_key_base: System.get_env("SECRET")

config :car_lot, CarLot.Repo,
  username: System.get_env("PG_USER"),
  password: System.get_env("PG_PASS"),
  database: System.get_env("PG_DB"),
  hostname: System.get_env("PG_HOST"),
  pool_size: 10
 
case File.read("gcp_creds.json") do
  {:ok, json} ->
    config :goth, json: json, disabled: false

  _ ->
    if System.get_env("GCP_CREDENTIALS") do
      config :goth, json: {:system, "GCP_CREDENTIALS"}, disabled: false
    else
      config :goth, disabled: true
    end
end

config :car_lot, CarLot.Mailers.PostmarkMailer,
  adapter: Swoosh.Adapters.Postmark,
  api_key: System.get_env("POSTMARK_SERVER_API_TOKEN")
```

The `deploy.sh` script xxxx
```bash
#!/bin/sh

server="my_app"

# assumes local is ahead of upstream 
git push
ssh $server "(cd /apps/builds/<my_app>; git pull origin main/master)"
ssh $server "source ~/.shrc; (cd /apps/builds/<my_app>; ./release.sh)"
```

The `release.sh` script
```bash
#!//bin/sh

cd /apps/builds/<my_app>

. /apps/secrets/<my_app>/<my_app>.prod.secret.env.sh

git pull origin main/master

MIX_ENV=prod mix deps.get --only prod
MIX_ENV=prod mix compile
(cd assets; npm i; npm run deploy)
mix phx.digest
MIX_ENV=prod mix release

./_build/prod/rel/<my_app>/bin/<my_app> eval "<MyApp>.Release.migrate"
./_build/prod/rel/<my_app>/bin/<my_app> restart
# ./_build/prod/rel/<my_app>/bin/<my_app> stop
# ./_build/prod/rel/<my_app>/bin/<my_app> daemon

```


## Add Github Deployment Key to Server
### Configure login
## Add secrets

