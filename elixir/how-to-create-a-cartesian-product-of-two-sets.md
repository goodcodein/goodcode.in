---
id: 8
title: "How to create a Cartesian product of two sets"
date: 2017-09-14T10:11:13Z
layout: default
tags:
  - elixir
  - Cartesian-product
  - sets
  - comprehensions
  - for
  - Enum

---

Elixir has a very rich set of functions to work with collections in the `Enum` module.
The name `Enum` however, is a bit unfortunate. Enum makes me think of (enumerated data)[https://en.wikipedia.org/wiki/Enumerated_type] more than the enumerable.

Anyway, the other day I needed to compute the Cartesian product between to lists in my elixir app.
And, I knew there would be something for that in the `Enum` module. I went through all the documentation and couldn't find anything useful.
That is when it hit me that comprehensions are best suited for this and this is a piece of cake for comprehensions.

So, without further ado, here is the code to get a Cartesian product in elixir:

```
a = [1, 2, 3, 4]
b = [1, 2, 3, 4]

cp = for x <- a, y <- b, do: {x, y}
IO.inspect(cp)
# =>
# [{1, 1}, {1, 2}, {1, 3}, {2, 1}, {2, 2}, {2, 3}, {3, 1}, {3, 2}, {3, 3}]
```
