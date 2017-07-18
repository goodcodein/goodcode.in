---
id: 1
title: A good .iex.exs for your phoenix apps
date: 2017-07-14T00:00:00Z
tags:
  - phoenix
  - iex
  - repl
---

Having a good `.iex.exs` in your phoenix apps makes it easy to navigate in your iex repl.

Here is what I have in my phoenix apps:


```
# .iex.exs
alias GC.{
  Repo,
  User,
  Site,
  Entry,
}

import Ecto.Query

alias GC.Client, as: DC
alias GC.Web.Router.Helpers, as: RH

import IExHelpers

# lib/iex_helpers.ex

defmodule IExHelpers do
  alias GC.{Repo, User}
  def get_user, do: Repo.one(User, limit: 1)
  def routes, do: Mix.Task.run "phx.routes"
end

```

What are the tricks present in your `.iex.exs`? Please share :)
