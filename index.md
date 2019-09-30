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
    - such as search, edit node, etc...
    - except enterprise addons.
- You have only to manage one chef-repo.
- Windows Server(Target) is not supported.

## Development status of Knife-Zero

It will be able to handle the Policyfile by used with Chef-DK v0.11 at Knife-Zero v1.14.
Incidentally, Chef-Vault is also supported already.
The ability to provide of the Chef-Core is almost ready for use.

So, maintenance policy for this project is currently as follows.

- New Feature: Negative. No immediate plans.
    - But it will be accepted at the request any time.
- Compatibility: It will be kept supporting of the latest version of the Chef-Client.

The version correspondence of Knife-Zero and Chef Infra Client (Chef-Client) is below.

- Knife-Zero 2.x or later: Chef Infra Client >= 15.x
- Knife-Zero 1.x (end of development): Chef Infra Client(Chef-Client) < 15.x

Besides, I think that it supports while cinc-client is just another name.  => [Use cinc-client instead?](/tips/use_cinc-client_instead/)

## Introduction

First, Knife-Zero is not replacement of [Knife-Solo](http://matschaffer.github.io/knife-solo/). The replacement from Knife-Solo is preferably [Chef-Run](https://www.chef.sh/docs/chef-workstation/chef-run-users-guide/), which is included in Chef WorkStation.

Knife-Zero adds the function which can do a target remotely to the local_mode which is a subset of the Chef-Server/Client environment.

It may be used before introduction of Chef-Server, or you may manage like Knife-Solo. Even if use with whitelist, you can manage Node objects in Git.


## Work with Knife-Zero

The how to use Knife-Zero is hardly different from management of the Chef-Server/Client environment. Explanation of an official document should almost be helpful.

- [All about Chef ... — Chef Docs](http://docs.chef.io/ "All about Chef ... — Chef Docs")

There are 2 main commands this plug-in offers.

`zero bootstrap` installs chef-client to remote Node. This subcommand based on `knife bootstrap`.

- [knife bootstrap — Chef Docs](https://docs.chef.io/knife_bootstrap.html "knife bootstrap — Chef Docs")

`zero converge` updates Nodes which already managed. This subcommand based on `knife ssh`.
The `QUERY` used for choice of a node uses the search function of Chef. Please see about `knife search`.


- [knife ssh — Chef Docs](https://docs.chef.io/knife_ssh.html "knife ssh — Chef Docs")
- [knife search — Chef Docs](https://docs.chef.io/knife_search.html "knife search — Chef Docs")

Management of Node uses these 2 commands basically.

In addition, `zero apply` run the Chef-Apply to run a single recipe via SSH.

- [chef-apply (executable) — Chef Docs](https://docs.chef.io/ctl_chef_apply.html "chef-apply (executable) — Chef Docs")


### You can include Node Object to version control system.

When I'd like also to manage a Node object for Git, I'd hope that you use WhiteList. It's written in Tips for more information.

When you'd like also to manage a Node object by VCS(such as Git), use WhiteList. See Tips.


## Which tool should I use?

If you just want to apply the recipe to the remote node, you can choose `chef-run`. It is included in the official tool set [Chef Workstation](https://www.chef.sh/docs/chef-workstation/getting-started/).

- [chef-run Guide](https://www.chef.sh/docs/chef-workstation/chef-run-users-guide/)

When it's existence or Node is provisioned by other tools, Knife-Zero is convenient. If local search is unnecessary, [Knife-Solo](http://matschaffer.github.io/knife-solo/) can also be chosen.

If you seem to hard about learning chef but love Ruby, You can consider the [Itamae](http://itamae.kitchen/).

Well, you can try Knife-Zero [Getting Started](/20_getting_started/).


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
