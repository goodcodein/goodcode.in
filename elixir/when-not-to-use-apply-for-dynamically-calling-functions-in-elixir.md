---
id: 3
title: "When not to use apply for dynamically calling functions in Elixir"
date: 2017-07-19T11:20:12Z
layout: default
tags:
  - elixir
  - apply
  - dynamic
---

Elixir has a nice `apply` function which allows you to call any `module`'s `function` with a list of `arguments` normally called the `mfa`.
However, I see `apply` being used in places where it shouldn't be. Let us take the below example.

```elixir
defmodule WorkerBehaviour do
  @callback perform(job :: Job.t)
end

defmodule EmailWorker do
  @behaviour WorkerBehaviour
  def perform(job) do
  # ...
  end
end

defmodule ScreenshotWorker do
  @behaviour WorkerBehaviour
  def perform(job) do
  # ...
  end
end

defmodule Processor do
  def process_job(worker_module, job) do
    apply(worker_module, :perform, [job])
  end
end
```

In this example the `Processor.process_job` uses the `apply` function to send the job to the right worker.
However, there is a more readable version of this code. Just use the following:

```elixir
defmodule Processor do
  def process_job(worker_module, job) do
    worker_module.perform(job)
  end
end
```

Since, in this particular scenario we know **beforehand what the name of the function is and the number of arguments is the same for all modules**, we can directly
invoke the required function on the module using the above syntax. This makes your code more readable overall.
