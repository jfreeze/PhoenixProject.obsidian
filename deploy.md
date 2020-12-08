I use a simple deployment strategy that uses two bash scripts. One to push the code to the deployment server and one for the deployment server to create the release.

The process uses the deploy server as the build server and requires that the deploy server has a github deployment key, which can be readonly.

Also, the setup requires that the original deploy needs to be done on the server.


## Add Deploy/Release scripts

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

#### Deploy

The `bin/deploy.sh` script is a simple script when the production server is also the build server. The production server has a github deploy key and keeps a local copy of the repo. This facilitates fast compile times when deploying.
```bash
#!/bin/sh

if [ ! $# == 5 ]; then
  echo "Usage: $0 app server build_path secret elixir-code"
  echo "  app:        Application name"
  echo "  server:     Name of ssh server for build/deploy"
  echo "  build_path: Directory of repo on server"
  echo "  secret:     Pathname of shell with runtime secrets"
  echo "  elixir:     Elixir eval to run before daemon"
  exit
fi

app=$1
server=$2
build_path=$3
secret=$4
elixir=$5

c=`git rev-list --count HEAD`; sed -E -i .bak "s/@version +\"([0-9]+).([0-9]+).([0-9]+)\"/@version \"\1.\2.$c\"/" mix.exs
rm -f mix.exs.bak
git add mix.exs
git commit -m "git commit version bump"
git push origin main

ssh $server "(cd $build_path; git pull origin main)"
ssh $server "source ~/.shrc; (cd $build_path; ./bin/release.sh $app $build_path $secret $elixir)"
```

The `deploy.sh` script that launches the above script sits in the main directory.
```bash
#!/bin/sh

./bin/deploy.sh my_app my_app /apps/builds/my_app /apps/secrets/my_app/my_app.prod.secret.env.sh "MyApp.Release.migrate"
```

#### Release

The `bin/release.sh` script is call on the production machine from the `deploy.sh` script.

```bash
#!//bin/sh -x

app=$1
build_path=$2
secret=$3
if [ $# == 4 ]; then
  migration=$4
fi

. $secret

cd $build_path

git pull origin main

MIX_ENV=prod mix deps.get --only prod
MIX_ENV=prod mix compile
(cd assets; npm i; npm run deploy)
MIX_ENV=prod mix phx.digest
./_build/prod/rel/$app/bin/$app stop

MIX_ENV=prod mix release
if [ $# == 4 ]; then
  ./_build/prod/rel/$app/bin/$app eval "$migration"
fi
./_build/prod/rel/$app/bin/$app daemon
```



## Add Github Deployment Key to Server
### Configure login
## Add secrets

