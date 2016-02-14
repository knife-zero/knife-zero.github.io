---
layout: tips-ja
lang: ja
title: Policyfileをつかう
permalink: tips/with_policyfile-ja/
---

バージョン1.13.0からPolicyfileをサポートしています。

- [Policyfile](https://docs.chef.io/config_rb_policyfile.html)

## Requirements

- chef-dk(chefコマンド)が必要です。次のいずれかで導入します。
    - chef-dkの環境にknife-zeroを追加する。
    - bundlerなどでchef-dkを含める。
- Chef-DK 0.11.0以降の場合
    - Knife-Zero 1.14.0以降が必要。
    - ネイティブAPIをサポート。
- Chef-DK 0.10.0以下の場合
    - Knife-Zero 1.13.xが必要。
    - ネイティブAPIは非サポート。

## Restrictions

Knife-ZeroでPolicyfileを使用する場合、次の制限があります。

- 同時に使用できるポリシーは1つ。
- policy_groupが`local`に固定されます。
- 他のCookbookマネージャとの併用不可。
    - `cookbooks/`ディレクトリがPolicyfile専用になる。

## Setup

ワークステーションの`knife.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true

# chef-dkが0.10.0以下の場合のみ追加
policy_document_native_api false
```

## Workflow

まずPolicyfileの内容をChef-Repoに反映します。

1. `chef install` または `chef update`を実行し、`Policyfile.lock.json`を作成します。
2. `chef export ./ -f`を実行し、以下のディレクトリを更新します。
    - `cookbooks/`
    - `data_bags/policyfiles/`
    - `policies`
    - `policy_groups`

### Bootstrap

`zero bootstrap`では`--policy-name`を使用します。

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME -N NODE_NAME
```

例えば、`--policy-name build`としてBootstrapすると`client.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name "build"
policy_group "local"
```

Chef-DKがv0.10.0のためKnife-Zeroの v1.13.xを使う場合は、次の内容で追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
deployment_group "build-local"
```

### Converge

`zero converge`は通常どおりです。

```shell
$ knife zero converge "QUERY"
```

また、`-n/--named-run-list`で`named runlist`を指定します。

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```


## Tighter Integration

Policyfileの更新を毎回確実に適用する場合は、`knife.rb`に次の内容を追記します。

```ruby
knife[:before_bootstrap] = 'chef update && chef export ./ -f'
knife[:before_converge]  = 'chef update && chef export ./ -f'
```
