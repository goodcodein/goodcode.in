---
id: 2
title: "How to do rate limiting of curl using bash and redis"
date: 2017-08-10T11:35:11Z
layout: default
tags:
  - bash
  - redis
  - rate-limit
  - curl
---

Recently, I had to do rate limiting while consuming an API from one of your providers.
I hacked together a simple script to do it using redis. Hope you find it useful

```elixir
#!/bin/bash

HOURLY_LIMIT=500
while true
do
  # we increment a key which is rounded off to the hour
  if (( $(redis-cli --raw INCR "provider:$(date +%Y%m%d%H)") < $HOURLY_LIMIT ))
  then
    echo "making request"
    curl -s "http://provider.com/url"
  else
    echo "limit reached sleeping"
    sleep 1m
  fi
done
```

You can tweak the `date +%Y%m%d%H` expression to `date +%Y%m%d%H%M` to apply a rate limit per minute.
