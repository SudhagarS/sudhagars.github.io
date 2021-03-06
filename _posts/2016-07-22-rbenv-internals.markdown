---
layout: post
title:  "Rbenv - Internals"
date:   2016-07-21 16:00:00 +0530
categories: ruby
tags: ruby internals
excerpt: I have run across issues with rbenv before and everytime I would just search on the internet about it and apply the solution I find on a stackoverflow link. This time I thought I will try to understand how rbenv works.
---

Today I installed a new gem. When I tried to run the command provided by the gem, I got an error `zsh: command not found: wheneverize`. I realised I forgot to run `rbenv rehash`. So I ran it and this is what I got `rbenv: cannot rehash: /Users/shot/.rbenv/shims/.rbenv-shim exists`.

I searched about it and found a Github issues [link](https://github.com/rbenv/rbenv/issues/759) that had a good discussion on why this happens. The file `.rbenv-shim` gets created during the rehash process to indicate a rehash is in progress. If a previous rehashing was interrupted in the middle or couldn't finish for some reason, the file will still be there. It then needs to be deleted manually, so that you can rehash again.

I deleted the file, did a rehash and everything worked fine. I have run across issues with rbenv before and everytime I would just search on the internet about it and apply the solution I find on a stackoverflow link. This time I thought I will try to understand how rbenv works. So, next time something goes wrong, I will be well equipped to handle it myself. And also I get a kick out of knowing how my tools work underneath.

## Rbenv

`rbenv` is really simple at its core. You install different ruby versions with `rbenv install version` and pick a version for a specific project with `rbenv local version` and set a global ruby version with `rbenv global version`.

Rbenv puts different Ruby versions in `~/.rbenv/versions`.

### Shims 

If you go into ~/.rbenv, you will find another directory `shims`. Shims? What is it? Here is its [wikipedia](https://en.wikipedia.org/wiki/Shim_(computing)) description.

> *In computer programming, a shim is a small library that transparently intercepts API calls and changes the arguments passed, handles the operation itself, or redirects the operation elsewhere.*

This is exactly what rbenv shims do. A rbenv shim intercepts a ruby related command and then calls `rbenv exec` which takes care of running the right executable.

How does rbenv intercept ruby commands? It does so by putting all possible commands as separate executable files in `~/.rbenv/shims` directory and adding this directory before all the other directories in `$PATH` env variable. So when you run `ruby -v`, the system will find the shim script `ruby` in `~/.rbenv/shims` directory and execute it.

Lets see what's inside a shim script.

{% highlight shell %}
#!/usr/bin/env bash
set -e
[ -n "$RBENV_DEBUG" ] && set -x

program="${0##*/}"
if [ "$program" = "ruby" ]; then
  for arg; do
    case "$arg" in
      -e* | -- ) break ;;
      */* )
        if [ -f "$arg" ]; then
          export RBENV_DIR="${arg%/*}"
          break
        fi
        ;;
    esac
  done
fi

export RBENV_ROOT="/Users/shot/.rbenv"
exec "/usr/local/Cellar/rbenv/1.0.0/libexec/rbenv" exec "$program" "$@"
{% endhighlight%}

Something you will observe when you open different files in the shims folder is that all the files have exactly the same content. Be it `.rbenv/shims/rails` or `.rbenv/shims/bundler` they are all the same.

Here is a translation of that shell script into pseudo-code for those who aren't familiar with shell scripting.

{% highlight ruby %}
set 'e' so that the script exits when it encounters an error.
if RBENV_DEBUG is set, set 'x' to print every command executed in this session. (Just trying running `RBENV_DEBUG=1 ruby -v`)

store the command name into $program. (If the command was rails, $program == "rails")
if $program == "ruby"
  for 'arg' in (each command line arguments)
    if `arg` startswith '-e' or equals '--'
      break out of the loop
    if `arg` matches the pattern '*/*' and `arg` == a regular file
      export RBENV_DIR=(first part of the path i.e. directory name)
      break out of the loop 

export RBENV_ROOT="/Users/shot/.rbenv"
exec "/usr/local/Cellar/rbenv/1.0.0/libexec/rbenv" exec "$program" "$@"
{% endhighlight %}

So a shim file on its own is not doing much. It sets up the environment variables `RBENV_DIR` and `RBENV_ROOT`. It then executes the command `/usr/local/Cellar/rbenv/1.0.0/libexec/rbenv exec original-command original-args`.

eg. `rails s` is interpreted by `~/.rbenv/shims/rails` which then runs `rbenv exec rails s`.

### rbenv exec

Rbenv exec [script](https://github.com/rbenv/rbenv/blob/master/libexec/rbenv-exec) takes over from the shim script.

From the documentation:
{% highlight html %}
Runs an executable by first preparing PATH so that the selected Ruby
version's 'bin' directory is at the front.
{% endhighlight %}

Lets examine how it works by going through the steps involved. We will use `rbenv exec rails s` here as an example.

1. Rbenv exec starts off by finding the right Ruby version to apply. To find the right version it runs `rbenv version-name`. `rbenv version-name` first looks at the current directory for a local version file named `.ruby-version`. If it exists it reads the version from there. Else it reads the version from the global version file at `~/.rbenv/version`.

    `RBENV_VERSION=2.2.3`

2. It then finds the command which is the first argument to `exec`.

    `RBENV_COMMAND=rails`

3. It then runs `rbenv which cmd` to find the path of the right executable. `rbenv which cmd` finds the right path by using the values of `RBENV_VERSION` and `RBENV_COMMAND`.
    
        -> rbenv which rails
        /Users/shot/.rbenv/versions/2.2.3/bin/rails

      `RBENV_COMMAND_PATH=/Users/shot/.rbenv/versions/2.2.3/bin/rails`

5. It then trims the last part of `RBENV_COMMAND_PATH` to the find the value of `RBENV_BIN_PATH`. This path is then prepended to the $PATH environment variable.
        
      `RBENV_BIN_PATH=/Users/shot/.rbenv/versions/2.2.3/bin`

      `export PATH="${RBENV_BIN_PATH}:${PATH}"`

6. Finally the original command is run. Now the system will find the right binary instead of the shim.

      `rails s`

So to summarise, when you run `rbenv exec rails s`, it is roughly turned into `PATH="~/.rbenv/versions/2.2.3/bin:$PATH" rails s` by rbenv exec.

### rbenv rehash

This commands jobs is to create shim scripts. After you install a gem, its shim won't be there. Hence the need to run `rbenv rehash` everytime after you install a gem.

Here is step by step breakdown on how shims are created.

1. It sees if the directory `~/.rbenv/shims` exists. If it doesn't, it is created.

2. It then checks if the shim prototype file `.rbenv/shims/.rbenv-shim` exists. If it exists, it means there is already an instance of rehash running. So the current program exits.

3. If it doesn't, create it and also acquire a lock on it. Put the contents of shim script into the prototype file.

4. Iterate through all the files in bin directories of all versions of Ruby installed. For each file, create a shim script in `shims` directory and copy the contents of the prototype file into the shim script. Now you know why all the shim scripts are the same.

5. Delete the prototype file.

So that wraps up this post on rbenv internals. I will update this post if I happen to read more on other topics like rbenv hooks and plugins. 

I hope it gave you a good idea on how rbenv works. Happy hacking!


