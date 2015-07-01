---
layout: page-ja
lang: ja
title: Installation
---

## Knife-Zeroなど、Chefのプラグインインストール方法について

私見です。  
あなたがRubyのプログラムも作る場合は、他のプロジェクトと同様にRubyのバージョンマネージャ(rvm, rbenv)と組み合わせながらBundlerを使いましょう。

Rubyの管理が不要、または共同で作業用のサーバを用意する場合はChef-DKがいいと思います。


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

### Bundlerの例

Bundlerを使って、インストールするGemの関連ファイルをまとめる例を紹介します。

```
$ bundle install --binstubs --path vendor/bundle
```

`--path`オプションを使うと、指定したディレクトリ以下にRubyGemsをインストールします。

さっきのコマンドで次のようなツリーができます。

```
$ tree . -L 5
.
├── Gemfile
├── Gemfile.lock
├── bin
│   ├── chef-apply
│   ├── chef-client
│   ├── chef-shell
│   ├── chef-solo
│   ├── chef-zero
│   ├── coderay
│   ├── erubis
│   ├── ffi-yajl-bench
│   ├── htmldiff
│   ├── knife
│   ├── ldiff
│   ├── ohai
│   ├── pry
│   ├── rackup
│   ├── rake
│   ├── rspec
│   └── serverspec-init
└── vendor
    └── bundle
        └── ruby
            └── 2.1.0
                ├── bin
                ├── build_info
                ├── cache
                ├── doc
                ├── extensions
                ├── gems
                └── specifications

12 directories, 19 files
```

`--binstubs`オプションはインストールしたRubygemsが持つコマンドラインツールを`./bin(変更可)`以下に設置します。

こうしておくと`bundle exec`なしで実行できます。

```
$ ./bin/chef-client -v
Chef: 12.3.0


$ ./bin/knife zero 2>/dev/null 
Available zero subcommands: (for details, knife SUB-COMMAND --help)

** ZERO COMMANDS **
knife zero bootstrap FQDN (options)
knife zero chef_client QUERY (options) | It's same as converge
knife zero converge QUERY (options)
knife zero diagnose # show configuration from file
```


### gemコマンド

ワケありでGemコマンドでインストールする場合は`gem`コマンド。

```
$ gem install knife-zero
```
