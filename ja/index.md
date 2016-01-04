---
layout: default-ja
title: Home(ja)
permalink: ja/index.html
---

# Knife-Plugin Zero

Knife-Zeroは[Chef](https://www.chef.io/)のプラグインです。特徴はだいたい次のとおり。

- SSHポートフォワードでChef-Zeroに接続して、必要なCookbookだけ転送します。
- Nodeオブジェクトをローカル保存します。
- Chef-Server/Client環境の機能をつかえます。
    - ただしenterprise addonは除きます。
- Windows Serverは管理対象にできません。

## はじめに

まずKnife-Zeroは[Knife-Solo](http://matschaffer.github.io/knife-solo/)の乗り換え先ではありません。
Knife-ZeroはChef-Server/Client環境のサブセットであるローカルモードに、対象をリモートにできる機能を追加します。

Chef-Serverの導入前につかってもよいし、Knife-Soloのように管理してもよし。Whitelistをうまくつって、NodeオブジェクトをGitで管理してもOKです。

とりあえず日本語情報は[リンク集]({{ site.baseurl }}/50_tips_index-ja/#Links)を。

> あと、Chef-ZeroとKnife-Zeroは別モンです。混同注意。
> 
> - Chef-Zero: Chef社の軽量Chef-Server
> 
> - Knife-Zero: ユーザが作ったChef-ZeroをSSH越しに使うプラグイン


## Knife-Zeroのワークフロー

Knife-Zeroの使い方はほとんどChef-Server/Client環境の管理と変わりません。たいていは公式のドキュメントの解説が参考になるはずです。

- [All about Chef ... — Chef Docs](http://docs.chef.io/ "All about Chef ... — Chef Docs")


プラグインとして追加した主要なコマンドは2つです。

リモートのNode(サーバ)にChefをインストールする`zero bootstrap`、これは`knife bootstrap`をベースに転送部分を拡張しています。

- [knife bootstrap — Chef Docs](https://docs.chef.io/knife_bootstrap.html "knife bootstrap — Chef Docs")

更新を適用する`zero converge`、これは`knife ssh`をベースにポートフォワードと`chef-client`コマンドの実行方法を指定しています。

`zero converge`で使用する`QUERY`はすでに管理対象であるNodeを検索するためのもので、直接指定とはすこし考え方が違います。こちらもChef公式のドキュメントを参照するのがよいでしょう。

- [knife ssh — Chef Docs](https://docs.chef.io/knife_ssh.html "knife ssh — Chef Docs")
- [knife search — Chef Docs](https://docs.chef.io/knife_search.html "knife search — Chef Docs")

基本的にはこの2つのコマンドでNodeを管理します。

また、`zero apply`はスポットで単一のレシピを実行するChef-ApplyをSSH経由で実行します。

- [chef-apply (executable) — Chef Docs](https://docs.chef.io/ctl_chef_apply.html "chef-apply (executable) — Chef Docs")


### Nodeオブジェクトも含めてバージョン管理

NodeオブジェクトもGit等で管理したければ、WhiteListを使うとよいでしょう。詳しくはTipsに書いておきます。


## どのツールをつかえばいいのか

管理対象にするNodeを調達するところからコードで管理したいならば、まずは[chef-provisioning](https://docs.chef.io/provisioning.html)を検討します。

`chef-provisioning`は調達、更新と廃棄を一元管理します(Chef-Repoの転送はKnife-Soloと似ています)。また、`chef-provisioning`の管理下でもNodeの更新にはKnife-Zeroの`zero converge`を併用できます。

既存、または他のツールでNodeを調達しているならば、Knife-Zeroは便利です。ローカルサーチが不要なら[Knife-Solo](http://matschaffer.github.io/knife-solo/)も選べます。

ChefはしんどいけどRubyがいいなら[Itamae](http://itamae.kitchen/)もいいですよ。

まあ、[Getting Started](/20_getting_started-ja/)でやってみましょうか。

{% comment %}
<div class="posts">
  {% for post in paginator.posts %}
  <div class="post">
    <h1 class="post-title">
      <a href="{{ post.url }}">
        {{ post.title }}
      </a>
    </h1>

    <span class="post-date">{{ post.date | date_to_string }}</span>

    {{ post.content }}
  </div>
  {% endfor %}
</div>
{% endcomment %}

{% comment %}
<div class="pagination">
  {% if paginator.next_page %}
    <a class="pagination-item older" href="/page{{paginator.next_page}}">Older</a>
  {% else %}
    <span class="pagination-item older">Older</span>
  {% endif %}
  {% if paginator.previous_page %}
    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="/">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="/page{{paginator.previous_page}}">Newer</a>
    {% endif %}
  {% else %}
    <span class="pagination-item newer">Newer</span>
  {% endif %}
</div>
{% endcomment %}
