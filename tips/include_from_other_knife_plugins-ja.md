---
layout: tips-ja
lang: ja
title: クラウド系knife pluginと連携するには
permalink: tips/include_from_other_knife_plugins-ja/
---

knife-zeroのBootstrapを他のクラウド操作系プラグインなどから連携したい場合は、次のように依存関係をもたせます。

```
deps do
  require 'chef/knife/zerobootstrap'
  Chef::Knife::ZeroBootstrap.load_deps
  self.options = Chef::Knife::ZeroBootstrap.options.merge(self.options)
end
```

実装にはknife-digital_oceanが参考になります。

- [knife-digital_ocean](https://github.com/higanworks/knife-digital_ocean/blob/79_merge_zero_bootstrap_options/lib/chef/knife/digital_ocean_droplet_create.rb)
