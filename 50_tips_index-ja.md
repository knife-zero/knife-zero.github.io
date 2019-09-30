---
layout: page-ja
lang: ja
title: Tips and Links
---

## FAQ

### Chef-Vaultってつかえるんですか？

つかえますよ。

### `knife zero init` 無いの？

Chef-DKの`chef generate`でいいと思うよ。

### リモートノード上のキャッシュを消したい

レシピで消したら良いと思うよ。

### リモートノードでchef-shellを動かしてレシピをデバッグしたい

こちらを参照 [https://github.com/higanworks/knife-zero/issues/104](https://github.com/higanworks/knife-zero/issues/104)


### 秘密鍵のエラー `ERROR: NotImplementedError: OpenSSH keys only supported if ED25519 is available` が出ます

SSH接続用ライブラリ、net-sshではオプションとして対応形式を増やすことができるので、それを試してみてください。  
native extensionsを含むので、いずれの方法もmakeやGCCなどのビルドツールが必要です。

bundlerならGemfileに下記を追加。

```
gem 'rbnacl', '< 5.0', require: false
gem 'rbnacl-libsodium', require: false
gem 'bcrypt_pbkdf', '< 2.0', require: false
```

Chef-DK環境なら`chef gem install`で。

```
$ chef gem install rbnacl -v '< 5.0'
$ chef gem install rbnacl-libsodium
$ chef gem install bcrypt_pbkdf -v '< 2.0'
```


## Tips

{% assign pages_list = site.pages %}
{% for node in pages_list %}
  {% if node.title != null %}
    {% if node.layout == "tips-ja" %}
      {% if node.lang == "ja" %}
- [{{ node.title }}]({{site.baseurl}}{{ node.url }})
      {% endif %}
    {% endif %}
  {% endif %}
{% endfor %}

## [Links](#Links)

自分で書いたKnife-Zeroの話。

- [Chefのローカルモードだけでリモートサーバを運用してみようと、Knife-Zeroを作った。Nodeの構成情報もとれるよ。 - Qiita](http://qiita.com/sawanoboly/items/218a7b03ddec6be45e34 "Chefのローカルモードだけでリモートサーバを運用してみようと、Knife-Zeroを作った。Nodeの構成情報もとれるよ。 - Qiita")
- [Ruby - Chefのローカルモードチュートリアル + knife-zero + knife-sakura - Qiita](http://qiita.com/sawanoboly/items/4f363909615d8a76e9e5 "Ruby - Chefのローカルモードチュートリアル + knife-zero + knife-sakura - Qiita")
- [chef-zero - Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する - Qiita](http://qiita.com/sawanoboly/items/28dfc22929b8fa961456 "chef-zero - Knife-Zeroで管理するnodeオブジェクトを任意のattributesに限定する - Qiita")
- [Knife-ZeroとVagrant(単品VM) - Qiita](http://qiita.com/sawanoboly/items/ae3c96734c5cee72863c "Knife-ZeroとVagrant(単品VM) - Qiita")


ほか

- [knife (zero) bootstrapで入れるChef Clientのバージョンを変える(test-kitchenも) - Qiita](http://qiita.com/Marcy/items/d3e5528a65280dc86f07 "knife (zero) bootstrapで入れるChef Clientのバージョンを変える(test-kitchenも) - Qiita")
