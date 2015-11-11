---
layout: tips-ja
lang: ja
title: ノードとの接続にIPv6をつかう
permalink: tips/using_ipv6-ja/
---

Knife-Zeroでのノート管理にIPv6のアドレスを使いたい場合は、だいたい次の書式でアドレスを指定します。

```
## []で囲む
$ knife zero bootstrap [::1]  -N v6vm -x vagrant -P vagrant --sudo

## 末尾にSSHのポートを付与する
$ knife zero bootstrap [::1]:22 -N v6vm -x vagrant -P vagrant --sudo

## インターフェースのスコープを付ける
$ knife zero bootstrap fe80::a00:27ff:fe60:59e0%en0 -N v6vm -x vagrant -P vagrant --sudo
```

この場合、`zero converge`で指定するアトリビュートは`knife_zero.host`にしましょう。

```
$ knife zero converge "name:v6vm" --attribute knife_zero.host -x vagrant -P vagrant --sudo
```
