---
layout: page
lang: en
title: Subcommands
---

Options that can be specified in each sub-command can be found in `knife zero SUBCOMMAND --help`.

## <a name="bootstrap">[zero bootstrap](#bootstrap)</a>

`knife zero bootstrap [SSH_USER@]FQDN (options)`

Initializes the remote Node, it has the following features.

- Installs the Chef-Client to Node.
- Puts or updates the configuration (client.rb) file.
- Run the Chef-Client.
    - Any Attribute (JSON), and the run-list can be specified.

Attributes that you specify from the option will be persistent saved as Normal attribute.

### Options(excerpted)

Almost options are inheritance from [`knife bootstrap`](https://docs.chef.io/knife_bootstrap.html). Depending on the version of Chef, you can use options will vary.

And options that you added in Knife-Zero, frequently asked option is is as follows.

- `--converge/--no-converge`
    - Bootstrap without Chef-Client Run.(for only update client.rb)
- `-N, --node-name NAME` (ChefCore)
- `-j JSON_ATTRIBS, --json-attributes` (ChefCore)
- `--json-attribute-file FILE`
    -  A JSON file to be used to the first run of chef-client.
- `--remote-chef-zero-port`
    - Listen port on remote to SSH Port Forwarding.
    - Specify when TCP/18889 is being used by another service.
- `-r, --run-list RUN_LIST` (ChefCore)
- `--sudo` (ChefCore)
- `-W, --why-run`
    - Enable whyrun mode on chef-client run at remote node.
- `--appendix-config PATH`
    - Add the contents of the specified file to `client.rb` of the remote node.
- `--[no-]overwrite`
    - Overwrite local node object if node already exist. false (ask) by default


Please refer to the `knife zero bootstrap --help` for the other.


### Examples

Reachability to host and name.

```
# Node has a resolvable host name
$ knife zero bootstrap zero.example.com

# It will grant any name
$ knife zero bootstrap zero.example.com --name zero.example.com

# Node doesn't have a resolvable host name
$ knife zero bootstrap 210.152.xxx.xxx --name zero01

# The login name to connect via SSH is ubuntu, and sudo is necessary
$ knife zero bootstrap 210.152.xxx.xxx --name zero01 -x ubuntu --sudo

OR

$ knife zero bootstrap ubuntu@210.152.xxx.xxx --name zero01 --sudo
```

It will grant the Normal Attribute at the time of the first Chef-Client run.

```
# as String
$ knife zero bootstrap zero.example.com --json-attributes '{"mykey":"myval"}'

# from File
$ knife zero bootstrap zero.example.com --json-attribute-file tmp/node.json
```

Specify run-list to Chef-Client first run.

```
# Comma separated list
$ knife zero bootstrap zero.example.com --run-list "role[base],recipe[iptables::www]"

# with JSON Attributes
$ knife zero bootstrap zero.example.com --run-list "role[base],recipe[iptables::www]" --json-attribute-file tmp/node.json
```

only update client.rb on Node which was bootstrapped.

```
$ knife zero bootstrap zero.example.com -N zero.example.com --no-converge
```

Bootstrap multi-nodes via GNU Parallel.

```
$ parallel -j 5 knife zero bootstrap ::: nodeA nodeB nodeC...
```

## <a name="converge">[zero converge](#converge)</a>

`knife zero converge QUERY (options)`

Run Chef-Client to update for Node.
Target Nodes are based on the results of a search query. The query format is the same as `SEARCH_QUERY` of [knife search](https://docs.chef.io/knife_search.html).

### Options(excerpted)

Almost options are inheritance from [`knife ssh`](https://docs.chef.io/knife_ssh.html). And, taken over some of the options from [`chef-client`](https://docs.chef.io/ctl_chef_client.html).
 Depending on the version of Chef, you can use options will vary.


And options that we added in Knife-Zero, frequently asked option is as follows.

- `-a, --attribute ATTR` (ChefCore)
    - The attribute to use for opening the connection
    - If the Node was bootstrapped by Knife-Zero, you can reuse the host / IP address that you used `as knife_zero.host`.
- `-C, --concurrency NUM` (ChefCore)
- `--remote-chef-zero-port`
    - Listen port on remote to SSH Port Forwarding.
    - Specify when TCP/18889 is being used by another service.
- `-W, --why-run`
    - Enable whyrun mode on chef-client run at remote node.
- `-o, --override-runlist RunlistItem,RunlistItem`
    - Replace current run list with specified items for a single run. 
    - It skips save node.json on local
- `--sudo/--no-sudo`
- `--splay SECONDS`(Chef-Client)
- `--skip-cookbook-sync`
    - Use cached cookbooks without overwriting local differences from the server.
- `--client-version [latest|VERSION]`
    - Up or downgrade omnibus chef-client before converge.
    - This option uses ruby which is included by omnibus-chef on node.
- `-j, --json-attributes JSON_ATTRIBS`
    - Load attributes from a JSON file or URL.
    - JSON will be get from a work station, and then transferred to the remote node via SSH.
    - It will be try to get from local file path if you do not specify a schema.
    - It must be use with `--override-runlist` because to avoid updating local node object.
- `--chef-license ACCEPTANCE`
    - Used with the `--client-version` option.
    - ACCEPTANCE is selected from '`accept`', '`accept-no-persist`', or '`accept-silent`'.
    - Use to update the remote Chef Infra Client to version 15 or later.


### Examples

```
# Converge all Nodes
$ knife zero converge "name:*" --attribute knife_zero.host

# Converge all Nodes, concurrent in 5 node.
$ knife zero converge "name:*" --attribute knife_zero.host --concurrency 5

# Converge Nodes matched Environment == "production"
$ knife zero converge "chef_environment:production" --attribute knife_zero.host

# specified run-list
$ knife zero converge "name:*" --attribute knife_zero.host --override-runlist "role[patch]"
```

`--client-version` usage.

```
# Upgrade chef-client to latest and converge all Nodes
$ knife zero converge "name:*" --attribute knife_zero.host --client-version latest

# install specific version (e.g. 12.4.3) and converge all Nodes
$ knife zero converge "name:*" --attribute knife_zero.host --client-version 12.4.3

# Without converge
$ knife zero converge "name:*" --attribute knife_zero.host --client-version 12.4.3 --why-run
```

> Note:  
> If you don't want to use omnibus-chef installation, you should not use `--client-version` option.  
> Try run your specified command to upgrade chef-client as you like via `knife ssh`.

## <a name="apply">[zero apply](#apply)</a>

`knife zero apply QUERY (options)` (※ after v1.12.0)

Run [Chef-Apply](https://docs.chef.io/ctl_chef_apply.html) to execute spot tasks for Node from a single recipe.
Target Nodes are based on the results of a search query. The query format is the same as `SEARCH_QUERY` of [knife search](https://docs.chef.io/knife_search.html).

The recipe string are passed to chef-apply from stdin.

### Options(excerpted)

Almost options are inheritance from [`knife ssh`](https://docs.chef.io/knife_ssh.html) and `zero converge`. Depending on the version of Chef, you can use options will vary.


And options as follows.

- `-a, --attribute ATTR` (ChefCore)
    - The attribute to use for opening the connection
    - If the Node was bootstrapped by Knife-Zero, you can reuse the host / IP address that you used `as knife_zero.host`.
- `-r, --recipe Recipe String or @filename`
- `-j, --json-attributes JSON_ATTRIBS`
    - Load attributes from a JSON file or URL (retrieves from the remote node)
- `-C, --concurrency NUM` (ChefCore)
- `--remote-chef-zero-port`
    - Listen port on remote to SSH Port Forwarding.
    - Specify when TCP/18889 is being used by another service.
- `-W, --why-run`
    - Enable whyrun mode on chef-client run at remote node.
- `-o, --override-runlist RunlistItem,RunlistItem`
    - Replace current run list with specified items for a single run. 
    - It skips save node.json on local
- `--sudo/--no-sudo`
- `--client-version [latest|VERSION]`
    - Up or downgrade omnibus chef-client before converge.
    - This option uses ruby which is included by omnibus-chef on node.

chef-apply(12.6.0) does not have function to load the Node objects from Chef-Server.  
The bear in only limited use, please use the `--override-runlist` with `converge` if you need to retrieve objects from ChefRepo.


### Examples

```
# Install tmux to all nodes from String.
$ knife zero apply "name:*" -r "package 'tmux'"

# Update tmux by all nodes from String. 
$ knife zero apply "name:*" --attribute knife_zero.host --concurrency 5 -r "package 'tmux' do action :upgrade end"

# Execute single recipe in all nodes from file.
$ knife zero apply "name:*" -r @apply/maintenance.rb
```


## <a name="diagnose">[zero diagnose](#diagnose)</a>

It will roughly output settings that are generated from knife.rb. There is no option.

For check setting or bug reports.

```
$ knife zero diagnose 
Chef::Config
====================
---
:local_mode: true
:knife_zero: {}
:verbosity: 
:log_location: !ruby/object:IO {}
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"
:color: true
:log_level: :warn
:chef_repo_path: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground"
:chef_server_url: chefzero://localhost:8889
:repo_mode: everything

Knife::Config
====================
---
:verbosity: 0
:color: true
:editor: vim
:disable_editing: false
:format: summary
:remote_chef_zero_port: 
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"

Zero Bootstrap Config
====================
---
:encrypt: false
:ssh_user: root
:run_list: []
:first_boot_attributes: {}
:host_key_verify: true
:remote_chef_zero_port: 
:bootstrap_converge: true

Zero Converge Config
====================
---
:concurrency: 
:manual: false
:ssh_password_ng: false
:host_key_verify: true
:remote_chef_zero_port: 
:use_sudo: true
:override_runlist: 
:config_file: "/Users/sawanoboriyu/github/higanworks/knife-zero_playground/knife.rb"
```

