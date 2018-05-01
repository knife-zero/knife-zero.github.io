---
layout: tips-ja
lang: ja
title: Policyfileをつかう
permalink: tips/with_policyfile-ja/
---

Knife-ZeroはPolicyfileをサポートしています。

- [Policyfile](https://docs.chef.io/config_rb_policyfile.html)

## Requirements

- chef-dk(chefコマンド)のv0.11以降が必要です。次のいずれかで導入します。
    - chef-dkの環境にknife-zeroを追加する。
    - bundlerなどでchef-dkを含める。

## Strategy

Policyfileを使用する場合、次の2通りから利用法を選びます。

- `chef export`を使用して、ローカルモード用ポリシーを出力する。
    - 操作がシンプル。
    - 同時に使用できるポリシーは1つ。
    - policy_groupが`local`に固定されます。
- `knife serve`と`chef push`を併用し、Chef-Serverと同じ仕様で使う。
    - 操作はやや面倒。
    - 複数ポリシー、複数バージョンに対応。

## `chef export`を利用する場合。

### Setup

ワークステーションの`knife.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
```

### Workflow

まずPolicyfileの内容をChef-Repoに反映します。

> 注意: `chef export ./ -f` を実行すると `./chef`ディレクトリを空にして上書きします。
> `knife.rb`などの設定ファイルはプロジェクトのルートディレクトリを使用することをおすすめします。

1. `chef install` または `chef update`を実行し、`Policyfile.lock.json`を作成します。
2. `chef export ./ -f`を実行し、以下のディレクトリを更新します。
    - `.chef/`
    - `cookbook_artifacts/`
    - `policies/`
    - `policy_groups/`

#### Bootstrap

`zero bootstrap`では`--policy-name`を使用します。

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME -N NODE_NAME
```

例えば、`--policy-name build`としてBootstrapすると`client.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name build
policy_group local
```

#### Converge

`zero converge`は通常どおりです。

```shell
$ knife zero converge "QUERY"
```

また、`-n/--named-run-list`で`named runlist`を指定します。

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```


### Tighter Integration

Policyfileの更新を毎回確実に適用する場合は、`knife.rb`に次の内容を追記します。

```ruby
knife[:before_bootstrap] = 'chef update && chef export ./ -f'
knife[:before_converge]  = 'chef update && chef export ./ -f'
```

## `knife serve`と`chef push`を利用する場合。

### Setup

ワークステーションの`knife.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
chef_server_url "http://localhost:8889"  # chef push用
```

### Workflow

まずローカルのChef-Zeroを起動します。`chef push`や`chef show-policy`などを実行する時のみ必要です。

```shell
$ knife serve
Serving files from:
repository at /Users/sawanoboriyu/worktemp/knife-zero_policy
  Multiple versions per cookbook

>> Starting Chef Zero (v4.5.0)...
...
```

別のターミナルから任意のgroup名を付与して`chef push`します。

```shell
$ chef push -c knife.rb mygroup policies/mypolicy.rb
```

pushが終わったら、`knife serve`を終了します。

#### Bootstrap

`zero bootstrap`では`--policy-name`、`--policy-group`を使用します。

```shell
$ knife zero bootstrap HOST_NAME --policy-name POLICY_NAME --policy-group POLICY_GROUP_NAME -N NODE_NAME
```

例えば、`--policy-name mypolicy, --policy-group mygroup` としてBootstrapすると`client.rb`に次の内容を追加します。

```ruby
use_policyfile true
versioned_cookbooks true
policy_document_native_api true
policy_name mypolicy
policy_group mygroup
```

#### Converge

`zero converge`は通常どおりです。

```shell
$ knife zero converge "QUERY"
```

また、`-n/--named-run-list`で`named runlist`を指定します。

```shell
$ knife zero converge "QUERY" -n NAMED_RUNLIST
```

