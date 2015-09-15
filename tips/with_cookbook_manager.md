---
layout: tips
lang: en
title: Cookbook Manager Integration
permalink: tips/with_cookbook_manager/
---


## Librarian

Ref: [librarian-chef#knife-integration](https://github.com/applicationsonline/librarian-chef#knife-integration)

Add `librarian-chef` to Gemfile, and bundle it.

```
gem 'knife-zero'
gem 'librarian-chef'
```

Stick the following in your knife.rb:

```
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              "/path/to/chef-repo/site-cookbooks"
```

For example. `knife.rb` in your chef_repo root.

```
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              File.expand_path("../site-cookbooks", __FILE__)
```

When you see `Cheffile and Cheffile.lock are out of sync!`, you should correct dependency below two ways.

- `librarian-chef install`
- `librarian-chef clean`


## Berkshelf

WIP

## Policyfile

WIP
