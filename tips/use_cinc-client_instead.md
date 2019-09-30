---
layout: tips
lang: en
title: Use cinc-client instead?
permalink: tips/use_cinc-client_instead/
---

You can use the community build package version of Cnic-Client on remote nodes if you find it difficult to accept the license when you run Chef Infra Client for the first time or every time you upgrade.

[https://gitlab.com/cinc-project/client](https://gitlab.com/cinc-project/client)

> This information is as of October 2019, and the Cnic package project has just begun. The distribution method may be different from the current situation.

Cinc-Client is a package that just changed the entry point and configuration directory from `chef-client` to `cnic-client` and from `/etc/chef` to `/etc/cinc`.  
Therefore, if you connect the following two places with symlinks, you can handle them exactly from knife-zero.

- `/usr/bin/chef-client` => `/usr/bin/cinc-client`
- `/etc/chef` => `/etc/cinc`

## When using cinc-client from bootstrap

Bootstrap has a convenient option by itself, so you can install cnic just by using them.

The packages are distributed at [http://downloads.cinc.sh](http://downloads.cinc.sh).

- `--bootstrap-preinstall-command` : use to create symlinks
  - e.g. `--bootstrap-preinstall-command "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef"`
- `--bootstrap-install-command` : use to install package
  - e.g. `--bootstrap-install-command "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"`

There is no installer that automatically detects the distribution like the original Chef Infra Client, so you can specify the package yourself.

The following is a sample to bootstrap an Ubuntu 16.04 node.


```shell
$ knife zero bootstrap ${YOUR_NODE_IP} \
  --sudo \
  -N my-ubuntu16 \
  --bootstrap-preinstall-command "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef" \
  --bootstrap-install-command "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"
```

We will able to apply without problems.

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

To make this bootstrap configuration permanent in the configuration file (`knife.rb` or `config.rb`), you can write as follows.

```ruby
knife[:bootstrap_preinstall_command] = 'sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo mkdir -p /etc/cinc && sudo ln -sf /etc/cinc /etc/chef'
CINC_VERSON = ENV['CINC_VERSON'] || '15.3.14-1'
knife[:bootstrap_install_command] = "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_#{CINC_VERSON}_amd64.deb && sudo dpkg -i cinc_#{CINC_VERSON}_amd64.deb"
```


## Change the version of cnic-client

You can install any package version with `knife ssh`.

```shell
$ knife ssh "name:my-ubuntu16" "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"
```


## Replace the node where Chef Infra Client is already running with Cinc

First, remove Chef Infra Client with `knife ssh`.

```shell
$ knife ssh "name:my-ubuntu16" "sudo dpkg -r chef"
(Reading database ... 72883 files and directories currently installed.)
xxx.xxx.xxx.xxx Removing chef (15.3.14-1) ...
```

`/etc/chef` remains, so let's reuse it. The direction to create Symlink is slightly different from bootstrap.

```
$ knife ssh "name:my-ubuntu16" "sudo ln -sf /usr/bin/cinc-client /usr/bin/chef-client && sudo ln -sf /etc/chef /etc/cinc"
```

Finally, install an arbitrary package.

```shell
$ knife ssh "name:my-ubuntu16" "wget http://downloads.cinc.sh/files/stable/cinc/ubuntu/16.04/cinc_15.3.14-1_amd64.deb && sudo dpkg -i cinc_15.3.14-1_amd64.deb && rm -f cinc_15.3.14-1_amd64.deb"

...

xxx.xxx.xxx.xxx Thank you for installing cinc, the community build based on Chef!
```

Run `knife zero converge` to check the operation.

```shell
$ knife zero converge "name:my-ubuntu16"
xxx.xxx.xxx.xxx Starting Cinc Client, version 15.3.14

...

xxx.xxx.xxx.xxx  Cinc Client finished, x/xxx resources updated in xx seconds
```
