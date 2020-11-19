Add .elixir_ls to `.gitignore`

Configure `mix.exs` `aliases`
```elixir
      test: [
        "ecto.create --quiet",
        "ecto.migrate --quiet",
        "test --stale --max-failures 1 --trace --seed 0"
      ],
      "test.l": [
        "ecto.create --quiet",
        "ecto.migrate --quiet",
        "test --listen-on-stdin --stale --max-failures 1 --trace --seed 0"
      ],
      ems: ["ecto.migrations"],
      em: ["ecto.migrate"],
      er: ["ecto.rollback"],
      er2: ["ecto.rollback --step 2"],
      er3: ["ecto.rollback --step 3"],
      er4: ["ecto.rollback --step 4"]
```