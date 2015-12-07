---
layout: tips
lang: en
title: Troubles?
permalink: tips/troubles/
---

## I met `Chef::Exceptions::ContentLengthMismatch: Response body length` when bootstrap/converge.

The net-ssh 2.9.2 (and older) will immediately send a CHANNEL_CLOSE even though there is still pending data in the output buffer.  
And this issue was fixed and already backported into branch `2.9-release`.

