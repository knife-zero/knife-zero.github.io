---
layout: tips-ja
lang: ja
title: トラブル集
permalink: tips/troubles-ja/
---

## エラー、`Chef::Exceptions::ContentLengthMismatch: Response body length`.

net-ssh 2.9.2以下にある既知の問題が原因です。転送中でも`CHANNEL_CLOSE`を送ってしまうケースがあるようです。  
修正版のnet-ssh 2.9.4以降にアップグレードしてください。
