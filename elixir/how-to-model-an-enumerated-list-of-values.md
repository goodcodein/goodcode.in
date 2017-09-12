---
id: 7
title: "How to model an enumerated list of values"
date: 2017-09-12T15:09:05Z
layout: default
tags:
  - elixir
  - enumerated
  - values
---

When dealing with things like `statuses` you may need to store and access a fixed
set of values. The following is one of way of doing it


```
defmodule ProductStatus do
  def active, do: :active
  def inactive, do: :inactive
  def cancelled, do: :cancelled
end
```

If you have a lot of values like these, the above code may become tedious. Metaprogramming to the rescue :)

```
defmodule ProductStatus do
  @statuses ~w[
    active
    inactive
    cancelled
  ]a

  for event <- @events do
    def unquote(event)(), do: unquote(event)
  end
end
```

This allows you to use statuses as `ProductStatus.active`
