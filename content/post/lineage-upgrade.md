---
title: "LineageOS 16から17.1にアップデートした"
date: 2020-12-20T10:00:00+09:00
draft: false
---

{{< figure src="/img/PXL_20201217_060424201-1024x768.jpg" >}}

naaaaaaaaaaafです。

OnePlus5T君は、デフォルトでOxygenOSとかいうよく分からんOSが入っています。ググってみると[OxygenOSの良い点みたいな記事](https://www.gizmodo.jp/2019/07/7-ways-oneplus-and-oxygenos-beat-googles-stock-android-1836311751.html) もあるのですが、ピュアっぽいAndroidを使いたかったので、2年前にこのスマホを買った時から [Lineage OS](https://lineageos.org/)を導入しています。

Lineage OS のサイトを見ると分かるように、OnePlus5Tは公式のイメージが配布されており、手軽にRomを変えることができます。アップデートも端末の設定から行うことが出来き、とても便利です。

しかし、表題にある通りLineage OS 16 -> Lineage OS 17.1 のようなアップデートは設定から行えず、パソコンから行う形になります。

今回はそのアップデートの方法について、前(15から16)までと変わっていたため簡単にまとめました。

(なお、現在はZoomやmeet用のWebカメラとして役割を任されています)

## ドキュメントを読む

読め 

[アップデートについてのドキュメント](https://wiki.lineageos.org/devices/dumpling/upgrade)

{{< figure src="/img/image-1024x568.png" >}}


Firmwareを更新し、TWRPから「Advance -> ADB sideloadみたいなやつ」を選択し、パソコンから更新するイメージをadb sideloadするだけです。

Open GAPPSを入れている人は**起動する前に**、一緒にsideloadしましょう。

sideloadはいい感じに差分データーをインストールしてくれるため、特にリセットをする必要もなく、アップデートをインストールできます。便利ですね。

{{< figure src="/img/9126b486bd03fc9c-512x1024.png" >}}

よかったね
