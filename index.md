---
layout: default
title: Home
---

# Knife-Plugin Zero

Knife-Zero is plugin for [Chef](https://www.chef.io/).

- It doesn't have to transport cookbooks via scp,rsync or something.
    - uses HTTP like Chef-Server/Client, via ssh port forwarding.
- It can collect node object into local chef-repo.
- It supports all functions of chef(C/S).
    - such as serach, edit node, etc...
    - except enterprise addons.
- You have only to manage one chef-repo.
- Windows Server(Target) is not supported.


## Introduction

First, Knife-Zero is not replacement of [Knife-Solo](http://matschaffer.github.io/knife-solo/).
Knife-Zero adds the function which can do a target remotely to the local_mode which is a subset of the Chef-Server/Client environment.

It may be used before introduction of Chef-Server, or you may manage like Knife-Solo. Even if use with whitelist, you can manage Node objects in Git.

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
