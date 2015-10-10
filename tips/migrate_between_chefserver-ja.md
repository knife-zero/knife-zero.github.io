---
layout: tips-ja
lang: ja
title: Chef-ServerとKnife-Zeroの相互移行
permalink: tips/migrate_between_chefserver-ja/
---


## Knife-Zero から Chef-Server へ

1. `knife.rb`から`local_mode true`の記述を消します。
1. `nodes`を除いて、`environments`, `roles` と `data_bags` のオブジェクトをChef-Serverにアップロードします(`knife upload`)。
1. すべてのNodeにChef-Server用のBootstrapを実行します。
1. ランリストやNormal属性のattributeを復旧させるため、`nodes`のオブジェクトをアップロードします(`knife upload`)。


## Chef-Server から Knife-Zero へ

1. Stop Chef-Client daemon or delete from cron job.
1. Download `environments`, `roles` and `data_bags` objects from Chef-Server.
1. Collect or download all cookbooks by Berkshelf or Libraran.
1. set `local_mode true` and `chef_repo_path` at `knife.rb`.
1. run `knife zero bootstrap` with `--no-converge` option to update `client.rb` on remote node.
1. That's all. We could be used `zero converge` after migrate.

1. デーモン稼働のChef-Clientを停止、またはCronジョブから除外します。
1. `nodes`, `environments`, `roles` および `data_bags` オブジェクトをChef-Serverからダウンロードします(`knife download`)。
1. Berkshelf や Libraran を用いて、Cookbookをローカルに集めます。
1. `knife.rb`に `local_mode true` と`chef_repo_path` を追記します。
1. `knife zero bootstrap` を `--no-converge` オプション付きで実行し、リモートサーバの `client.rb` を更新します。
1. 以降は、各Nodeを `zero converge` で管理することができます。


> 参考:  
> Knife-ZeroはCookbookのバージョニングに対応していません。これはファイルベースでChef-Zeroを使う際の仕様に依存してます。 
> バージョン違いで困るようなCookbookは、移行前に少々がんばって差異を吸収しておかないとダメかも。
