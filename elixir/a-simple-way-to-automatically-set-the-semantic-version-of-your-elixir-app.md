---
id: 6
title: "A simple way to automatically set the semantic version of your Elixir app"
date: 2017-09-12T07:47:32Z
layout: default
tags:
  - elixir
  - Phoenix
  - Semver
  - Version
  - Phoenix
  - Git
---

There is a neat trick which I bumped into while doing Rails development which I've
been using to set the semver value of my Elixir Apps.


This works if you use Git for your version control. The basic idea is to use git tags,
and the number of commits since the git tag to generate your version number. Elixir
allows you to use a version string like below (You can read more about this at https://hexdocs.pm/elixir/Version.html)

    [MAJOR].[MINOR].[PATCH]-[pre_release_info]+[build_info]

When I want to bump the major or the minor version, I create a tag with the version information e.g. `v1.4` using the command

    git tag v1.4 --annotate --message 'Version 1.4'
    git push --tags --all

I use the git describe command to get the major, minor and the patch info. A part of the describe output also goes into the build information

    git describe
    # => v1.4-270-fa78ab71e
    # => major.minor-patch-git_commit_id

Putting all of this together, I have the following in my mix config, it also uses the BUILD_NUMBER passed by Jenkins (the build server that we use)

```
defmodule Dan.Mixfile do
  use Mix.Project

  def project do
    [app: :dan,
     version: app_version(), # call out to another function which generates the version
     # ...
    ]
  end

  # ...

  def app_version do
    # get suffix
    build_number = System.get_env("BUILD_NUMBER")
    suffix = if build_number, do: ".build-#{build_number}", else: build_number # => .build-443

    # get git version
    {git_desc, 0} = System.cmd("git", ~w[describe])
    ["v" <> major_minor, patch, git_commit_id] = git_desc |> String.trim |> String.split("-") # => ["v1.4", "270", "fa78ab71e"]
    "#{major_minor}.#{patch}+ref-#{git_commit_id}#{suffix}" # => 1.4.270+ref-fa78ab71e.build-443
  end

end
```

Creating such a beautiful version number without showing it anywhere wouldn't be very useful :)
I usually put the version information of the app in a footer and the head inside a meta tag (if it is a phoenix app)

```
defmodule Dan do

  # cache the app_version during build time
  @app_version Mix.Project.config[:version]
  def app_version, do: @app_version

end
```

Inside the `app.html`

```
<!doctype html>
...
<meta name="version" content="<%= Dan.app_version %>">
...
```

So, now when something goes wrong I can take a look at the current version of the app by visiting a page and know which precise git commit reproduces the problem.
Our QA team too uses this information when filing bug reports.


I also send this version info to my error monitoring and metric services like Rollbar and AppSignal


Hope you find this technique useful :)
