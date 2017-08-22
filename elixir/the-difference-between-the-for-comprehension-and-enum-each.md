---
id: 5
title: "the difference between the for comprehension and Enum.each"
date: 2017-08-22T11:44:07Z
layout: default
tags:
  - elixir
  - for
  - comprehension
  - Enum
---

Look at the code below and try to guess what happens when you run the following lines of code.

### for
```
for {:resp, f} <- [{:resp, 3}, :b, {:resp, 4}] do
  IO.inspect f
end

### Enum.each
```
Enum.each([{:resp, 3}, :b, {:resp, 4}], fn {:resp, f} ->
  IO.inspect f
end)
```

The for version chugs along just fine :) which might surprise you. The Enum.map version blows up as expected.

Be careful about using for in your code, especially your tests. I had a small test which was the following lines:


```
for {:resp, stat} <- stats do
  assert stat.meta == %{a: 3}
  assert stat.time_ms in 10..20
end
```

And this was passing every time even when I changed the assert to the code below.

```
for {:resp, stat} <- stats do
  assert stat.meta == nil
  assert stat.time_ms in 10..20
end
```

All because I had an incorrect pattern match. I had `{:resp, stat}` instead of a `{ {:resp, _id}, stat }`
