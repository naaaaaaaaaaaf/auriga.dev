---
title: "LinodeのNVMeストレージを試す"
date: 2022-03-03T11:41:00+09:00
draft: false
---
※この記事は以前別ブログに掲載していたものを加筆修正し、再度公開したものです。  


こんにちは Nafです。

今回は[Linode](https://www.linode.com/lp/refer/?r=33483601fd2884fed84b1ac598927371f66596a8)で提供されているブロックストレージ(NVMe SSD版)をベンチマークしてみました。  

このNVMe SSDは以前から一部のリージョンで使用可能だったのですが、最近東京リージョンで提供開始されました。  


(提供開始されたらメール通知するやつを申し込んだのにメールは来なかった…)  

# 準備
まずはテスト用のサーバーを立ち上げます。  
今回はLinode 2GBのプランでサーバーを作成しました。  

後に作成するブロックストレージと同じリージョンでサーバーを作らないと、ストレージがアタッチできないので注意しましょう。  
{{< figure src="/img/try-linode-nvme01.webp" >}}


その後、サーバーにアタッチする用のボリュームを作成します。  
現時点(2022年3月)ではNVMeストレージは全てのリージョンで利用可能とのことです。  
{{< figure src="/img/try-linode-nvme02.webp" >}}


NVMeで作成されたボリュームは専用の表示があるため、簡単に見分けが付きます。  
{{< figure src="/img/try-linode-nvme03.webp" >}}


ボリュームをアタッチしたら、マウントを行います。  
マウント手順は「Show Config」から詳しく見ることができます。便利  
{{< figure src="/img/try-linode-nvme04.webp" >}}


# いざベンチマーク  
さて、準備ができたので実際にどれくらいの性能なのか、ベンチマークを行います。  

今回、ベンチマークはPostgreSQLのベンチマークツールであるpgbenchを利用しました。(当初ブロックストレージをデーターベース用に考えていたため)  

まずはpgbenchのセットアップから始めます。  
確か、postgresql-contribのパッケージにpgbenchが入っていた記憶…  

```bash
[naf@localhost ~]$ sudo dnf install postgresql postgresql-server postgresql-contrib
```

pgbenchがインストールできたら早速ベンチマークを取ります。  
まずはtest_dbに対して初期化をします。  

```bash
[postgres@localhost ~]$ pgbench -i test_db
dropping old tables...
NOTICE:  table "pgbench_accounts" does not exist, skipping
NOTICE:  table "pgbench_branches" does not exist, skipping
NOTICE:  table "pgbench_history" does not exist, skipping
NOTICE:  table "pgbench_tellers" does not exist, skipping
creating tables...
generating data (client-side)...
100000 of 100000 tuples (100%) done (elapsed 0.14 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 0.41 s (drop tables 0.00 s, create tables 0.01 s, client-side generate 0.22 s, vacuum 0.10 s, primary keys 0.08 s)
```

初期化が完了したらベンチマークを実行します。  
今回は 「接続数10、1接続あたりのトランザクションは1000 」という条件で取りました。  

pgbenchはこのようなベンチマークだけではなく、実際に使うSQLもベンチマークできるようです。実際の見積りが出来ていいですね  

```bash
[postgres@localhost ~]$ pgbench -c 10 -t 1000 test_db
starting vacuum...end.
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 19.021 ms
tps = 525.746350 (including connections establishing)
tps = 525.914967 (excluding connections establishing)
```

結果、TPSが525前後ということになりました。  
めちゃくちゃざっくりとTPSが大きければ大きいほど性能が良いということで大丈夫です。  

それでは、NVMeストレージをマウントして、Postgresqlのデータフォルダも変更します。  

```bash
[root@localhost ~]# mkfs.ext4 "/dev/disk/by-id/scsi-0Linode_Volume_db-bench"
mke2fs 1.46.3 (27-Jul-2021)
Discarding device blocks: done                            
Creating filesystem with 13107200 4k blocks and 3276800 inodes
Filesystem UUID: 9428e668-d18d-49ef-a7c3-305127615e73
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done   

[root@localhost ~]# mkdir "/mnt/db-bench"
[root@localhost ~]# mount "/dev/disk/by-id/scsi-0Linode_Volume_db-bench" "/mnt/db-bench"
[root@localhost ~]# chown -R postgres:postgres /mnt/db-bench/pgsql/
[root@localhost ~]# chmod -R 700 /mnt/db-bench/pgsql/
```

データフォルダの移行はPG_DATAとかいじっていい感じにしてください。  

移行が完了したら、再度同じ条件でベンチマークを取ってみます。  

```bash
[postgres@localhost ~]$ pgbench -c 10 -t 1000 test_db
starting vacuum...end.
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 54.972 ms
tps = 181.911595 (including connections establishing)
tps = 181.955593 (excluding connections establishing)
```

!?!?!?!?!?????????!?!?!?!?!?!?!?!  

遅すぎワロタ  

TPSがブロックストレージのほうが4,5倍遅くなっちゃった。  

# 結論
NVMeって名前は速いと思わせているが、以外と大したことは無い速度だった。  

公式ドキュメントによると、概ねスタンダードモードで350MB/s、バーストモードで525MB/sの速度になるようです。(予想してたよりあんま速くないね…)  
{{< figure src="/img/try-linode-nvme05.webp" >}}

この速度だとサーバーのストレージの方が速いので、データベースなどディスクが重要なソフトはサーバーのストレージを使うことをお勧めします。  

バックアップデーターの保存とかには向いているかもしれないですね(値段も安いので…)  