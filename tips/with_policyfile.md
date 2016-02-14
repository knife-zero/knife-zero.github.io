---
layout: tips
lang: en
title: Using Policyfile
permalink: tips/with_policyfile/
---

Knife-Zero supports Policyfile from version 1.13.0.

- [Policyfile](https://docs.chef.io/config_rb_policyfile.html)

## Requirements

- chef-dk(chef command) required. to install it as below.
    - install knife-zero under chef-dk environment.
    - include chef-dk to your Gemfile.
- With Chef-DK 0.11.0 or later.
    - Need Knife-Zero 1.14.0 or later.
    - Supports Native API.
- Chef-DK 0.10.0以下の場合
    - Need Knife-Zero 1.13.x.
    - Not supports Native API.

## Restrictions

If you are using a Policyfile at Knife-Zero, it has the following limitations.

- One Policy that can be used simultaneously.
- policy_group is fixed as `local`.
- It can not be used with other Cookbook manager.


## Setup

Add the following to `knife.rb`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
```

## Workflow

First, reflect the contents of the Policyfile to Chef-Repo.

1. Run `chef install` or `chef update` to create `Policyfile.lock.json`.
2. Run `chef export ./ -f` to update following directories in your Chef-Repo.
    - `cookbooks/`
    - `data_bags/policyfiles/`
    - `policies`
    - `policy_groups`

### Bootstrap

Use `--policy-name` with `zero bootstrap`.

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME -N NODE_NAME
```

For example, It'll add the following to `client.rb` when passed `--policy-name build`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name "build"
policy_group "local"
```

When using Chef-DK is v0.10.0 and Knife-Zero is v1.13.x, below configurations are added to `client.rb`.

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
deployment_group "build-local"
```

### Converge

There is no change to the `zero converge` normaly.

```shell
$ knife zero converge "QUERY"
```

You can `-n/--named-run-list` to use `named runlist`.

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```


## Tighter Integration

If you want to every time surely apply the update of Policyfile, you can append the following to the `knife.rb`.

```ruby
knife[:before_bootstrap] = 'chef update && chef export ./ -f'
knife[:before_converge]  = 'chef update && chef export ./ -f'
```

