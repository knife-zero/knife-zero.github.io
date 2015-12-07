---
layout: tips-ja
lang: ja
title: トラブル集
permalink: tips/troubles-ja/
---

## エラー、`Chef::Exceptions::ContentLengthMismatch: Response body length`.

net-ssh 2.9.2以下にある既知の問題が原因です。転送中でも`CHANNEL_CLOSE`を送ってしまうケースがあるようです。  
すでにPRによってパッチが提供されており、3系(master)で修正、および`2-9-release`ブランチにもバックポートされています。

このエラーがでやすい環境では、net-sshの`2-9-release`ブランチをつかいましょう。
