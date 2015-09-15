---
layout: page-ja
lang: ja
title: Configration
---

knife.rbに設定することができる、Knife-Zero固有のオプションです。

- `knife[:automatic_attribute_whitelist]`
    - bootstrapおよびconvergeで収集するNodeの情報を抑制します。
    - 詳細はこちら。[Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456)

オススメ設定。

```
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
  "ec2/ami_id/",
  "ec2/instance_id/",
  "ec2/instance_type/",
  "ec2/placement_availability_zone/",
  "chef_packages/"
]
```
