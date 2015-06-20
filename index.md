---
layout: default
title: Home
---

# Knife-Plugin Zero

- It doesn't have to transport cookbooks via scp,rsync or something.
- It can collect node object into local chef-repo.
- It supports all functions of chef(C/S).
- You have only to manage one chef-repo.


## Introduction

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
