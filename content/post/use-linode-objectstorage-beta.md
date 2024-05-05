---
title: "Linodeのオブジェクトストレージ(Beta版)を試す"
date: "2019-06-27"
draft: false
---

{{< figure src="/img/d96844fdb2891265-1024x576.jpg" >}}

LinodeよりS3互換のオブジェクトストレージの先行利用権を貰ったため試してみました。

※事前にオブジェクトストレージについてのアンケートに答えたため先行利用権が貰えたようです。現在はサポートチケットを切ると貰えるようです([参考](https://www.linode.com/community/questions/18372/announcement-linode-object-storage-early-adopter-program))

## Cloud Managerから使ってみる

Cloud ManagerにアクセスするとObject Storageが追加されているのがわかります。

{{< figure src="/img/SnapCrab_NoName_2019-6-27_9-5-18_No-00-1024x499.png" >}}

ここから新たなバケットを作ることができます。

{{< figure src="/img/SnapCrab_NoName_2019-6-27_9-5-25_No-00.png" >}}

現時点ではリージョンはニュージャージーのみ提供のようです。(おそらく東京リージョンも追加されると思われます。)

{{< figure src="/img/SnapCrab_NoName_2019-6-27_9-9-23_No-00-1024x499.png" >}}

バケットを作成しました。ですが、今はCloud Managerからファイルをアップロードしたり権限の編集をすることは出来ないようです。そのため他のツールで操作してみます。

## Cyberduckでファイルのアップロードをする

s3互換のストレージなのでs3cmd等のコマンドラインツールも使えるのですが面倒なのでGUIで操作できる「Cyberduck」を使ってみます。

{{< figure src="/img/SnapCrab_NoName_2019-6-27_10-1-27_No-00-1024x717.png" >}}

Cyberduckをインストール後、Cloud Managerで発行したアクセスキーとシークレット、エンドポイント(us-east-1.linodeobjects.com)を入力します。

{{< figure src="/img/SnapCrab_NoName_2019-6-27_9-11-8_No-00.png" >}}

これで接続が完了します。

アップロード後、権限を修正する場合はファイルを選択し情報->アクセス権より編集が可能です。

アップロードした画像: [https://mstdn.us-east-1.linodeobjects.com/28683404.png](https://mstdn.us-east-1.linodeobjects.com/28683404.png)

## 現時点で出来ないこと

重複しますがまとめておきます。

- Cloud Managerからのファイル操作
- 他のリージョンの利用(現在はニュージャージーのみ)
- カスタムドメイン、証明書の利用

現在はBeta版での提供なのでおそらくこれらのことは出来るようになると思います。

## 料金について

料金はストレージ利用料が250GBにつき5ドル(超過分は1GBあたり0.02ドル)、転送量が1GBあたり0.02ドルとなっています。

しかし、Linodeで既にインスタンスを持っている場合は、そちらの転送プールが優先的に利用されるようです。

{{< figure src="/img/SnapCrab_NoName_2019-6-27_10-18-16_No-00.png" >}}

そのため、Linodeで沢山インスタンスを持っているユーザーは転送量のことは気にせず利用できそうです。

料金体系はこの転送量課金以外はDigitalOceanのSpacesとほぼ同じです。このBeta版の機会に試してみては如何でしょうか。

[Linodeに登録](https://www.linode.com/?r=33483601fd2884fed84b1ac598927371f66596a8)

{{< figure src="/img/f5ce4ab66ecd3b8d-1024x576.jpeg" >}}
