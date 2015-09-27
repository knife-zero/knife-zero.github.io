---
layout: page
lang: en
title: Getting Started
---

## Getting Started with knife-zero.

You have 2 servers, production and development.

- servers have public ip address. (Please replace your IPs.)
    - server1: 153.120.97.132 (development)
    - server2: 153.120.97.133 (production)
- ssh login user is `ubuntu` which can sudo.

### Setup Chef-Repo

First, create your Chef repository.

```
$ mkdir my_chef_repo
$ cd my_chef_repo
$ touch ./knife.rb
```

Write settings into `knife.rb`.

```
local_mode true
chef_repo_path   File.expand_path('../' , __FILE__)

knife[:ssh_attribute] = "knife_zero.host"
knife[:use_sudo] = true

## use specific key file to connect server instead of ssh_agent(use ssh_agent is set true by default).
# knife[:identity_file] = "~/.ssh/id_rsa"

## Attributes of node objects will be saved to json file.
## the automatic_attribute_whitelist option limits the attributes to be saved.
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

### Bootstrap servers

Bootstraping development server. Unless otherwise stated, all commands have been running on the workstation.

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

Bootstrap does the following.

- Installs Omnibus-Chef to remote node.
- Create files for chef-client run.
- Run chef-client once.

These process will not affect to almost server's behavior.

There are created files in `/etc/chef` below by default. This behavior is same as chef-server/client environment.

```
(server1) $ sudo find /etc/chef
/etc/chef
/etc/chef/first-boot.json
/etc/chef/client.pem
/etc/chef/validation.pem
/etc/chef/client.rb
```

Node object will be created in `./nodes/` under current directory as json file.  
And, We can manage with knife command.

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

If you would like to use another `Node Name` of remote server, we can use specific name of it by passing `--node--name` option.

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

Similarly, also bootstrap production server.

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

### Search and SSH

We can search and run command via ssh.

- [knife search — Chef Docs](https://docs.chef.io/knife_search.html "knife search — Chef Docs")
- [knife ssh — Chef Docs](https://docs.chef.io/knife_ssh.html "knife ssh — Chef Docs")

Search with attributes.

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

Run command to nodes witch was found by query.

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

WIP

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

----

WIP...


See [knife-zero/README.md](https://github.com/higanworks/knife-zero/blob/master/README.md)

## How to manage bootstraped nodes.

WIP