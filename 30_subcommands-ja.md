---
layout: page-ja
lang: ja
title: Subcommands
---

各サブコマンドで指定できるオプションは`knife zero SUBCOMMAND --help`で確認できます。

## [zero bootstrap](#bootstrap)

リモートNodeの初期化を行います、次の機能をもちます。

- NodeにChef-Clientをインストールする。
- コンフィグ(client.rb)ファイルを設置または更新する。
- Chef-Clientを実行する。
    - 任意のAttribute(JSON)、run-listを指定可能。
    - スキップ可能。

オプションから指定したAttributesはNormal属性として永続扱いになります。

### Options(抜粋)

Chef本体の`knife bootstrap`からオプションを引き継いでいます。本体のバージョンにより、使えるオプションは変動します。

- `--converge/--no-converge`
    - Bootstrapコマンド実行時に、Chef-Clientを実行するかしないか切り替えます。
    - Bootstrap済みのNodeには、`client.rb`のみ書き換えることができます。
- `-N, --node-name NAME` (ChefCore)
    - Bootstrapするノードに任意の名前を付与します。
    - Searchクエリし易い名前をつけることができます。
- `-j JSON_ATTRIBS, --json-attributes` (ChefCore)
    - JSON文字列をNodeのAttributeとして与えます。
- `--json-attribute-file FILE`
    - `--json-attributes`とすることは一緒ですが、こちらはJSONファイルを指定します。
    - 文字列とファイルを同時に指定することはできません。
- `--remote-chef-zero-port`
    - リモートNodeでポートフォワードに使用するポートです。 (デフォルト: 18889)
    - 18889が他のサービスで使用されている場合に指定します。
- `-r, --run-list RUN_LIST` (ChefCore)
    - Bootstrap時に実行するレシピのランリストです。
- `--sudo` (ChefCore)
    - リモートNodeでChef-Clientの実行にsudoを使います。
- `-W, --why-run` (ChefCore)
    - Chef-Clientの実行時をWhy-Runモードで行います。
    - Chefのインストール、設定変更までは実際に行われます。


その他については`--help`を参照してください。


### Examples

WIP


## [zero converge](#converge)

Chef本体の`knife ssh`からオプションを引き継いでいます。本体のバージョンにより、使えるオプションは変動します。
BootstrapおよびChef-Client実行済み(Node情報収集ずみ)のNodeに対して、Chef-Clientを再度実行します。


### Options(抜粋)

WIP

### Examples

WIP

## [zero diagnose](#diagnose)

knife.rbから生成される設定を大まかに出力します。
設定内容の確認やバグ報告に使います。

```
$ knife zero diagnose 
Chef::Config
====================
---
:local_mode: true
:knife_zero: {}
:verbosity: 
:log_location: !ruby/object:IO {}
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"
:color: true
:log_level: :warn
:chef_repo_path: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground"
:chef_server_url: chefzero://localhost:8889
:repo_mode: everything

Knife::Config
====================
---
:verbosity: 0
:color: true
:editor: vim
:disable_editing: false
:format: summary
:remote_chef_zero_port: 
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"

Zero Bootstrap Config
====================
---
:encrypt: false
:ssh_user: root
:run_list: []
:first_boot_attributes: {}
:host_key_verify: true
:remote_chef_zero_port: 
:bootstrap_converge: true

Zero Converge Config
====================
---
:concurrency: 
:manual: false
:ssh_password_ng: false
:host_key_verify: true
:remote_chef_zero_port: 
:use_sudo: true
:override_runlist: 
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"
```

## [Configration](#configration)

knife.rbに設定することができる、Knife-Zero固有のオプションです。

- `knife[:automatic_attribute_whitelist]`
    - bootstrapおよびconvergeで収集するNodeの情報を抑制します。
    - 詳細はこちら。[Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456)

