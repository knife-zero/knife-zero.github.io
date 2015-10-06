---
layout: page-ja
lang: ja
title: Getting Started
---

## Getting Started with knife-zero.

では、既存のサーバが2台あるとしましょう、それぞれ本番用(production)、開発用(development)あたりで。  
このGetting Startedで使うサーバについて、条件は次の通り。

- グローバルIP(もしくは実行マシンから到達可能)がついてます。
    - server1: 153.120.97.132 (development)
    - server2: 153.120.97.133 (production)
- sshのログインユーザは`ubuntu`で、sudoがパスワードなしで可能。

### Chef-Repoの準備

まずはChef-Repoの準備をしましょう。  
といってもディレクトリとknife設定ファイル(`knife.rb`)でOKです。あとは必要なときに勝手に作成されていきますから。

```
$ mkdir my_chef_repo
$ cd my_chef_repo
$ touch ./knife.rb
```

`knife.rb`は一旦この様に書いておきます。

```
local_mode true
chef_repo_path   File.expand_path('../' , __FILE__)

knife[:ssh_attribute] = "knife_zero.host"
knife[:use_sudo] = true

## SSHエージェントを使ってないなら、SSHログイン用の鍵へのファイルパスを指定しましょう。
# knife[:identity_file] = "~/.ssh/id_rsa"

## Nodeの各種属性(attributes)はローカルにJSONファイルとして保存されていきます。
## automatic_attribute_whitelist オプションは、Nodeから収集したAttributeのうち、保存する対象を絞ることができます。
## NodeもGitで管理する場合は、現状に依存するディスク使用量などは無視してよいでしょう。
knife[:automatic_attribute_whitelist] = [
  "fqdn/",
  "os/",
  "os_version/",
  "hostname",
  "ipaddress/",
  "roles/",
  "recipes/",
  "ipaddress/",
  "platform/",
  "platform_version/",
  "platform_version/",
  "cloud/",
  "cloud_v2/",
  "chef_packages/"
]
```

### サーバに Bootstrap をかける

とりあえず開発用サーバにBootstrapを実行します。特に記述がなければ、各操作はKnife-Zeroをインストールしたローカルのコンピュータ(Workstation)で実行します。

```
$ knife zero bootstrap 153.120.97.132 --ssh-user ubuntu

Connecting to 153.120.97.132

153.120.97.132 -----> Installing Chef Omnibus (-v 12)

...

153.120.97.132 Setting up chef (12.4.3-1) ...
153.120.97.132 Thank you for installing Chef!

153.120.97.132 Starting first Chef Client run...
153.120.97.132 Starting Chef Client, version 12.4.3
153.120.97.132 Creating a new client identity for server1 using the validator key.
153.120.97.132 resolving cookbooks for run list: []
153.120.97.132 Synchronizing Cookbooks:
153.120.97.132 Compiling Cookbooks...
153.120.97.132 WARN: Node server1 has an empty run list.
153.120.97.132 Converging 0 resources
153.120.97.132 
153.120.97.132 Running handlers:
153.120.97.132 Running handlers complete
153.120.97.132 Chef Client finished, 0/0 resources updated in 1.935799462 seconds
```

Bootstrap では次の内容を適用します。

- Omnibus-Chef(Chef-Client)をインストール。
- Chef-Client実行に必要な各種ファイルを作成します。
- 初回のChef-Clientを実行します。

これらはよほどの競合が無い限りは、サーバの振る舞いに変更を加えません。例えばそれまでサーバ上でChef-Soloが使われていたとしても、別にお互いに関係なく動作します。

実行に必要な各種ファイルとして、`/etc/chef`に次のファイルを作ります、Chef-Server/Clientの環境と一緒ですね。

```
(server1) $ sudo find /etc/chef
/etc/chef
/etc/chef/first-boot.json
/etc/chef/client.pem
/etc/chef/validation.pem
/etc/chef/client.rb
```

BootstrapしたサーバのNodeオブジェクトは`./nodes/`以下に作成します。  
knifeコマンドでこれらを管理することができます。

```
$ ls nodes/server1.json 
nodes/server1.json


$ knife node show server1
Node Name:   server1
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

`Node Name`には通常、サーバに設定されているホスト名が自動的に使われます。  
Bootstrap時に`--node--name` オプションを使うと、任意の名前をつけることもできます。

ホスト名の`server1`じゃなくて、`develop-server`にしてみます。Bootstrapは何回やっても構いません。

```
$ knife zero bootstrap 153.120.97.132 --ssh-user ubuntu --node-name develop-server
...

$ knife node list
develop-server

$ knife node show develop-server
Node Name:   develop-server
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

ついでに本番用サーバも同じようにBootstrapしておきましょう。

```
$ knife zero bootstrap 153.120.97.133 --ssh-user ubuntu --node-name production-server

...


$ knife node list
develop-server
production-server


$ knife node show production-server
Node Name:   production-server
Environment: _default
FQDN:        
IP:          153.120.97.133
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

### knife search と knife ssh

`nodes/*json`が出来たら、KnifeのSearchとSSHが使えるようになります。

- [knife search — Chef Docs](https://docs.chef.io/knife_search.html "knife search — Chef Docs")
- [knife ssh — Chef Docs](https://docs.chef.io/knife_ssh.html "knife ssh — Chef Docs")

Searchは対象オブジェクトと、キーのクエリを指定します。

```
$ knife search node "name:dev*"
1 items found

Node Name:   develop-server
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        


$ knife search node "platform:ubuntu"
2 items found

Node Name:   develop-server
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        

Node Name:   production-server
Environment: _default
FQDN:        
IP:          153.120.97.133
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

Searchで確認できる対象には、`knife ssh`でリモートコマンドを実行して回る事ができます。

```
$ knife ssh "platform:ubuntu" --ssh-user ubuntu hostname
153.120.97.132 server1
153.120.97.133 server2


$ knife ssh "platform:ubuntu" --ssh-user ubuntu sudo apt-get update
153.120.97.132 sudo: unable to resolve host server1
153.120.97.133 sudo: unable to resolve host server2

...

Reading package lists... Done
Reading package lists... Done


$ knife ssh "platform:ubuntu" --ssh-user ubuntu sudo apt-get install -y unattended-upgrades

...

$ knife ssh "platform:ubuntu" --ssh-user ubuntu sudo apt-get install -y unattended-upgrades
153.120.97.132 sudo: unable to resolve host server1
Reading package lists... Done
Building dependency tree       
Reading state information... Done
153.120.97.133 sudo: unable to resolve host server2
Reading package lists... Done
153.120.97.132 Suggested packages:
153.120.97.132   bsd-mailx
153.120.97.132 The following NEW packages will be installed:

...

153.120.97.133 Processing triggers for man-db ...
153.120.97.132 Setting up unattended-upgrades (0.76ubuntu1.1) ...
153.120.97.133 Processing triggers for ureadahead ...
153.120.97.133 Setting up unattended-upgrades (0.76ubuntu1.1) ...
```

### Converge

Bootstrapが済んだサーバには、とりあえず`zero converge`で **特に何も変更しない** Chef-Clientを実行できるようになっています。

```
$ knife zero converge "name:*" --ssh-user ubuntu
153.120.97.132 sudo: unable to resolve host server1
153.120.97.133 sudo: unable to resolve host server2
153.120.97.132 Starting Chef Client, version 12.4.3
153.120.97.133 Starting Chef Client, version 12.4.3
153.120.97.132 resolving cookbooks for run list: []
153.120.97.132 Synchronizing Cookbooks:
153.120.97.132 Compiling Cookbooks...
153.120.97.132 WARN: Node develop-server has an empty run list.
153.120.97.132 Converging 0 resources
153.120.97.132 WARN: Could not find whitelist attribute fqdn/.
153.120.97.132 WARN: Could not find whitelist attribute cloud/.
153.120.97.132 
153.120.97.132 Running handlers:
153.120.97.132 Running handlers complete
153.120.97.132 Chef Client finished, 0/0 resources updated in 1.612886172 seconds
153.120.97.133 resolving cookbooks for run list: []
153.120.97.133 Synchronizing Cookbooks:
153.120.97.133 Compiling Cookbooks...
153.120.97.133 WARN: Node production-server has an empty run list.
153.120.97.133 Converging 0 resources
153.120.97.133 WARN: Could not find whitelist attribute fqdn/.
153.120.97.133 WARN: Could not find whitelist attribute cloud/.
153.120.97.133 
153.120.97.133 Running handlers:
153.120.97.133 Running handlers complete
153.120.97.133 Chef Client finished, 0/0 resources updated in 1.970427916 seconds
```

これで準備はできました。

> メモ:  
> 念の為に言っておくと、この後にサーバに変更を行なうにあたって、無理してわざわざレシピを使わなければいけないということはありません。  
> Cookbookを使わずとも、`knife (ssh|search|node list)`だけで大体の操作はできるので、サーチやリモートコマンドを使える単に便利な管理台帳として使ったっていいんだよ。


### レシピを書いて、適用する

それでは、Chef-Repo内にレシピを作って、サーバに適用してみましょう。


`create_file`というCookbookを作ってみましょう、ファイルは`./cookbooks/`以下に作られます。

```
$ knife cookbook create create_file
** Creating cookbook create_file in /Users/sawanoboriyu/bitbucket/sawanoboly/knife-zero.gettingstarted/cookbooks
** Creating README for cookbook: create_file
** Creating CHANGELOG for cookbook: create_file
** Creating metadata for cookbook: create_file
```

Cookbookに含まれる、`cookbooks/create_file/recipes/default.rb`を次のように編集します。

```
file '/tmp/myenvironment' do
  content node.chef_environment
end
```

では早速、`develop-server`に適用してみましょう、`zero converge`に`--override-runlist`オプションで指定します。

```
$ knife zero converge "name:develop-server" --ssh-user ubuntu --override-runlist create_file
153.120.97.132 sudo: unable to resolve host server1
153.120.97.132 Starting Chef Client, version 12.4.3
153.120.97.132 WARN: Run List override has been provided.
153.120.97.132 WARN: Original Run List: []
153.120.97.132 WARN: Overridden Run List: [recipe[create_file]]
153.120.97.132 resolving cookbooks for run list: ["create_file"]
153.120.97.132 Synchronizing Cookbooks:
153.120.97.132   - create_file
153.120.97.132 Compiling Cookbooks...
153.120.97.132 Converging 1 resources
153.120.97.132 Recipe: create_file::default
153.120.97.132   * file[/tmp/myenvironment] action create
153.120.97.132     - create new file /tmp/myenvironment
153.120.97.132     - update content in file /tmp/myenvironment from none to 3bf305
153.120.97.132     --- /tmp/myenvironment	2015-xx-xx xx:xx:xx.846280326 +0900
153.120.97.132     +++ /tmp/.myenvironment20151006-17877-1itgu45	2015-xx-xx xx:xx:xx.846280326 +0900
153.120.97.132     @@ -1 +1,2 @@
153.120.97.132     +_default
153.120.97.132 WARN: Skipping final node save because override_runlist was given
153.120.97.132 
153.120.97.132 Running handlers:
153.120.97.132 Running handlers complete
153.120.97.132 Chef Client finished, 1/1 resources updated in 1.712681794 seconds
```

サーバ上にファイルができましたね。

```
(server1)$ cat /tmp/myenvironment 
_default
```

さて、実は`--override-runlist` はローカルのNodeファイルを更新しません。 Run Listはカラッポのままです。

```
$ knife node show develop-server
Node Name:   develop-server
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

このレシピの実行を永続的に登録したい場合、`node run_list add`コマンドを使用します。

- `knife node run_list add [NODE] [ENTRY[,ENTRY]] (options)`

```
$ knife node run_list add develop-server create_file
develop-server:
  run_list: recipe[create_file]

$ knife node show develop-server 
Node Name:   develop-server
Environment: _default
FQDN:        
IP:          153.120.97.132
Run List:    recipe[create_file]
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

今度は`--override-runlist`オプション無しで、`zero converge`を実行します。レシピが適用されていますね。

```
$ knife zero converge "name:develop-server" -x ubuntu
153.120.97.132 sudo: unable to resolve host server1
153.120.97.132 Starting Chef Client, version 12.4.3
153.120.97.132 resolving cookbooks for run list: ["create_file"]
153.120.97.132 Synchronizing Cookbooks:
153.120.97.132   - create_file
153.120.97.132 Compiling Cookbooks...
153.120.97.132 Converging 1 resources
153.120.97.132 Recipe: create_file::default
153.120.97.132   * file[/tmp/myenvironment] action create (up to date)
153.120.97.132 WARN: Could not find whitelist attribute fqdn/.
153.120.97.132 WARN: Could not find whitelist attribute cloud/.
153.120.97.132 
153.120.97.132 Running handlers:
153.120.97.132 Running handlers complete
153.120.97.132 Chef Client finished, 0/1 resources updated in 1.852618101 seconds
```

Knife-Zeroでサーバ(Node)管理のワークフローはChef-Server/Client環境とほぼ同じです。公式ドキュメントは参考になるので、一読を。

- [knife node — Chef Docs](https://docs.chef.io/knife_node.html "knife node — Chef Docs")

#### Environmentsを割り当てる

じゃあ本番用、開発用で処理を分ける一例、Environmentを使ってみましょう。

`knife environment create`で`development`と`production`と2つのEnvironmentを作成します。

```
$ knife environment create development --disable-editing
Created development

$ knife environment create production --disable-editing
Created production


$ ls -1 environments/
development.json
production.json
```

- [About Environments — Chef Docs](https://docs.chef.io/environments.html "About Environments — Chef Docs")

`node environment set`で、各サーバにそれぞれEnvironmentを割当ててみます。

- `knife node environment set NODE ENVIRONMENT`

```
$ knife node environment set develop-server development
develop-server:
  chef_environment: development

$ knife node environment set production-server production
production-server:
  chef_environment: production
```

Environmentをサーチのキーに使う場合、名称は`chef_environment`です。

```
$ knife search node "chef_environment:production"
1 items found

Node Name:   production-server
Environment: production
FQDN:        
IP:          153.120.97.133
Run List:    
Roles:       
Recipes:     
Platform:    ubuntu 12.04
Tags:        
```

では、`production-server`のRun-Listにも`create_file`を登録しましょう。

```
$ knife node run_list add production-server create_file
production-server:
  run_list: recipe[create_file]
```

両方のサーバを対象に`zero converge`を実行します。  
同じレシピを適用していますが、結果はそれぞれに割り当てられたEnvironmentによって違いますね。

```
$ knife zero converge "chef_environment:*" -x ubuntu
153.120.97.132 sudo: unable to resolve host server1
153.120.97.133 sudo: unable to resolve host server2
153.120.97.132 Starting Chef Client, version 12.4.3
153.120.97.133 Starting Chef Client, version 12.4.3
153.120.97.132 resolving cookbooks for run list: ["create_file"]
153.120.97.132 Synchronizing Cookbooks:
153.120.97.132   - create_file
153.120.97.132 Compiling Cookbooks...
153.120.97.132 Converging 1 resources
153.120.97.132 Recipe: create_file::default
153.120.97.132   * file[/tmp/myenvironment] action create
153.120.97.132     - update content in file /tmp/myenvironment from 3bf305 to 875b93
153.120.97.132     --- /tmp/myenvironment	2015-xx-xx xx:xx:xx.846280326 +0900
153.120.97.132     +++ /tmp/.myenvironment20151006-19739-ml9ybe	2015-xx-xx xx:xx:xx.698338024 +0900
153.120.97.132     @@ -1,2 +1,2 @@
153.120.97.132     -_default
153.120.97.132     +development
153.120.97.132 WARN: Could not find whitelist attribute fqdn/.
153.120.97.132 WARN: Could not find whitelist attribute cloud/.
153.120.97.132 
153.120.97.132 Running handlers:
153.120.97.132 Running handlers complete
153.120.97.132 Chef Client finished, 1/1 resources updated in 1.953228699 seconds
153.120.97.133 resolving cookbooks for run list: ["create_file"]
153.120.97.133 Synchronizing Cookbooks:
153.120.97.133   - create_file
153.120.97.133 Compiling Cookbooks...
153.120.97.133 Converging 1 resources
153.120.97.133 Recipe: create_file::default
153.120.97.133   * file[/tmp/myenvironment] action create
153.120.97.133     - create new file /tmp/myenvironment
153.120.97.133     - update content in file /tmp/myenvironment from none to ab8e18
153.120.97.133     --- /tmp/myenvironment	2015-xx-xx xx:xx:xx.291539816 +0900
153.120.97.133     +++ /tmp/.myenvironment20151006-17291-rpkr7j	2015-xx-xx xx:xx:xx.291539816 +0900
153.120.97.133     @@ -1 +1,2 @@
153.120.97.133     +production
153.120.97.133 WARN: Could not find whitelist attribute fqdn/.
153.120.97.133 WARN: Could not find whitelist attribute cloud/.
153.120.97.133 
153.120.97.133 Running handlers:
153.120.97.133 Running handlers complete
153.120.97.133 Chef Client finished, 1/1 resources updated in 2.218201444 seconds
```

できました？

何かよくわからないことがあったら、まずはChefの公式ドキュメントをみてください。繰り返しになりますが、Knife-ZeroはChef-Server環境と同じです。

- [All about Chef ... — Chef Docs](https://docs.chef.io/ "All about Chef ... — Chef Docs")

それか、Githubにイシュー(日本語歓迎)からはじめるとか、プルリクを送るとか。

- [higanworks/knife-zero | Github](https://github.com/higanworks/knife-zero "higanworks/knife-zero")

また、Twitterで適当に言及しておけば、いつのまにかこのドキュメントが更新されていることもあります。
