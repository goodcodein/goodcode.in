---
id: 2
title: Changes to your prod.exs before deploying phoenix apps using distillery
date: 2017-07-17T00:00:00Z
tags:
  - phoenix
  - distillery
  - prod.exs
---

While deploying my first phoenix app. I spent 2 hours trying to debug why my phoenix app wasn't showing up on http://localhost:4000.
I usually test it using `curl -v http://localhost:4000/` from my server. I had my head scratching for a long time before reading these lines in the `prod.exs` file

```elixir
# ## Using releases
#
# If you are doing OTP releases, you need to instruct Phoenix
# to start the server for all endpoints:
#
#     config :phoenix, :serve_endpoints, true
#config :phoenix, :serve_endpoints, true
```

As it says, uncomment the `:serve_endpoints` line if you are using distillery for deployments and save yourself some frustration :)
