---
layout: tips
lang: en
title: Practice with Vagrant
permalink: tips/practice_with_vagrant/
---

You can test cookbooks easily by Test-Kitchen before manage remote nodes instead of using knife-zero for vagrant VM.

See [Getting Started knife-zero with test-kitchen](https://github.com/higanworks/knife-zero-with-kitchen).

### Or, Try knife-zero simply with Vagrant.

> **For Your Information** :  
> If only you want to try `chef-zero` or `chef-client localmode` (For instance: migrate from chef-solo), You should use [chef-zero provisioner(Vagrant)](http://docs.vagrantup.com/v2/provisioning/chef_zero.html) with vagrant.
> The knife-zero will provides similar usage with the chef-zero provisioner on vagrant, but for remote node.

Set local_mode as default to `knife.rb`.

```
$ echo 'local_mode true' >> knife.rb
```

Add host-only network to vagrant vm(strongly recommended).

```
Vagrant.configure(2) do |config|
  config.vm.box = "opscode-ubuntu-14.04"
  config.vm.network "private_network", ip: "192.168.33.10"
end
```


Retrieve ssh-config.

```
$ vagrant up
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2201
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/sawanoboriyu/worktemp/knife-zero-vagrant/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

#### Case: Use name as address for ssh

Bootstrap with ssh options and `--sudo` to host-only address. And set ipaddress to name with `-N` option.

```
$ ./bin/knife zero bootstrap 192.168.33.10 -i ./.vagrant/machines/default/virtualbox/private_key -N 192.168.33.10 -x vagrant --sudo

WARN: No cookbooks directory found at or above current directory.  Assuming /Users/sawanoboriyu/worktemp/knife-zero-vagrant.
Connecting to 192.168.33.10
192.168.33.10 Installing Chef Client...

...
```

Run zero converge with `-a name` option.

> Caution: `-a(--attribute) name` option doesn't work since chef 12.1.0.
> Please use specific attribute until fix it.
> I've already create PR for fix. Please wait for merge to use name attribute. https://github.com/chef/chef/pull/3195

```
$ knife zero converge "name:*" -x vagrant -i ./.vagrant/machines/default/virtualbox/private_key --sudo -a name
WARN: No cookbooks directory found at or above current directory.  Assuming /Users/sawanoboriyu/worktemp/knife-zero-vagrant.
192.168.33.10 Starting Chef Client, version 12.0.3
192.168.33.10 resolving cookbooks for run list: []
192.168.33.10 Synchronizing Cookbooks:
192.168.33.10 Compiling Cookbooks...
192.168.33.10 [2015-02-04T04:08:04+00:00] WARN: Node 192.168.33.10 has an empty run list.
192.168.33.10 Converging 0 resources
192.168.33.10 
192.168.33.10 Running handlers:
192.168.33.10 Running handlers complete
192.168.33.10 Chef Client finished, 0/0 resources updated in 6.571334535 seconds
...
```

#### Case: Use network specific attribute

Bootstrap with ssh options and `--sudo` to host-only address. 

```
$ knife zero bootstrap 192.168.33.10 -i ./.vagrant/machines/default/virtualbox/private_key -x vagrant --sudo

WARN: No cookbooks directory found at or above current directory.  Assuming /Users/sawanoboriyu/worktemp/knife-zero-vagrant.
Connecting to 192.168.33.10
192.168.33.10 Installing Chef Client...
192.168.33.10 --2015-02-03 16:44:56--  https://www.opscode.com/chef/install.sh
192.168.33.10 Resolving www.opscode.com (www.opscode.com)... 184.106.28.91
192.168.33.10 Connecting to www.opscode.com (www.opscode.com)|184.106.28.91|:443... connected.
192.168.33.10 HTTP request sent, awaiting response... 200 OK
192.168.33.10 Length: 18285 (18K) [application/x-sh]
192.168.33.10 Saving to: ‘STDOUT’
192.168.33.10 
100%[======================================>] 18,285      --.-K/s   in 0.002s  
...
```

The knife-zero creates `node.knife_zero.host` attribute when bootstrap.

Run zero converge with `-a knife_zero.host` option.

```
$ ./bin/knife zero converge "name:vagrant.vm" -x vagrant -i ./.vagrant/machines/default/virtualbox/private_key --sudo -a knife_zero.host

192.168.33.10 Starting Chef Client, version 12.0.3
192.168.33.10 resolving cookbooks for run list: []
192.168.33.10 Synchronizing Cookbooks:
192.168.33.10 Compiling Cookbooks...
192.168.33.10 [2015-02-03T17:03:37+00:00] WARN: Node vagrant.vm has an empty run list.
192.168.33.10 Converging 0 resources
192.168.33.10 
192.168.33.10 Running handlers:
192.168.33.10 Running handlers complete
192.168.33.10 Chef Client finished, 0/0 resources updated in 6.245413202 seconds
```
