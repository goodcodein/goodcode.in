---
id: 1
title: How to redirect to back or the root path similar to rails in a phoenix app
date: 2017-07-14T00:00:00Z
tags:
  - phoenix
  - redirect
  - root
  - back
  - referer
---

While writing a phoenix app you may want to redirect a user back to where they came
from. This is possible because [HTTP sends you a refer header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) which tells you which page/url the user came from.
Rails allows you to this using `redirect_to :back` in versions < 5 and `redirect_back(fallback: "/")` in versions >= 5. You can do something similar in Phoenix by using the following code snippet.

```elixir
def redirect_back(conn, fallback \\ "/") do
    case get_req_header(conn, "referer") do
      [referer] -> redirect conn, to: referer
      _         -> redirect conn, to: fallback
    end
  end
end
```

Which can be used as below.

```elixir

conn
|> put_flash(:info, "Success!")
|> redirect_back

# or

conn
|> put_flash(:info, "Success!")
|> redirect_back(_fallback = "/dash")

```
