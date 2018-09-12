---
layout: page-ja
lang: ja
title: Configuration
---

## ざっくりしたおすすめ設定

- `knife.rb`

```ruby
local_mode true
chef_repo_path   File.expand_path('../' , __FILE__)
cookbook_path    [ File.expand_path('../cookbooks' , __FILE__), File.expand_path('../site-cookbooks' , __FILE__)]

knife[:ssh_attribute] = "knife_zero.host"
knife[:use_sudo] = true
knife[:identity_file] = "PATH_TO_YOUR_PRIVATE_KEY"

knife[:automatic_attribute_whitelist] = %w[
  fqdn
  os
  os_version
  hostname
  ipaddress
  roles
  recipes
  ipaddress
  platform
  platform_version
  cloud
  cloud_v2
  chef_packages
]

### Optional.
## Cookbookやレシピで定義されたattributeを、認証情報や毎回値が変わるような動的情報を使っている際に使います。
## nodeオブジェクトをバージョン管理したい際に役に立つでしょう。
# knife[:default_attribute_whitelist] = []
# knife[:normal_attribute_whitelist] = []
# knife[:override_attribute_whitelist] = []
```


## knife.rbに設定することができる、Knife-Zero固有のオプション

- `knife[:automatic_attribute_whitelist]`
    - bootstrapおよびconvergeで収集するNodeの情報を抑制します。
    - 詳細はこちら。[Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456)
- `knife[:default_attribute_whitelist]`
- `knife[:normal_attribute_whitelist]`
- `knife[:override_attribute_whitelist]`
    - 同上、 詳しくは [About Attributes#whitelist-attributes — Chef Docs](https://docs.chef.io/attributes.html#whitelist-attributes)
- `knife[:appendix_config]`
  - 後述

たとえばEC2のインスタンスで使う時のオススメ設定。

```ruby
knife[:automatic_attribute_whitelist] = %w[
  fqdn
  os
  os_version
  hostname
  ipaddress
  roles
  recipes
  ipaddress
  platform
  platform_version
  cloud
  cloud_v2
  ec2/ami_id
  ec2/instance_id
  ec2/instance_type
  ec2/placement_availability_zone
  chef_packages
]
```

- `knife[:before_bootstrap]`, `knife[:before_converge]`
    - bootstrap/convergeを実行する直前に割り込みで実行するローカルコマンド。

```ruby
knife[:before_bootstrap] = 'logger "bootstrap start"'
knife[:before_converge]  = 'logger "before_converge"'
```

exitステータスが0以外なら停止します。

## リモートノードのclient.rbに任意の内容を追加する

`zero bootstrap` の `--appendix-config PATH` オプションを使用する例です。

たとえば、リモート側Chef実行時のログレベルをデフォルトの`:warning`から`:error`に変更したい場合。

`append.rb` など任意の名前で、次のファイルを用意します。

```
log_level :error
```

次のようにbootstrapを実行すると、リモートノードの`client.rb`末尾に`append.rb`の内容を追記します。

```
$ knife zero bootstrap --appendix-config ./append.rb ...(other options)
```

`knfie.rb`に記述する場合は、`knife[:appendix_config]`です。

こちらはファイルパスでなく、コンテンツそのものを指定します。

```
knife[:appendix_config] = File.read(File.expand_path('clientrb_append.rb', __dir__))
```
