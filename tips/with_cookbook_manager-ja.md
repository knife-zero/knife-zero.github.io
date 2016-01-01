---
layout: tips-ja
lang: ja
title: Cookbookマネージャと一緒に
permalink: tips/with_cookbook_manager-ja/
---

この項目内では3rdパーティーおよび自家製のCookbookの両方をマネージャで管理する前提にしています。

## [Berkshelf](http://berkshelf.com)

`berks vendor`コマンドを割り込みに指定します。

```ruby
coobook_path [
  File.expand_path('../../berks-cookbooks', __FILE__)
]

knife[:before_bootstrap] = "berks vendor"
knife[:before_converge]  = "berks vendor"

## ex. under the bundler environment.
# knife[:before_bootstrap] = "bundle/chef exec berks vendor"
# knife[:before_converge]  = "bundle/chef exec berks vendor"
```

## [Batali](https://github.com/hw-labs/batali)

Berkshelfとほぼ一緒です。コマンドは`batali update`で読みかえます。

```ruby
knife[:before_bootstrap] = "batali update"
knife[:before_converge]  = "batali update"

## ex. under the bundler environment.
# knife[:before_bootstrap] = "bundle/chef exec batali vendor"
# knife[:before_converge]  = "bundle/chef exec batali vendor"
```

## [Librarian](https://github.com/applicationsonline/librarian-chef)

参考: [librarian-chef#knife-integration](https://github.com/applicationsonline/librarian-chef#knife-integration)

Gemfileに`librarian-chef`を追加してbundleします。

```
gem 'knife-zero'
gem 'librarian-chef'
```

`knife.rb`で`cookbook_path`を次のようにして下さい。

```ruby
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              "/path/to/chef-repo/site-cookbooks"
```

例えば、Chef−Repoのルートに`knife.rb`を置いている環境なら次のように記述します。

```ruby
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              File.expand_path("../site-cookbooks", __FILE__)
```

設定後、knife実行時に`Cheffile and Cheffile.lock are out of sync!`が出る場合、次のどちらかで解決しましょう。

- `librarian-chef install`
- `librarian-chef clean`


## Policyfile

WIP
