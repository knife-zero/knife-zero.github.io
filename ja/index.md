---
layout: default-ja
title: Home(ja)
pamalink: ja/index.html
---

# Knife-Plugin Zero

Knife-Zeroは[Chef](https://www.chef.io/)のプラグインです。特徴はだいたい次のとおり。

- SSHポートフォワードでChef-Zeroに接続して、必要なCookbookだけ転送します。
- Nodeオブジェクトをローカル保存します。
- Chef-Server/Client環境の機能をつかえます。
    - ただしenterprise addonは除きます。

## Introduction

まずKnife-Zeroは[Knife-Solo](http://matschaffer.github.io/knife-solo/)の乗り換え先ではありません。
Knife-ZeroはChef-Server/Client環境のサブセットであるローカルモードに、対象をリモートにできる機能を追加します。

Chef-Serverの導入前につかってもよいし、Knife-Soloのように管理してもよし。Whitelistをうまくつって、NodeオブジェクトをGitで管理してもOKです。

とりあえず日本語情報は[リンク集]({{ site.baseurl }}/50_tips_index-ja/#Links)を。

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
