---
layout: page
lang: en
title: Installation
---

## Install with Chef-DK

Install via `chef gem` subcommand.

```
$ chef gem install knife-zero
```

## Install from Rubygems

Add this line to your application's Gemfile:

```ruby
gem 'knife-zero'
```

And then execute:

```
$ bundle
```

### Bundler Example

I usually bundle gems with `--binstubs` and `--path` option.

```
$ bundle install --binstubs --path vendor/bundle
```

When pass `--path` option to bundler, RubyGems are installed under one directory.

Previous command creates tree below.

```
$ tree . -L 5
.
├── Gemfile
├── Gemfile.lock
├── bin
│   ├── chef-apply
│   ├── chef-client
│   ├── chef-shell
│   ├── chef-solo
│   ├── chef-zero
│   ├── coderay
│   ├── erubis
│   ├── ffi-yajl-bench
│   ├── htmldiff
│   ├── knife
│   ├── ldiff
│   ├── ohai
│   ├── pry
│   ├── rackup
│   ├── rake
│   ├── rspec
│   └── serverspec-init
└── vendor
    └── bundle
        └── ruby
            └── 2.1.0
                ├── bin
                ├── build_info
                ├── cache
                ├── doc
                ├── extensions
                ├── gems
                └── specifications

12 directories, 19 files
```

When pass `--binstubs` option to bundler, Command Line Tools which included with RubyGems are installed under `./bin`(configurable).

```
$ ./bin/chef-client -v
Chef: 12.3.0


$ ./bin/knife zero 2>/dev/null 
Available zero subcommands: (for details, knife SUB-COMMAND --help)

** ZERO COMMANDS **
knife zero bootstrap FQDN (options)
knife zero chef_client QUERY (options) | It's same as converge
knife zero converge QUERY (options)
knife zero diagnose # show configuration from file
```


### gem command(I don't recommend it)

install it yourself as:

```
$ gem install knife-zero
```
