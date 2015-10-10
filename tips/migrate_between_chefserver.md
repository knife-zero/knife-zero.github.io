---
layout: tips
lang: en
title: Migrate between Chef-Server to Knife-Zero.
permalink: tips/migrate_between_chefserver/
---


## Knife-Zero to Chef-Server

1. remove `local_mode true` from `knife.rb`.
1. Upload `environments`, `roles` and `data_bags` objects to Chef-Server except the nodes(`knife upload`).
1. Bootstrap all nodes to manage under Chef-Server.
1. Upload all node objects to Chef-Server(`knife upload`) to set run-list and normal attributes.

## Chef-Server to Knife-Zero

1. Stop Chef-Client daemon or delete from cron job.
1. Download `nodes`, `environments`, `roles` and `data_bags` objects from Chef-Server(`knife download`).
1. Collect or download all cookbooks by Berkshelf or Libraran.
1. set `local_mode true` and `chef_repo_path` at `knife.rb`.
1. run `knife zero bootstrap` with `--no-converge` option to update `client.rb` on remote node.
1. That's all. We could be used `zero converge` after migrate.

> Note:  
> Knife-Zero doesn't support cookbook versioning. This behavior is depends on file-based chef-zero.  
> Probably, we should migrate cookbooks into one version works for all nodes.
