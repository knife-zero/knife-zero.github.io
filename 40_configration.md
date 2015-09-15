---
layout: page
lang: en
title: Configration
---

Knife-Zero specific configration for `knife.rb`

- `knife[:automatic_attribute_whitelist]`
    - add attribute whitelist to `client.rb` which is put on remote Node.

For example, recomemnd by me.

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
