Add Custom Error Helper `error_helpers.ex` (copy from other projects)

```elixir
  def error_tag(form, field) do
    Enum.map(Keyword.get_values(form.errors, field), fn error ->
    content_tag(:span, translate_error(error),
      class: "text-red-500 mx-auto w-content text-sm italic",
      phx_feedback_for: input_id(form, field)
    )
  end)
end
```

