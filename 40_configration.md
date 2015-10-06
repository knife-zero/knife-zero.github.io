---
layout: page
lang: en
title: Configration
---

## Recommended Configration

- `knife.rb`

```ruby
local_mode true
chef_repo_path   File.expand_path('../' , __FILE__)
cookbook_path    [ File.expand_path('../cookbooks' , __FILE__), File.expand_path('../site-cookbooks' , __FILE__)]

knife[:ssh_attribute] = "knife_zero.host"
knife[:use_sudo] = true
knife[:identity_file] = "PATH_TO_YOUR_PUBLIC_KEY"

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


## Knife-Zero specific configration for `knife.rb`

- `knife[:automatic_attribute_whitelist]`
    - add attribute whitelist to `client.rb` which is put on remote Node.

For example, on Amazon EC2 instance.

```ruby
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
