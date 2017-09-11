---
id: 3
title: "How to open your web browser with your app when you start your phoenix app"
date: 2017-09-11T13:13:33Z
layout: default
tags:
  - phoenix
---

I have added this line to my `.iex.exs` to automatically open my web browser when I start my phoenix app

```
# .iex.exs
spawn(fn -> :os.cmd('xdg-open http://localhost:4000/') end)
```

Hat tip to: https://github.com/gfvcastro/phoenix_open_browser
