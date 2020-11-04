# Adding `phx_gen_auth` 

Check hex.pm for latest `phx_gen_auth` and add it to the project dependencies in `mix.exs`:
```elixir
{:phx_gen_auth, "~> 0.5.0"}
```

Compile so `phx.gen.auth` shows in mix.
install `phx.gen.auth` (this uses `argon2` for the encryption library.)

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

You can customize the params for memory and time using [`Argon2.Stats`](https://hexdocs.pm/argon2_elixir/Argon2.Stats.html)
