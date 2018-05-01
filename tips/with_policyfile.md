---
layout: tips
lang: en
title: Using Policyfile
permalink: tips/with_policyfile/
---

Knife-Zero supports Policyfile.

- [Policyfile](https://docs.chef.io/config_rb_policyfile.html)

## Requirements

- chef-dk(chef command) v0.11.0 or later required. to install it as below.
    - install knife-zero under chef-dk environment.
    - include chef-dk to your Gemfile.

## Strategy

If you are using a Policyfile at Knife-Zero, select the usage from the following two.

- Use local policy which exported by `chef export`.
    - It's simple.
    - One Policy that can be used simultaneously.
    - policy_group is fixed as `local`.
- Use the same specifications as the Chef-Server with combination of `knife serve` and` chef push`.
    - Operation is a little cumbersome.
    - Supports multiple-policies and versioning.


## Case: Use `chef export`.

Add the following to `knife.rb`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
```

### Workflow

First, reflect the contents of the Policyfile to Chef-Repo.

> Notice: Executing `chef export ./ -f` empties the`. / Chef` directory and overwrites it.
> recommended to put `knife.rb` and other configs to the project root directory.

1. Run `chef install` or `chef update` to create `Policyfile.lock.json`.
2. Run `chef export ./ -f` to update following directories in your Chef-Repo.
    - `cookbooks/`
    - `data_bags/policyfiles/`
    - `policies`
    - `policy_groups`

#### Bootstrap

Use `--policy-name` with `zero bootstrap`.

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME -N NODE_NAME
```

For example, It'll add the following to `client.rb` when passed `--policy-name build`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name build
policy_group local
```

#### Converge

There is no change to the `zero converge` normaly.

```shell
$ knife zero converge "QUERY"
```

You can `-n/--named-run-list` to use `named runlist`.

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```


### Tighter Integration

If you want to every time surely apply the update of Policyfile, you can append the following to the `knife.rb`.

```ruby
knife[:before_bootstrap] = 'chef update && chef export ./ -f'
knife[:before_converge]  = 'chef update && chef export ./ -f'
```

## Case: Use combination of `knife serve` and` chef push`

Add the following to `knife.rb`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
chef_server_url "http://localhost:8889"  # for `chef push`
```

### Workflow

First, launch a local Chef-Zero. It is necessary only when you manage policies by chef command, such as the `chef push` or `chef show-policy` or others.

```shell
$ knife serve
Serving files from:
repository at /Users/sawanoboriyu/worktemp/knife-zero_policy
  Multiple versions per cookbook

>> Starting Chef Zero (v4.5.0)...
...
```

Open another terminal, then run `chef push` with specific group name.

```shell
$ chef push -c knife.rb mygroup policies/mypolicy.rb
```

After push, you should terminate `knife serve`.

#### Bootstrap

Use `--policy-name` and `--policy-group` with `zero bootstrap`.

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME --policy-group POLICY_GROUP_NAME -N NODE_NAME
```

For example, It'll add the following to `client.rb` when passed `--policy-name mypolicy` and `--policy-group mygroup`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name mypolicy
policy_group mygroup
```

#### Converge

There is no change to the `zero converge` normaly.

```shell
$ knife zero converge "QUERY"
```

You can `-n/--named-run-list` to use `named runlist`.

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```

