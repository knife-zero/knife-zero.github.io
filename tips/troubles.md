---
layout: tips
lang: en
title: Troubles?
permalink: tips/troubles/
---

## I met `Chef::Exceptions::ContentLengthMismatch: Response body length` when bootstrap/converge.

The cause is unknown yet. But, there is a probability to solve by deleting /var/chef/cache.

[knife-zero/issues/#69](https://github.com/higanworks/knife-zero/issues/69)


