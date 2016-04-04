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

