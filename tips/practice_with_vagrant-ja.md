---
layout: tips-ja
lang: ja
title: VagrantでKnife-Zero
permalink: tips/practice_with_vagrant-ja/
---

このコンテンツは現在ほぼ英語版のみです。

VagrantとKnife-Zeroの組み合わせは、あまりお勧めしないんですよ。  
そもそも通常のローカルモードで試したことを、本番とかに適用するツールなんです。まずChef-Zero(ローカルモード)の動作をみるならば、Test-KitchenのChef-Zeroプロバイダでやってみることをおすすめします。

-  [Getting Started knife-zero with test-kitchen](https://github.com/higanworks/knife-zero-with-kitchen).

それでもVagrantでKnife-Zeroを試したいならば、Vagrantのネットワークと、SSHのTCPフォワードが何なのかを調べてからやりましょう。

## ユーザさんプラクティス

- Vagrantでknife-zeroを使うためのベストプラクティス | 割り箸ポテチ
    - [http://blog.chopschips.net/blog/2015/08/25/vagrant-knife-zero-best-practice/](http://blog.chopschips.net/blog/2015/08/25/vagrant-knife-zero-best-practice/)
