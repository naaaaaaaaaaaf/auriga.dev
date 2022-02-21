---
title: "Freenomのドメインをメインで使うのはやめろ"
date: 2022-02-21T15:43:16+09:00
draft: false
---

## なにが言いたい
Freenomの無料ドメイン(.ga .tk .cf .ml)は突然長時間の障害が起きる可能性があるのでメインで使うのはやめろ

## 経緯
こんにちは Naf です。  
みなさーん！Freenom、使ってますか〜？  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
# 地獄に落ちろ！！！！！！！！！！  
{{< figure src="/img/nuclear-2136244_1280-768x512.jpg" >}}
私はFreenomでauri.gaというドメインを取得して利用していたのですが、ある日(2022年2月11日ごろから)突然アクセス出来無くなりました。  
&nbsp;  
{{< mastodon ap.ketsuben.red naf 107779939480777414 >}}  
&nbsp;  
てっきり最初はネームサーバーの問題かと思い、ConoHaの障害情報を調べたりしていたのですが、情報が出て来ず、意味不明でした。([ConoHa](https://www.conoha.jp/referral/?token=SS73RfQ9OKe5mhSVVXzLo.Z2TAIYBimj2xoaIEd__rKQfJwjEeU-HT0)から[Linode](https://www.linode.com/?r=33483601fd2884fed84b1ac598927371f66596a8)にネームサーバーを変更したりしたが結果変わらず)  
{{< figure src="/img/2022-02-21-171409.jpg" >}}  

よくよく、digで名前解決をしてみると、一部のサーバーのみ名前解決ができなくなっていることに気が付きました。  
具体的に見ると、Google DNS (8.8.8.8)からは正常に引けずに、Cloudflare DNS (1.1.1.1)からは正常に引けるようになっていました。  
そこでGoogle DNSがおかしい？と思い、[Google DNSのキャッシュクリア](https://developers.google.com/speed/public-dns/cache)をサイトにアクセスして実行してみました…が、結局改善しませんでした。  

## そういえば  
昔、ioドメインで障害が起き、名前解決が出来無いという事案があったのを思い出し、それと同じような状況なのか調べてみました。  
[Qiitaで見つけた記事](https://qiita.com/MasahitoShinoda/items/40cd312fabc6db604b39)のように適当なドメインをdigしてみます。
```txt
$ dig example.ga +trace

; <<>> DiG 9.16.1-Ubuntu <<>> example.ga +trace
;; global options: +cmd
.			53	IN	NS	g.root-servers.net.
.			53	IN	NS	h.root-servers.net.
.			53	IN	NS	i.root-servers.net.
.			53	IN	NS	j.root-servers.net.
.			53	IN	NS	k.root-servers.net.
.			53	IN	NS	l.root-servers.net.
.			53	IN	NS	m.root-servers.net.
.			53	IN	NS	a.root-servers.net.
.			53	IN	NS	b.root-servers.net.
.			53	IN	NS	c.root-servers.net.
.			53	IN	NS	d.root-servers.net.
.			53	IN	NS	e.root-servers.net.
.			53	IN	NS	f.root-servers.net.
;; Received 262 bytes from 127.0.0.53#53(127.0.0.53) in 0 ms

ga.			172800	IN	NS	a.ns.ga.
ga.			172800	IN	NS	b.ns.ga.
ga.			172800	IN	NS	c.ns.ga.
ga.			172800	IN	NS	d.ns.ga.
ga.			86400	IN	NSEC	gal. NS RRSIG NSEC
ga.			86400	IN	RRSIG	NSEC 8 1 86400 20220224220000 20220211210000 9799 . IwNKC3CNRLuDyrT0wdFonFwfQL8Gl8yB2SHMys3O0cUTp9v1q6gFXB3m j25sIzPbI5mo3TqNcPeUT8Y6yGMI//OGIT07MJvkd5ipFcmn/jXu9tCJ BlY41AtHbA+pkHLTuMLJ7jvB9id9peT9BR0tuz+lxDDxG3g6UQaOBK+n enXqiySAvYXLRAzM6vfY0dpWGsR3p6UK4hgo4x3h2hQKC42mDMQH3xlX OpEVgJvIY/hF4LVygts9ZknHnaxVZJa64Gu0F5juIqyuKdN7AjWe+9Ii hnn+lFU7QxmNtj/NgI0Yw/5+7Z9AF/0LqVS7UMY3yPajSU1REGa+MAKm mZsM8g==
couldn't get address for 'a.ns.ga': failure
couldn't get address for 'b.ns.ga': failure
couldn't get address for 'c.ns.ga': failure
couldn't get address for 'd.ns.ga': failure
dig: couldn't get address for 'a.ns.ga': no more
```  
### ハイクソー  
見事にgaドメインの権威サーバーが死んでました。  
gaドメインの権威サーバーは
- a.ns.ga
- b.ns.ga
- c.ns.ga
- d.ns.ga   

の4つあり、これが全て死んでいる訳では無く、d.ns.gaが死んでいたぽいです。  
だからGoogle DNS以外は正常に名前解決できていたのか？(それはそれでどうなんだ)

## こういう時、どんな顔をすればいいかわからないの  
こういった事例に遭遇したことが無かったので、綾波レイになってしまいました。  
サーバーが壊れたらサーバー屋さんに問い合わせるけどドメインが壊れたら…？  
よく分からないし、メールもなんか普通に受信できていたのでしばらく放置してみました。  

## 数日後…  
直んねえ！！！！！！！！    
マジで直らんしモヤモヤするので連絡先がないか調べてみました。  
[ありました](http://www.iana.org/domains/root/db/ga.html)  
{{< figure src="https://s3-media.hostdon.ne.jp/s1/media_attachments/files/107/835/288/475/351/622/original/72c73fcf0385c0cf.png" >}}  
でもメールアドレスもgaドメインってことは、連絡出来無いってコト！？！？！？！？！？！？  
ガボンってフランス語が公用語なんだ  
結局連絡できそうにないのでちょっと放置しました。  

## ところで
gaドメインを取得したFreenomで、今障害が起きてるgaドメインって取れるのかなと思いちょっと試してみました。(Freenomのサイト、滅茶苦茶重い。  )  
結果から言うと、gaドメイン以外もドメインが取れなくなっていました。  
{{< figure src="https://s3-media.hostdon.ne.jp/s1/media_attachments/files/107/783/695/053/914/854/original/e392a1bfba1ccb50.png" >}}  
ちなみにFreenomの障害情報は「何も障害起きてないぜ！」って言ってました。ハア  
一応、Freenomにはサポートチケットを切っておき、「ドメイン取れねえけど」と投げておきました。  

## 復旧は突然に
2022年2月14日に復旧しました。  
{{< mastodon ap.ketsuben.red naf 107795968799366950 >}}  
大体2、3日正常に名前解決されなかったということになりました。  
ちなみにFreenomのチケットは無言でクローズされました。  
{{< figure src="https://s3-media.hostdon.ne.jp/s1/media_attachments/files/107/835/289/411/184/298/original/dd4f1ca43a0aa7dd.png" >}}  

## まとめ  
gaドメインやtkドメインといったドメインはccTLDという区分です。  
ccTLDは確かSLAは無かったので、今回の様に数日間名前解決ができなくなる自体がありえます。なのでメインで使うドメインはgTLDにしましょうということです。(gTLDはSLAあり)  
また、あくまでも無料ドメインなので過度なサポートや期待はやめて、落ちても良いサービスに使いましょう。  
なので、auriga.dev取りました。gTLD最高！
