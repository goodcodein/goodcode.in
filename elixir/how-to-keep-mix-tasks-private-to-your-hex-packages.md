---
id: 9
title: "How to keep mix tasks private to your hex packages"
date: 2017-10-26T09:24:13Z
layout: default
tags:
  - elixir
  - hex
  - mix
  - private
---

A few days ago I created a mix task for one of my hex packages. This package
was just a wrapper around the awesome [shipit](https://github.com/wojtekmach/shipit) package.
Now, this task is specific to my hex package and isn't going to be used by apps
which depend on my hex package. However, today I found my mix task happily listed
in the host apps' list of tasks. This is not a good thing :)

I initially tried changing the extension of the mix task to `.exs` but that didn't help.
The mix task was not even listed in my hex package now.

After a lot of digging I found two useful pieces of info which helped me get around this.

  1. [Mix](https://hexdocs.pm/mix/Mix.Tasks.Deps.html#module-dependency-definition-options) deps have an `:env` key which defaults to `:prod`. This means, when mix compiles a dependency, the value of Mix.env inside that dependency is set to `:prod`.

  2. [Mix compiler](https://hexdocs.pm/mix/Mix.Tasks.Compile.Elixir.html#module-configuration) has an `:elixirc_paths` flag which tells the compiler where to look for elixir source code.


Knowing this, all I had to do was:

  1. Move the mix tasks from `./lib/mix/tasks` to `./mix/tasks` so that it is not picked up by default.
  2. Change `mix.exs` by adding the following to the project, so that in the `:dev` env of the hex package our mix task is loaded/compiled.

            # mix.exs
            def project do
              [app: :honeybadger,
              # ...
               elixirc_paths: elixirc_paths(Mix.env),
              # ...
              ]
            end

            defp elixirc_paths(:dev), do: ["lib", "mix"]
            defp elixirc_paths(_), do: ["lib"]

*Now* my hex specific mix tasks are loaded only when I am working on my hex package. And the users of my hex package don't see my mix utility tasks.
