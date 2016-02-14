---
layout: tips
lang: en
title: Using Ruby DSL for Environments or Roles
permalink: tips/use_ruby_dsl_for_envs_or_roles/
---

Chef-Zero doesn't support Environments/Roles which written by Ruby DSL.


Although I do not usually write roles or others using ruby. But you can convert them by `knife.rb` at every knife run.

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

Please keep in mind that this usage becomes the target outside of `knife edit` other for normal workflow commands.

I recommend to add JSON files to `.gitignore` when you use this little hack.

```shell
/roles/*.json
```
