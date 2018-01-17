---
id: 10
title: "how to process an ets table in chunks or batches"
date: 2018-01-17T10:01:48Z
layout: default
tags:
  - elixir
  - ets
  - batch
  - chunk
---

We have an etl module which stores a lot of data in an ets table. This is loaded from a file
using `:ets.file2tab` when the app starts and is written back to the disk using `:ets.tab2file` when the processing is done.

However, we have been running out of memory in one particular path of the module (this is the one which creates an export of this data).
Our current ets table on disk is at 8GB and with a server having 32GB of memory and the fact that elixir/erlang structures are immutable,
we are running out of memory when we do a few transformations on the ets data before the export.

Our code looked something like below:

```
@db_path '....'
:ets.file2tab(@db_path)
:ets.tab2list(:db)
|> Enum.map(fn ....)
|> Enum.filter(fn ....)
|> Enum.into(File.stream!("urls"))
```

Fortunately for us, `:ets` provides a way to process a batch of records at a time without processing all the data in one go.
For batch processing needs `:ets.match/3` function is your friend. With this knowledge in hand we changed our code to look like below:


```
defmodule BatchProc do
  @batch_size 100

  @db_path '....'
  def run do
    :ets.file2tab(@db_path)
    process(:ets.match(:db, {:"$1", :"$2"}, @batch_size))
  end

  def process({els, cnt}) do
    els
    |> Enum.map(&transform/1)
    |> Enum.filter(&filter/1)
    |> Enum.into(File.stream!("./urls.#{:erlang.unique_integer([:monotonic, :positive])}"))

    Process.list()
    |> Enum.each(&:erlang.garbage_collect/1)

    process(:ets.match(cnt))
  end

  def process(:"$end_of_table") do
    IO.puts("DONE")
  end

  defp transform(el), do: el
  defp filter(el), do: true
end
```

The key takeaway here is that the `:ets.match` takes a third argument which is an integer
and limits the number of rows returned. This is similar to the `LIMIT` clause in sql.
And, when you use `:ets.match/3`, it returns a list of elements and a special structure
called a continuation in a 2 element tuple `{els, continuation}`. This `continuation` can be
thought of like a database cursor. It can be used with `:ets.match(continuation)` to get the
next batch of elements. Once it reaches the end, you'll get `:"$end_of_table"` as the continuation
value. The above code uses this fact to terminate the recursion.
