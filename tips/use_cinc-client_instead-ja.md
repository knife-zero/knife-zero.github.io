---
layout: tips-ja
lang: ja
title: cinc-client をつかいたい
permalink: tips/use_cinc-client_instead-ja/
---

Chef Infra Clientの初回実行時や、バージョンアップのたびにライセンスに同意するオプションを付けるのが面倒な場合、リモートノードではコミュニティビルドパッケージ版のCinc-Clientを使うこともできます。

[CINC](https://cinc-project.gitlab.io/)

> この情報は2019年12月時点のものです。

`cinc-client`は`chef-client`からデフォルトの設定ディレクトリを`/etc/chef`から`/etc/cinc`に変更しただけのパッケージです。  
`chef-client` => `cinc-client`へのSymlinkも作成されます。

そのため、下記のように設定ファイルへのパスを実行時に指定するだけで差し替えられるようになっています。

- `chef-client --config /etc/chef/client.rb`

または、`/etc/cinc => /etc/chef`をSymlinkにしてもよいでしょう。


## bootstrap時からcinc-clientを使用する場合

CINCはChefのomnibus-installer互換のインストールスクリプトを提供しています。

[Download • CINC](https://cinc-project.gitlab.io/download/)

knife-zero 2.2.0以降であれば、`--alter-project`オプションに`cinc`を渡せば自動でそちらを選択します。


以下はUbuntu 16.04のノードをbootstrapするサンプルです。

```shell
$ knife zero bootstrap ${YOUR_NODE_IP} \
  --sudo \
  --alter-project cinc \
  -N my-ubuntu16
```

問題なく適用できました。

```shell
$ knife node show my-ubuntu16
Node Name:   my-ubuntu16
Environment: _default
FQDN:        ubuntu-xenial
IP:          xx.xx.xx.xx
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 16.04
Tags:        
```

`alter-project`を設定ファイル(`knife.rb`や`config.rb`)に永続化する場合、次のように書いておくことができます。

```ruby
knife[:alter_project] = 'cinc'
```

## cinc-clientのバージョンを変更する

`zero converge`でも同様に`--alter-project`オプションが使えます。

```shell
$ knife zero converge 'name:my-ubuntu16' \
  --client-version latest \
  --alter-project cinc \
  --sudo
```

こちらもconfigに下記記述をしておけば省略可能です。

```ruby
knife[:alter_project] = 'cinc'
```


## すでにChef Infra Clientが稼働しているノードをCincに入れ替える

まず`knife ssh`でChef Infra Clientを削除します。

```shell
$ knife ssh "name:my-ubuntu16" "sudo dpkg -r chef"
(Reading database ... 72883 files and directories currently installed.)
xxx.xxx.xxx.xxx Removing chef (15.3.14-1) ...
```

After that, install cinc using `zero bootstrap` and` --alter-project`. At the time of replacement, it is safer to do `--no-converge`.

```shell
$ knife zero bootstrap ${YOUR_NODE_IP} \
  --sudo \
  --alter-project cinc \
  -N my-ubuntu16 \
  --no-converge
```

Let's try `knife zero converge` again to check the operation.

```shell
$ knife zero converge "name:my-ubuntu16"
xxx.xxx.xxx.xxx Starting Cinc Client, version 15.5.1

...

xxx.xxx.xxx.xxx  Cinc Client finished, x/xxx resources updated in xx seconds
```
