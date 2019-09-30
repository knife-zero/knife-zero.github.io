---
layout: page
lang: en
title: Tips and Links
---

## FAQ

### Does Knife-zero works with Chef-Vault ?

Yes, you can.


### Is there `knife zero init` ?

Chef-DK have `chef generate`.


### I would like to remove the cache of remote node.

I think that we can remove by recipe.

### Can I debug a recipe with chef-shell on the remote node?

See [https://github.com/higanworks/knife-zero/issues/104](https://github.com/higanworks/knife-zero/issues/104)

### Private key error `ERROR: NotImplementedError: OpenSSH keys only supported if ED25519 is available`

You can extend the number of supported formats as an option in the SSH connection library, net-ssh, so please try it.
Since native extensions are included, both methods require build tools such as make and GCC.

For bundler, add the following to Gemfile.

```
gem 'rbnacl', '< 5.0', require: false
gem 'rbnacl-libsodium', require: false
gem 'bcrypt_pbkdf', '< 2.0', require: false
```

For Chef-DK environment, use `chef gem install`.

```
$ chef gem install rbnacl -v '< 5.0'
$ chef gem install rbnacl-libsodium
$ chef gem install bcrypt_pbkdf -v '< 2.0'
```

## Tips

{% assign pages_list = site.pages %}
{% for node in pages_list %}
  {% if node.title != null %}
    {% if node.layout == "tips" %}
      {% if node.lang == "en" %}
- [{{ node.title }}]({{site.baseurl}}{{ node.url }})
      {% endif %}
    {% endif %}
  {% endif %}
{% endfor %}

## [Links](#Links)

Blogs written by me.

- Nothing in English...
- [(Japanese)Knife-Zero released.](http://qiita.com/sawanoboly/items/218a7b03ddec6be45e34 "Chefのローカルモードだけでリモートサーバを運用してみようと、Knife-Zeroを作った。Nodeの構成情報もとれるよ。 - Qiita")
- [(Japanese)Knife-Zero tutorial.](http://qiita.com/sawanoboly/items/4f363909615d8a76e9e5 "Ruby - Chefのローカルモードチュートリアル + knife-zero + knife-sakura - Qiita")
- [(Japanese)Node Whitelist how to.](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456 "chef-zero - Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する - Qiita")
- [(Japanese)Knife-Zero on Vagrant](http://qiita.com/sawanoboly/items/ae3c96734c5cee72863c "Knife-ZeroとVagrant(単品VM) - Qiita")


