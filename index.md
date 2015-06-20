---
layout: default
title: Home
---

# Knife-Plugin Zero

Knife-Zero is plugin of [Chef](https://www.chef.io/).

- It doesn't have to transport cookbooks via scp,rsync or something.
    - uses HTTP like Chef-Server/Client, via ssh port forwarding.
- It can collect node object into local chef-repo.
- It supports all functions of chef(C/S).
    - such as serach, edit node, etc...
- You have only to manage one chef-repo.


## Introduction

First, Knife-Zero is not replacement of [Knife-Solo](http://matschaffer.github.io/knife-solo/).

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
