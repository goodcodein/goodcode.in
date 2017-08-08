---
id: 4
title: "Using mnesia with distillery"
date: 2017-08-08T09:48:59Z
layout: default
tags:
  - elixir
  - mnesia
  - distillery
---

If you are using `mnesia` with distillery. You may run into an error which likes below:

```
09:46:26.974 [info]  Application dynamic_store exited: DS.Application.start(:normal, []) returned an error: shutdown: failed to start child: DB
    ** (EXIT) an exception was raised:
        ** (UndefinedFunctionError) function :mnesia.create_schema/1 is undefined (module :mnesia is not available)
            :mnesia.create_schema([:"dynamic_store@127.0.0.1"])
```

This is because distillery doesn't export mnesia by default. You need to tell distillery to export `:mnesia` by adding it to the `extra_applications` option in your mix application.

```elixir
  # Run "mix help compile.app" to learn about applications.
  def application do
    [
      extra_applications: [:logger, :mnesia],
      mod: {DS.Application, []}
    ]
  end
```
