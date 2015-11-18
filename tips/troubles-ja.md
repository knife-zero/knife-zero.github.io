---
layout: tips-ja
lang: ja
title: トラブル集
permalink: tips/troubles-ja/
---

## エラー、`Chef::Exceptions::ContentLengthMismatch: Response body length`.

たまに聞きますけど原因がイマイチ特定できません。  
`/var/chef/cache`以下を消したら解決することがあるようです。

[knife-zero/issues/#69](https://github.com/higanworks/knife-zero/issues/69)


