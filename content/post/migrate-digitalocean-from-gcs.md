---
title: "GCSからDigitalOceanのSpacesに移行する"
date: 2019-06-25T11:30:48+09:00
draft: false
---

※この記事は以前別ブログに掲載していたものを加筆修正し、再度公開したものです。  

どうもnafです。今回はGoogle Cloud Storageから[DigitalOcean](https://m.do.co/c/d34da7003443)のS3互換ストレージ「Spaces」に移行してみたので、手順などを書きます。  

# DigitalOceanって何？
{{< figure src="/img/digitalocean01.webp" >}}

DigitalOcean のホームページ
クラウド事業者が提供しているサービスの名称です。  
現在、日本語には対応していませんが、普通に登録ができます。また、日本リージョンも提供していないのですが、シンガポールリージョンがあるため日本からのアクセスはそこまで悪くなりません。  

競合のサービスとしては、[Linode](https://www.linode.com/lp/refer/?r=33483601fd2884fed84b1ac598927371f66596a8)やVultrがよく挙げられますね。  

# なぜDigitalOceanにするのか
DigitalOceanでは先に挙げたようなLinode,Vultrでは提供していないS3互換ストレージサービス(Spaces)を提供しています。
このサービスはディスク使用量が250GBまで5ドルの定額制になっています。また、転送量も1TBまで無料なので純粋にコストパフォーマンスが高いです。(超過した場合は1GB/0.01$)  

また、無料で提供しているCDNサービスのノードが東京にもあるようなので、レイテンシの問題もありません。そのため、DigitalOceanのSpacesを利用することにしました。  

# GCSからメディアファイルを移行する
私はGCSからSpacesへ移行する際に2つのツールを試してみました。順に紹介します。  

1. Minio client
Minioというオープンソースのストレージ用のクライアントです。全然情報が無くて困っていたのですが、どうやらS3互換のストレージサービスを複数登録してメディアの移動が出来るみたいなので使ってみました。

```bash
mc config host add gcs https://storage.googleapis.com  <YOUR-ACCESS-KEY> <YOUR-SECRET-KEY> #gcsを登録
mc config host add do https://<BUCKET>.sgp1.digitaloceanspaces.com <YOUR-ACCESS-KEY> <YOUR-SECRET-KEY> #DigitalOceanを登録
mc mirror gcs do #mirrorコマンドで同期
```

これで同期が開始されます。同期はかなり速く、大容量のバケットでもすぐ終わるのですが、どうやらGCS側で公開設定にしていたファイルがDigitalOcean側で非公開になってしまうようです。(全然使えねーじゃん)  

2. rclone
そこでクラウドストレージ間の同期に特化しているお馴染のrcloneを使うことにしました。  

rcloneの設定はめっちゃ優しい対話式なので割愛します。  

```
rclone sync gcs do -P
```
これで同期が開始されます。Minio clientと違い詳細にログを出すことが可能です。ですが、rcloneはかなり真面目に同期をするので結構時間がかかります。大容量のバケットを同期する時は注意しましょう。  

これでGCSからDigitalOceanのSpacesへの移行が完了しました。作業自体はコマンドを何個か打つだけなので簡単です。SpacesはGCSやS3などから移行したいと考えている人にはおすすめのサービスかと思います。この機会に移行してみてはいかがでしょうか。  

最後に無料クレジットが貰える紹介リンクを貼っておきます。  

[DigitalOceanに登録](https://m.do.co/c/d34da7003443)  

※GCSやS3はファイルの操作が課金対象になるため、調子に乗って何回も同期するとクラウド破産する可能性があります。計画的に使用してください。  
{{< figure src="/img/digitalocean02.webp" >}}
