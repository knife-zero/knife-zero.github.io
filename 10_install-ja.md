---
layout: page-ja
lang: ja
title: Installation
---

## Chef-DKに追加する場合

`chef gem`コマンドを使いましょう。

```
$ chef gem install knife-zero
```

## Rubygemsからインストールする場合

通常はBundlerを使います。`Gemfile`に`knife-zero`を追加します。

```ruby
gem 'knife-zero'
```

Bundleします。

```
$ bundle
```

ワケありでGemコマンドでインストールする場合は`gem`コマンド。

```
$ gem install knife-zero
```
