---
title: Annotating variables with underscore variables to make code more readable
date: 2017-07-14
tags:
  - elixir
  - readability
---

We should always strive to make our code as readable as possible. Underscore variables
aid is in making our code more readable by annotating literal values with meanings.

Look at the following variations

## without any underscore variable annotation

```elixir
Tentacat.Contents.find(@owner, @repo, "", client)
```
While reading the code, it is difficult to know what the third parameter is. In most these cases, I navigate to the actual function definition and read the variable name.

## with underscore annotations
Here is an improved version of the same code with and underscore variable annotation. It makes it crystal clear that the third argument is a path.
```elixir
Tentacat.Contents.find(@owner, @repo, _path = "", client)
```

What are the techniques you use in your code to make it more readable?
