---
layout: tips-ja
lang: ja
title: EnvironmentsやRolesにRuby DSLを使いたい
permalink: tips/use_ruby_dsl_for_envs_or_roles-ja/
---

Chef-Zeroがサポートしていないので、そのままではEnvironmentsやRolesにRuby DSLはつかえません。

もしRuby DSLをつかうなら、`knife.rb`にて都度JSONに変換しちゃいましょう。

```
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

JSONファイルは`.gitignore`に追加しておきましょう。

```
/roles/*.json
```
