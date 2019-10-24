---
layout: tips-ja
lang: ja
title: cinc-client をつかいたい
permalink: tips/use_cinc-client_instead-ja/
---

Chef Infra Clientの初回実行時や、バージョンアップのたびにライセンスに同意するオプションを付けるのが面倒な場合、リモートノードではコミュニティビルドパッケージ版のCinc-Clientを使うこともできます。

[https://gitlab.com/cinc-project/client](https://gitlab.com/cinc-project/client)

> この情報は2019年10月時点のもので、Cincパッケージプロジェクトは始まったばかりです。配布の方法など現状とちがっているかもしれません。

`cinc-client`は`chef-client`からエントリーポイントと設定ディレクトリを`/etc/chef`から`/etc/cinc`に変更しただけのパッケージです。  
そのため、下記2箇所をsymlinkでつないでしまえば、knife-zeroからみて全くおなじように扱うことができます。

- `/usr/bin/chef-client` => `/usr/bin/cinc-client`
- `/etc/chef` => `/etc/cinc`


## bootstrap時からcinc-clientを使用する場合

bootstrapにはもともと便利なオプションが用意されているので、それを使うだけでcincをインストールすることができます。

パッケージは [http://downloads.cinc.sh](http://downloads.cinc.sh) で配布されています。

- `--bootstrap-preinstall-command` でリンクを作成
  - 例: `--bootstrap-preinstall-command "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef"`
- `--bootstrap-install-command` でパッケージインストール
  - 例: `--bootstrap-install-command "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"`


もともとのChef Infra Clientのようにディストリビューションを自動判定するインストーラーがまだ無いので、パッケージを自分で指定するとよいでしょう。

以下はUbuntu 16.04のノードをbootstrapするサンプルです。

```shell
$ knife zero bootstrap ${YOUR_NODE_IP} \
  --sudo \
  -N my-ubuntu16 \
  --bootstrap-preinstall-command "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef" \
  --bootstrap-install-command "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"
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

このbootstrap設定を設定ファイル(`knife.rb`や`config.rb`)に永続化する場合、次のように書いておくことができます。

```ruby
knife[:bootstrap_preinstall_command] = 'sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef'
CINC_VERSON = ENV['CINC_VERSON'] || '15.3.14-1'
knife[:bootstrap_install_command] = "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_#{CINC_VERSON}_amd64.deb && sudo dpkg -i cinc_#{CINC_VERSON}_amd64.deb"
```

## cinc-clientのバージョンを変更する

`knife ssh`で任意のパッケージを入れたらよいです。

```shell
$ knife ssh "name:my-ubuntu16" "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"
```

## すでにChef Infra Clientが稼働しているノードをCincに入れ替える

まず`knife ssh`でChef Infra Clientを削除します。

```shell
$ knife ssh "name:my-ubuntu16" "sudo dpkg -r chef"
(Reading database ... 72883 files and directories currently installed.)
xxx.xxx.xxx.xxx Removing chef (15.3.14-1) ...
```

`/etc/chef`は残っているので、再利用しましょう。リンクの貼り方がbootstrapと少し違いますね。

```
$ knife ssh "name:my-ubuntu16" "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo ln -sf /etc/chef /etc/cinc"
```

最後にバージョン変更とおなじように任意のパッケージを入れましょう

```shell
$ knife ssh "name:my-ubuntu16" "wget -nv http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"

...

xxx.xxx.xxx.xxx Thank you for installing cinc, the community build based on Chef!
```

動作確認のため、`knife zero converge`をしてみましょう。


```shell
$ knife zero converge "name:my-ubuntu16"
xxx.xxx.xxx.xxx Starting Cinc Client, version 15.3.14

...

xxx.xxx.xxx.xxx  Cinc Client finished, x/xxx resources updated in xx seconds
```
