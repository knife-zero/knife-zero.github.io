---
layout: tips-ja
lang: ja
title: EnvironmentsやRolesにRuby DSLを使いたい
permalink: tips/use_ruby_dsl_for_envs_or_roles-ja/
---

Chef-Zeroがサポートしていないので、そのままではEnvironmentsやRolesにRuby DSLはつかえません。

私はあまりRoleなどにRubyを使わないですが、もしRuby DSLをつかいたいなら、`knife.rb`にて都度JSONに変換しちゃいましょう。

```ruby
Dir.glob("roles/*.rb").each do |rf|
  role = Chef::Role.new
  role.name(File.basename(rf, ".rb"))
  role.from_file(rf)
  File.open(rf.gsub(".rb", ".json"),"w") {|f| f.puts(role.to_json)}
end

local_mode true
chef_repo_path File.expand_path("../", __FILE__)

## -- snip --

```

ただし、`knife edit`ほか通常のワークフロー用コマンドの対象外になるので注意。


JSONファイルは`.gitignore`に追加しておきましょう。

```shell
/roles/*.json
```
