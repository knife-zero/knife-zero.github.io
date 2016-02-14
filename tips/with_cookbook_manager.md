---
layout: tips
lang: en
title: Cookbook Manager Integration
permalink: tips/with_cookbook_manager/
---

This usage assumes using cookbook manager to manage both 3rd parties cookbooks and self maintenance cookbooks.

## [Berkshelf](http://berkshelf.com)

Set `berks vendor` to hooks.

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

That is mostly the same as Berkshelf.  Use `batali install`.

```ruby
knife[:before_bootstrap] = "batali update"
knife[:before_converge]  = "batali update"

## ex. under the bundler environment.
# knife[:before_bootstrap] = "bundle/chef exec batali vendor"
# knife[:before_converge]  = "bundle/chef exec batali vendor"
```

## [Librarian](https://github.com/applicationsonline/librarian-chef)

Ref: [librarian-chef#knife-integration](https://github.com/applicationsonline/librarian-chef#knife-integration)

Add `librarian-chef` to Gemfile, and bundle it.

```ruby
gem 'knife-zero'
gem 'librarian-chef'
```

Stick the following in your knife.rb:

```ruby
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              "/path/to/chef-repo/site-cookbooks"
```

For example. `knife.rb` in your chef_repo root.

```ruby
require 'librarian/chef/integration/knife'
cookbook_path Librarian::Chef.install_path,
              File.expand_path("../site-cookbooks", __FILE__)
```

When you see `Cheffile and Cheffile.lock are out of sync!`, you should correct dependency below two ways.

- `librarian-chef install`
- `librarian-chef clean`


## Policyfile

[Here](/tips/with_policyfile/)
