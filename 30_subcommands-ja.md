---
layout: page-ja
lang: ja
title: Subcommands
---

各サブコマンドで指定できるオプションは`knife zero SUBCOMMAND --help`で確認できます。

## <a name="bootstrap">[zero bootstrap](#bootstrap)</a>

`knife zero bootstrap [SSH_USER@]FQDN (options)`

リモートNodeの初期化を行います、次の機能をもちます。

- NodeにChef-Clientをインストールする。
- コンフィグ(client.rb)ファイルを設置または更新する。
- Chef-Clientを実行する。
    - 任意のAttribute(JSON)、run-listを指定可能。
    - スキップ可能。

オプションから指定したAttributesはNormal属性として永続扱いになります。

### Options(抜粋)

Chef本体の[`knife bootstrap`](https://docs.chef.io/knife_bootstrap.html)からオプションを引き継いでいます。本体のバージョンにより、使えるオプションは変動します。

Knife-Zeroで追加したオプションと、よく聞かれるオプションは次の通りです。

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
- `-W, --why-run`
    - Chef-Clientの実行をWhy-Runモードで行います。
    - Chefのインストール、設定変更までは実際に行われます。


その他については`knife zero bootstrap --help`を参照してください。


### Examples

到達性と名前のパターン。

```
# ノードが解決可能なホスト名を持つ
$ knife zero bootstrap zero.example.com

# 任意の名前を付与
$ knife zero bootstrap zero.example.com --name zero.example.com

# ノードが解決可能なホスト名を持たない
$ knife zero bootstrap 210.152.xxx.xxx --name zero01

# SSHに使用するログイン名がubuntuで、sudoが必要
$ knife zero bootstrap 210.152.xxx.xxx --name zero01 -x ubuntu --sudo

または

$ knife zero bootstrap ubuntu@210.152.xxx.xxx --name zero01 --sudo
```

初回のChef-Client実行時にNormal Attributeを付与する。

```
# 文字列として渡す
$ knife zero bootstrap zero.example.com --json-attributes '{"mykey":"myval"}'

# ファイルから読み込む
$ knife zero bootstrap zero.example.com --json-attribute-file tmp/node.json
```

初回のChef-Client実行時に任意のレシピを実行する。

```
# 複数要素を持つランリストを指定する
$ knife zero bootstrap zero.example.com --run-list "role[base],recipe[iptables::www]"

# JSONファイルを同時に指定する
$ knife zero bootstrap zero.example.com --run-list "role[base],recipe[iptables::www]" --json-attribute-file tmp/node.json
```

Bootstrap済みNodeの、`client.rb`だけを更新。

```
$ knife zero bootstrap zero.example.com -N zero.example.com --no-converge
```

複数のNodeをGNU Parallelで同時にBootstrap。

```
$ parallel -j 5 knife zero bootstrap ::: nodeA nodeB nodeC...
```


## <a name="converge">[zero converge](#converge)</a>

`knife zero converge QUERY (options)`

BootstrapおよびChef-Client実行済み(Node情報収集ずみ)のNodeに対して、Chef-Clientを再度実行します。  
対象Nodeはクエリ結果で絞込みます。クエリの書式は[knife search](https://docs.chef.io/knife_search.html)の`SEARCH_QUERY`と同一です。

### Options(抜粋)

Chef本体の[`knife ssh`](https://docs.chef.io/knife_ssh.html)からオプションを引き継いでいます。本体のバージョンにより、使えるオプションは変動します。

Knife-Zeroで追加したオプションと、よく聞かれるオプションは次の通りです。

- `-a, --attribute ATTR` (ChefCore)
    - SSHの接続先として用いるattributeを指定します。
    - デフォルトで使用されるホスト名(FQDNおよびName)が接続先として利用できない場合に使用します。
    - なお、Knife-ZeroでBootstrapしたNodeでは、使用したホスト/IPアドレスを`knife_zero.host`として再利用できます。
- `-C, --concurrency NUM` (ChefCore)
    - 対象が複数台ある際に、並行して実行する数を指定します。
- `--remote-chef-zero-port`
    - リモートNodeでポートフォワードに使用するポートです。 (デフォルト: 18889)
    - 18889が他のサービスで使用されている場合に指定します。
- `-W, --why-run`
    - Chef-Clientの実行をWhy-Runモードで行います。
- `-o, --override-runlist RunlistItem,RunlistItem`
    - Nodeのランリストを上書き指定します。
    - このオプションを指定した場合、ローカルのNodeオブジェクトを更新しません。
- `--sudo/--no-sudo`
    - リモートNodeでChef-Clientの実行にsudoを使います。
- `--client-version [latest|VERSION]`
    - リモートNodeでChef-Clientを実行する前に、任意のバージョンに変更します。
    - この処理ではOmnibus-chefに含まれるRubyを使います。


### Examples

```
# すべてのNodeをConverge
$ knife zero converge "name:*" --attribute knife_zero.host

# すべてのNodeをConverge、5Nodeずつ並行。
$ knife zero converge "name:*" --attribute knife_zero.host --concurrency 5

# Environment == "production" のNodeをConverge
$ knife zero converge "chef_environment:production" --attribute knife_zero.host

# 任意のランリストを使用する
$ knife zero converge "name:*" --attribute knife_zero.host --override-runlist "role[patch]"
```

`--client-version`の使い方。

```
# chef-clientを実行する前に、最新バージョンに更新。
$ knife zero converge "name:*" --attribute knife_zero.host --client-version latest

# chef-clientを実行する前に、12.4.3をインストール。
$ knife zero converge "name:*" --attribute knife_zero.host --client-version 12.4.3

# chef-clientはwhy-runで実行し、変更はしない。
$ knife zero converge "name:*" --attribute knife_zero.host --client-version 12.4.3 --why-run
```

> Note:  
> Chefのオムニバスインストーラを使いたくない場合は`--client-version`は役に立ちません。  
> 自前でやるなら`knife ssh`で好きなコマンドを流してくればよいです。

## <a name="apply">[zero apply](#apply)</a>

`knife zero apply QUERY (options)` (※1.12.0で追加)

BootstrapおよびChef-Client実行済み(Node情報収集ずみ)のNodeに対して、スポットで任意のレシピを[Chef-Apply](https://docs.chef.io/ctl_chef_apply.html)で実行します。  
対象Nodeはクエリ結果で絞込みます。クエリの書式は[knife search](https://docs.chef.io/knife_search.html)の`SEARCH_QUERY`と同一です。

レシピはChef-Applyに標準入力から渡されます。

### Options(抜粋)

Chef本体の[`knife ssh`](https://docs.chef.io/knife_ssh.html)、および`zero converge`からオプションを引き継いでいます。本体のバージョンにより、使えるオプションは変動します。

`zero apply`で使用する主なオプションは次の通りです。

- `-a, --attribute ATTR` (ChefCore)
    - SSHの接続先として用いるattributeを指定します。
    - デフォルトで使用されるホスト名(FQDNおよびName)が接続先として利用できない場合に使用します。
    - なお、Knife-ZeroでBootstrapしたNodeでは、使用したホスト/IPアドレスを`knife_zero.host`として再利用できます。
- `-r, --recipe Recipe String or @filename`
    - レシピの文字列または、@付きでファイル名を渡します。
- `-C, --concurrency NUM` (ChefCore)
    - 対象が複数台ある際に、並行して実行する数を指定します。
- `-W, --why-run`
    - Chef-Applyの実行をWhy-Runモードで行います。
- `--sudo/--no-sudo`
    - リモートNodeでChef-Applyの実行にsudoを使います。
- `--client-version [latest|VERSION]`
    - リモートNodeでChef-Applyを実行する前に、任意のバージョンに変更します。
    - この処理ではOmnibus-chefに含まれるRubyを使います。

`zero apply`コマンド作成時のChef(12.6.0)では、chef-applyはChef-ServerからNodeオブジェクトをロードする手段がありません。  
現在、対応できそうなコードを本体にプルリクエストしています。


### Examples

```
# すべてのNodeにtmuxパッケージをインストール、文字列から
$ knife zero apply "name:*" -r "package 'tmux'"

# すべてのNodeのtmuxパッケージを更新、5Nodeずつ並行。
$ knife zero apply "name:*" --attribute knife_zero.host --concurrency 5 -r "package 'tmux' do action :upgrade end"

# すべてのNodeにファイルからレシピを実行
$ knife zero apply "name:*" -r @apply/maintenance.rb
```


## <a name="diagnose">[zero diagnose](#diagnose)</a>

knife.rbから生成される設定を大まかに出力します。特にオプションはありません。
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

