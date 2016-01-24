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

## Restrictions

Knife-ZeroでPolicyfileを使用する場合、次の制限があります。

- 同時に使用できるポリシーは1つ。
- policy_groupが`local`に固定されます。
- 他のCookbookマネージャとの併用不可。
    - `cookbooks/`ディレクトリがPolicyfile専用になる。
- ネイティブAPIに非対応。


## Setup

ワークステーションの`knife.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
```

## Workflow

まずPolicyfileの内容をChef-Repoに反映します。

1. `chef install` または `chef update`を実行し、`Policyfile.lock.json`を作成します。
2. `chef export ./ -f`を実行し、以下のディレクトリを更新します。
    - `cookbooks/`
    - `data_bags/policyfiles/`

### Bootstrap

`zero bootstrap`では`--policy-name`を使用します。

```
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME -N NODE_NAME
```

例えば、`--policy-name build`としてBootstrapすると`client.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api false
deployment_group "build-local"
```

### Converge

`zero converge`は通常どおりです。

```
$ knife zero converge "QUERY"
```

また、`-n/--named-run-list`で`named runlist`を指定します。

```
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```


## Tighter Integration

Policyfileの更新を毎回確実に適用する場合は、`knife.rb`に次の内容を追記します。

```
knife[:before_bootstrap] = 'chef update && chef export ./ -f'
knife[:before_converge]  = 'chef update && chef export ./ -f'
```
