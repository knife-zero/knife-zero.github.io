---
layout: tips
lang: en
title: Use cinc-client instead?
permalink: tips/use_cinc-client_instead/
---

You can use the community build package version of Cinc-Client on remote nodes if you find it difficult to accept the license when you run Chef Infra Client for the first time or every time you upgrade.

[https://gitlab.com/cinc-project/client](https://gitlab.com/cinc-project/client)

> This information is as of December 2019.

Cinc-Client is a package that is simply changed default configration directory from`/etc/chef` to `/etc/cinc` from `chef-client`.  
`chef-client` symlinks to `cinc-client` is also created when install.

Therefore, it can be replaced simply by specifying the path to the configuration file at runtime as shown below.

- `chef-client --config /etc/chef/client.rb`

Or, create symlink from `/etc/cinc` to `/`etc/chef` may be works.

## When using cinc-client from bootstrap

CINC provides Chef's omnibus-installer compatible installation script.

[Download • CINC](https://cinc-project.gitlab.io/download/)

knife-zero 2.2.0以降であれば、`--alter-project`オプションに`cinc`を渡せば自動でそちらを選択します。


The following is a sample to bootstrap an Ubuntu 16.04 node.

```shell
$ knife zero bootstrap ${YOUR_NODE_IP} \
  --sudo \
  --alter-project cinc \
  -N my-ubuntu16
```

It's able to bootstrap without problems.

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

If you want to make this `alter-project` configuration permanent in the configuration file (`knife.rb` or` config.rb`), you can write as follows.

```ruby
knife[:alter_project] = 'cinc'
```

## Change the version of cinc-client

The `--alter-project` option can be used with` zero converge` as well.

```shell
$ knife zero converge 'name:my-ubuntu16' \
  --client-version latest \
  --alter-project cinc \
  --sudo
```

This can also be omitted if the following is described in config.

```ruby
knife[:alter_project] = 'cinc'
```


## Replace the node where Chef Infra Client is already running with Cinc

First, remove Chef Infra Client with `knife ssh`.

```shell
$ knife ssh "name:my-ubuntu16" "sudo dpkg -r chef"
(Reading database ... 72883 files and directories currently installed.)
xxx.xxx.xxx.xxx Removing chef (15.3.14-1) ...
```

