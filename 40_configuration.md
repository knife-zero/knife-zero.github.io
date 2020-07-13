---
layout: page
lang: en
title: Configuration
---

## Recommended Configuration

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
## If you use attributes from cookbooks for set credentials or dynamic values.
## This option is useful to managing node-objects which are managed under version controle systems(e.g git).
# knife[:default_attribute_whitelist] = []
# knife[:normal_attribute_whitelist] = []
# knife[:override_attribute_whitelist] = []
```
## Knife specific configuration for `knife.rb`

You can use any optional options from knife itself to configure the bootstrapping template, chefs client version, and more:

Further reading at: https://docs.chef.io/workstation/config_rb/#optional-settings

## Knife-Zero specific configuration for `knife.rb`

- `knife[:automatic_attribute_whitelist]`
    - add automatic attribute whitelist to `client.rb` which is put on remote Node.
    - preventing non-whitelisted attributes from being saved.
- `knife[:default_attribute_whitelist]`
- `knife[:normal_attribute_whitelist]`
- `knife[:override_attribute_whitelist]`
    - Ditto, see [About Attributes#whitelist-attributes — Chef Docs](https://docs.chef.io/attributes.html#whitelist-attributes)
- `knife[:appendix_config]`
  - Described later.

For example, on Amazon EC2 instance.

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
    - execute local command before bootstrap/converge.

```ruby
knife[:before_bootstrap] = 'logger "bootstrap start"'
knife[:before_converge]  = 'logger "before_converge"'
```

Knife-Zero will stop when their exit status is not 0.


## Add optional contents to client.rb on the remote node

This is an example of using the `--appendix-config PATH` option of` zero bootstrap`.

For example, if you want to change the log level during Chef execution on the remote node from the `:warning`(default) to `:error`.

Prepare the following file with an arbitrary name such as `append.rb`.

```
log_level :error
```

Executing bootstrap as follows adds the contents of `append.rb` at the end of `client.rb` for the remote node.

```
$ knife zero bootstrap --appendix-config ./append.rb ...(other options)
```

To set in `knfie.rb`, use `knife [:appendix_config]`.

This is not the file path, but specify the content itself.
```
knife[:appendix_config] = File.read(File.expand_path('clientrb_append.rb', __dir__))
```
