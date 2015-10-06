---
layout: tips
lang: en
title: To include from other knife plugins
permalink: tips/include_from_other_knife_plugins/
---

If you want to integrate knife-zero on machine creation with cloud plugins, you can add zerobootstrap to deps like below.

```ruby
deps do
  require 'chef/knife/zerobootstrap'
  Chef::Knife::ZeroBootstrap.load_deps
  self.options = Chef::Knife::ZeroBootstrap.options.merge(self.options)
end
```

For example, [knife-digital_ocean](https://github.com/higanworks/knife-digital_ocean/blob/79_merge_zero_bootstrap_options/lib/chef/knife/digital_ocean_droplet_create.rb)
