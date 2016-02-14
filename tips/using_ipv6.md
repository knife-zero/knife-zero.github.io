---
layout: tips
lang: en
title: Using IPv6
permalink: tips/using_ipv6/
---

If we want to use IPv6 address with bootstrapping and converge.

We should use address syntax below.

```shell
## Enclosed in []
$ knife zero bootstrap [::1]  -N v6vm -x vagrant -P vagrant --sudo

## Append ssh port to tail of string.
$ knife zero bootstrap [::1]:22 -N v6vm -x vagrant -P vagrant --sudo

## Set scope
$ knife zero bootstrap fe80::a00:27ff:fe60:59e0%en0 -N v6vm -x vagrant -P vagrant --sudo
```

And I recommend to use `knife_zero.host` to ssh attribute.

```shell
$ knife zero converge "name:v6vm" --attribute knife_zero.host -x vagrant -P vagrant --sudo
```
