---
layout: post
title:  "Rbenv - How it works"
date:   2016-07-22 16:00:00 +0530
categories: ruby
tags: ruby how-it
---

Today I installed a new gem. When I tried to run the command provided by the gem, I got an error `zsh: command not found: wheneverize`. I realised I forgot to run `rbenv rehash`. So I ran it and this is what I got `rbenv: cannot rehash: /Users/shot/.rbenv/shims/.rbenv-shim exists`.

I searched about it and found a Github issues [link](https://github.com/rbenv/rbenv/issues/759) that had a good discussion on why this happens. The file `.rbenv-shim` gets created during the rehash process to indicate a rehash is in progress. If a previous rehashing was interrupted in the middle or couldn't finish for some reason, the file will still be there. It then needs to be deleted manually, so that you can rehash again.

I deleted the file, did a rehash and everything worked fine. I have run across issues with rbenv before and everytime I would just search on the internet and apply the solution I find on a stackoverflow link. This time I thought I will try to understand how rbenv works so that I won't break my flow next time I run into such issues.

# Rbenv

`rbenv` is really simple at its core. You install different ruby versions with `rbenv install version` and pick a version for a specific project with `rbenv local version` and set a global ruby version with `rbenv global version`.

To understand how it all works, you need to understand these `shims` and `binstubs` first 
