---
layout: tips-ja
lang: ja
title: Cookbookマネージャと一緒に
permalink: tips/with_cookbook_manager-ja.html
---

## Librarian

参考: [librarian-chef#knife-integration](https://github.com/applicationsonline/librarian-chef#knife-integration)

Gemfileに`librarian-chef`を追加してbundleします。

```
gem 'knife-zero'
gem 'librarian-chef'
```

`knife.rb`で`cookbook_path`を次のようにして下さい。

```
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              "/path/to/chef-repo/site-cookbooks"
```

例えば、Chef−Repoのルートに`knife.rb`を置いている環境なら次のように記述します。

```
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              File.expand_path("../site-cookbooks", __FILE__)
```

設定後、knife実行時に`Cheffile and Cheffile.lock are out of sync!`が出る場合、次のどちらかで解決しましょう。

- `librarian-chef install`
- `librarian-chef clean`

## Berkshelf

WIP

## Policyfile

WIP
