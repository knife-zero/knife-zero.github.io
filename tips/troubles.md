---
layout: tips
lang: en
title: Troubles?
permalink: tips/troubles/
---

## I met `Chef::Exceptions::ContentLengthMismatch: Response body length` when bootstrap/converge.

The net-ssh 2.9.2 (and older) will immediately send a CHANNEL_CLOSE even though there is still pending data in the output buffer.  
Please update net-ssh to 2.9.4 or later. It's already fixed.

## I've seen warning `Doing old-style registration with ...` at bootstraping.

NP. Please check [Validatorless Bootstraps | Chef Blog](https://www.chef.io/blog/2015/04/16/validatorless-bootstraps/).

Knife-Zero always uses dummy validation_key. You can choose ignoring this message or using chef-vault.


## I saw `Field 'policy_name' invalid error` .

Probably, you have set the options that you do not use to configuration file (e.g. knife.rb). In most cases, it is the `validation_key`, it will been created by` knife configure`.

Please remove unused options from configuration by refering [https://knife-zero.github.io/40_configuration/](https://knife-zero.github.io/40_configuration/).

## Unintended run list is applied during Converge

There is probably a duplicate Node object.  
For example, if there is a Node object that matches the host name of the remote server (FQDN that can be obtained by `hostname -f`), such as` nodes / localhost.json`, it may take precedence.

In bootstrap, if you give specific name to manage with the `-N, --node-name NAME` option, the `node_name` attribute is written in `client.rb` on the remote server, so you can prevent it.

- [client.rb — Chef Docs](https://docs.chef.io/config_rb_client.html)

## Private key error `ERROR: NotImplementedError: OpenSSH keys only supported if ED25519 is available`

You can extend the number of supported formats as an option in the SSH connection library, net-ssh, so please try it.
Since native extensions are included, both methods require build tools such as make and GCC.

For bundler, add the following to Gemfile.

```
gem 'rbnacl', '< 5.0', require: false
gem 'rbnacl-libsodium', require: false
gem 'bcrypt_pbkdf', '< 2.0', require: false
```

For Chef-DK environment, use `chef gem install`.

```
$ chef gem install rbnacl -v '< 5.0'
$ chef gem install rbnacl-libsodium
$ chef gem install bcrypt_pbkdf -v '< 2.0'
```
