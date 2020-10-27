# Adding `phx_gen_auth` 

Check hex.pm for latest phx_gen_auth 
```elixir
{:phx_gen_auth, "~> 0.5.0"}
```

Compile so `phx.gen.auth` shows in mix and install `phx.gen.auth`

```bash
mix deps.compile
mix phx.gen.auth --hashing-lib argon2 --binary-id Accounts User users
```

Add argon2 config to test.exs
```elixir
config :argon2_elixir,
    t_cost: 1,
    m_cost: 8
```


Add argon2 config to dev.exs
```elixir
config :argon2_elixir,
    t_cost: 12,
    m_cost: 17,
    parallelism: 2
```

Add argon2 config to prod.exs (designed for GCE 2CPU)
```elixir
config :argon2_elixir,
    t_cost: 3,
    m_cost: 18,
    parallelism: 2
```


