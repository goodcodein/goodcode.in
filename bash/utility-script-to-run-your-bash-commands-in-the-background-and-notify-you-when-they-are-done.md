---
id: 1
title: "Utility script to run your bash commands in the background and notify you when they are done"
date: 2017-07-18T08:38:08Z
layout: default
tags:
  - bash
  - utility
---

I have a small bash utility script that I use to run my commands in the background. For instance, I do a `git push` in the background
by running `b git push`

This utility runs the command in the background and notifies me when it is complete. It also tells me if the command succeeded or failed. Here it is:

```bash
#!/bin/bash
#Script to run a command in background redirecting the
#STDERR and STDOUT to /tmp/b.log in a background task

echo "$(date +%Y-%m-%d:%H:%M:%S): started running $*" >> /tmp/b.log
cmd="$*"
(/bin/bash -l -c "$cmd" 1>> /tmp/b.log 2>&1; notify-send --urgency=low -i "$([ $? = 0 ] && echo '/home/goodcode/.icons/ruby_green_icon.png' || echo error)" "$cmd")&>/dev/null &
```

Let us split this command and understand what it does

  1. `/bin/bash` invokes the bash command, this is useful when you want to execute arbitrary commands using bash after constructing them in a script.
  2. `-l` uses the login option
  3. `-c` to pass the actual command
  4. `2>&1` to redirect the standard error stream to the standard out stream
  5. `1>> /tmp/b.log` to redirect the standard out (which also has the standard error because of the above) to a file called `/tmp/b.log` in append mode.
  6. `;` separator between commands to run the next command regardless of the success of the first command
  7. `notify-send` to show a notification on our screen, this is executed once the first command is finished
  8. `$([ $? = 0 ] && echo '/home/goodcode/.icons/ruby_green_icon.png' || echo error)`: `$?` gives us the exit value of the previous command and if it is equal to `0` it means the
      previous command was successful, and in this case it uses a green ruby icon, otherwise it uses a red error icon.
  9. `&> /dev/null` redirects the stdout and stderr of this entire command to `/dev/null` which is a null device, which discards all that is written to it. So, basically it is as if we are writing in to the void
  10. `&` adding `&` at the end backgrounds the command
