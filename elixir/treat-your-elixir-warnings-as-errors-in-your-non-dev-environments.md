---
id: 2
title: "Treat your elixir warnings as errors in your non dev environments"
date: 2017-07-19T10:36:54Z
layout: default
tags:
  - elixir
---

One of the bittersweet things about [Go](https://golang.org/) is its relentlessness with warnings.
You cannot compile your Go code which has warnings in it. This is great for the long term health of the project.
However, it is not so pleasant while writing the code. Elixir takes an opposite approach to this where even
errors which should break your build are treated as warnings. One such instance are [Behaviours](https://hexdocs.pm/elixir/behaviours.html).
If you have a module which implements a behaviour and does not implement a non optional callback, all elixir does is emit a warning!
It should really throw an error and stop the compilation. However, all hope is not lost! You can use an elixir compiler flag to treat
warnings as errors. All you need to do is add the following to your `mix.exs`:

```elixir

  def project do
    [app: :awesome_possum,
     #...
     # treat warnings as errors in non dev environments
     elixirc_options: [warnings_as_errors: Mix.env != :dev],
     #...]
  end

```

You can even hard code it to `true` and it will always treat warnings as errors. However, non dev is the sweet spot for me, as I may be testing incomplete code with warnings in dev.

You can also pass these options to via the cli like so:

```bash

# using elixirc
elixirc --warnings-as-errors awesome_possum.ex
# using mix
mix compile --warnings-as-errors

```
