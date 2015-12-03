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
  platform_version
  cloud
  cloud_v2
  chef_packages
]
```


## knife.rbに設定することができる、Knife-Zero固有のオプション

- `knife[:automatic_attribute_whitelist]`
    - bootstrapおよびconvergeで収集するNodeの情報を抑制します。
    - 詳細はこちら。[Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456)

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
