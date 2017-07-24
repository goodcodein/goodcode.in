---
id: 1
title: "Open your text editor with the migration file when you run mix ecto.gen.migration"
date: 2017-07-24T13:14:55Z
layout: default
tags:
  - ecto
  - elixir
  - phoenix
  - migration
  - editor
---

There is a neat little trick which I found while browsing [Ecto's code](https://github.com/elixir-ecto/ecto/blob/master/lib/mix/tasks/ecto.gen.migration.ex#L26)
Adding the following line to your `~/.bashrc` will open up your new migration file with the text editor of your choice

```bash
ECTO_EDITOR="code" # put the name of your editor here
export ECTO_EDITOR
```

Now, when you run `mix ecto.gen.migration`, it will open up your editor and you can modify your migration.

**I actually use neovim to edit my code. However, it doesn't open up from erlang. I tried running `:os.cmd 'nvim /tmp/a'` but it fails with an error about stdin not being found.**
