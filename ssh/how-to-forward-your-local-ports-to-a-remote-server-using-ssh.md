---
id: 1
title: "How to forward your local ports to a remote server using SSH"
date: 2017-07-21T08:14:00Z
layout: default
tags:
  - ssh
  - port
  - forward
  - local
  - remote
  - tunnel
---

SSH is a great tool which is used by Linux/Unix sysadmins all over the world.
One neat little thing that it allows you to do is: to connect to ports on a remote computer
through SSH. This process is also called tunneling or creating an SSH tunnel.
And the TCP traffic/communication that happens on this connection is encrypted over SSH.
So, you get security without opening up ports on your remote computers to the public.

Let us take a simple example of accessing a postgresql database which is present on a remote server (on one of the server providers like aws, digital ocean).
You don't want to open the 5432 (the port on which postgresql runs) port to the internet. As, this will allow everyone to try bruteforce attacks on your database.
So, you deny traffic on 5432. You can access this port from your local computer through an SSH tunnel.

The `~/.ssh/config` syntax for a tunnel is simple:

```ssh
Host myserver
  Hostname myserver.com or the IP 192.168.1.1
  Port 22
  User goodcode
  # forward our local port 4000 to the localhost:5432 on the remote server which is the postgresql server
  LocalForward 4000 127.0.0.1:5432
  # forward our local port 5000 to the localhost:6379 on the remote server which is the redis server
  LocalForward 5000 127.0.0.1:6379
```

Let us break it down:

  1. `Host myserver`: creates an ssh configuration with a name `myserver`. Which you can connect to using `ssh myserver`
  2. `Hostname 192.168.1.1`: signals ssh to connect to this port when you run `ssh myserver`
  3. `Port 22`: you can drop this if your port is the default port 22. However, if you are running ssh on a different port on the remote server, you can change this.
  4. `User goodcode`: this tells ssh to use the username goodcode when connecting using `ssh myserver`
  5. `LocalForward 4000 127.0.0.1:5432`: This is what creates the actual tunnel, Here we are forwarding our local port `4000` to the port `5432` on the remote server.
  6. `LocalForward 5000 127.0.0.1:6379`: Just to show that you can create multiple tunnels on the same SSH connection, I have also forwarded port 5000 to the redis instance on the remote server

Once we have this setup and open an ssh connection using `ssh myserver`. We can connect to postgresql and redis using the following commands

```bash
# connect to postgresql on the remote server
psql --host localhost --port 4000 database_name
# connect to the redis instance on the remote server
redis-cli -h localhost -p 5000
```

You can also pass all these options via the command line without creating an ssh config.

```
ssh -L 4000:localhost:5432 -L 5000:localhost:6379 --port 22  goodcode@192.168.1.1
```
