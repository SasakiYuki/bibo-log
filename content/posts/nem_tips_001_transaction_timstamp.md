---
title: "トランザクションアナウンスにはNISのタイムスタンプを使う必要がある"
description: "NEMのドキュメントには書いてないそれ。内容はトランザクションアナウンスにはNISのタイムスタンプを使う必要があるという話。"
author:
  name: "Yuki Yamada"
date: 2018-12-17
draft: false	
categories:
- blogging
tags:
- NEM
- Android
---

# トランザクションアナウンスにはNISのタイムスタンプを使う必要がある 
タイトルの通りですが、トランザクションを作る際のタイムスタンプはローカルタイムではなくNISから持ってくる必要があります。

`「は～～？そんなことドキュメントに書いてないぞ～～？」`
と思った方　正解です。書いてません。

# 経緯

書こうと思った経緯ですが、某日　NEMFourm にて

とある人物がテストネットのHTTPS対応ノードを立てた旨をフォーラムに投稿しました。
すると一つのコメントが書き込まれました。
「なんで『nis.useNetworkTime = false』してんの？」と。要するにNetworktaimeを使わないよう設定していることを指摘しています。
これはミスではなくテストネットを立てた人にはある考えがあっての行動でした。
その考えというのが以下です。

「nem-libraryをはじめとする有名NEMLibraryはマシーンローカルタイムを使っているため」
とのことでした。確かに主要なライブラリはローカルタイムを採用しているものが多く、テストネットということもありこれは非常に理にかなった提案であると私も思いました。
また、テストネットを立てた人は「本来であればライブラリ側が対応するべきものですので一時的なdisableであり、こういった状況が改善されればこうする必要もなくなる」と仰っております。

# アクションプラン
さて、これを読んだエンジニアは何をすべきかは明白かと思います。ライブラリにPR送る？自プロダクトではTimeStampをしっかりセットする？どちらも正しそう。
が、そもそもNetworktimeはどのように取得すればいいでしょう。先ほども言ったようにこの仕様は特にNISのドキュメントには書かれていません。

APIはこれです

```kotlin
(NODE_DOMAIN)/time-sync/network-time
```

# やってみる
まず上のAPIを叩くとレスポンス形式としては

```json
{
"sendTimeStamp": 117445044449,
"receiveTimeStamp": 117445044449
}
```

シンプルな二つのタイムスタンプだけが含まれています。
これのsendTimeStampを使用します。

これをトランザクションアナウンスAPIを叩くときに使いましょう！！

ちなみに拙作ラクーンウォレットでもローカルタイムを渡しているので対応する必要があります。

前提として使用させてもらっている nem-kotlin ではtimestampを渡すとそれが使われますが、渡さない場合ローカルタイムを自動的にセットしてくれています。[該当コード](https://github.com/ryuta46/nem-kotlin/blob/19640f0f5ab7cd4424dc02c1cae3669cc0efe25f/src/main/kotlin/com/ryuta46/nemkotlin/transaction/TransactionHelper.kt#L102)

ので、timestampを渡すよう修正します。ラクーン側のコードは汚いんでホントは見てほしくないんですが[ここ](https://github.com/SasakiYuki/RaccoonWallet/blob/1fae2df69fb662c92417bcade841efda736e7e1b/app/src/main/java/wacode/yamada/yuki/nempaymentapp/utils/NemCommons.kt)です。


# おまけ
このAPIに限りませんがNEMのタイムスタンプはちょっと特殊です。
例えばこれをそのままDateに変換すると全然違う日付になります。
ではどうするか。
NEMのジェネシスブロック時間を足す必要があります。

ちなみにジェネシスブロックの生成日は
```kotlin
    cal.set(2015, 3 - 1, 29, 0, 6, 25)
```

です。これを足してDateを正しい日付にします。

```kotlin
val date = Date(nodeList.sendTimeStamp + nemStartTimeLong)
```

これで表示等には困らないでしょう！よかったよかった

皆さん参考までに～

