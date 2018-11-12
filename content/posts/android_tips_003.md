---
title: "AndroidTips-003-リスト表示をしてみる"
description: "Android初心者向け記事。リスト表示をするための資料です。"
author:
  name: "Yuki Sasaki"
date: 2018-11-09
draft: false
categories:
- blogging
tags:
- Android
- AndroidTips
---

複数の似たようなデータを表示する場合にはリスト形式のViewを使うのが一般的です。
今回はそのリスト形式のViewをどのように扱うかをやっていきたいと思います。

# 流れ
- Listを表示する `Activity` を追加します。
- レイアウトにリストの元になるRecyclerViewを置きます
- RecyclerViewにリストを表示するための `Adapter` を用意する
- （リストに表示するデータを用意する）
- 用意したAdapterを`RecyclerView` にセットしてリストを表示する

この流れでいきます。

目指すリストは下の通りです。


シンプルな文字だけどリストです。

# Activityを追加
特に難しいことはしませんが一応。
`Empty Activity` を作り今回は名前は `SimpleStringListActivity` としました。

# レイアウトファイルにRecyclerViewを置く
Androidでリスト表示をするには `RecyclerVIew` を使います。

まずはレイアウトファイルに配置してみましょう。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

RecyclerViewはサポートライブラリーで追加されたViewです。

# RecyclerViewで表示するレイアウトを作る
リストとして表示する主な要素となるレイアウトを作成します。
今回はシンプルなリストを目指すのでTextViewのみ置かれたシンプルなレイアウトファイルを作成します。

```xml:view_simple_string
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/rootView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="10dp" />

</LinearLayout>
```

今作ったレイアウトがリスト形式でたくさん表示されることになります。


# Adapterを用意する
RecyclerViewでリスト形式に要素を表示するには `RecyclerView.Adapter<T>` を継承したAdapterを用意する必要ががります。

必要最低限なAdapterを書いてみたのでそれを元に何を実装する必要があるか確認してみましょう。

```kotlin:SimpleStringAdapter.kt
package wacode.yamada.yuki.nempaymentapp.view.adapter

import android.content.Context
import android.support.v7.widget.RecyclerView
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView

import wacode.yamada.yuki.nempaymentapp.R

class SimpleStringAdapter(private val context: Context, private val items: List<String>) : RecyclerView.Adapter<SimpleStringAdapter.ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val inflater = LayoutInflater.from(context)
        val view = inflater.inflate(R.layout.view_simple_string, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val text = items[position]
        holder.textView.text = text
    }

    override fun getItemCount(): Int {
        return items.size
    }

    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        var textView: TextView = itemView.findViewById(R.id.textView)
    }
}
```

## クラス宣言部
上でも言いましたが、RecyclerViewのAdapterは `RecyclerView.Adapter<T>` を継承して作られます。
この `T` ですが何が入るかというと `RecyclerView.ViewHolder` をを継承したクラスが入ります。
ViewHolderの説明は後程するとして、一旦継承クラスは RecyclerView.Adapter<ViewHolder>` と認識しておいてください。コンストラクタはRecyclerView.Adapterにはないので必要な値を渡しましょう。今回は簡易的に `Context` と 表示したいStringのリストを渡しました。それぞれインスタンス化時にメンバに代入しています。


## ViewHolder
その他メソッドを説明する前に `RecyclerView.ViewHolder` について説明しておきましょう。
ViewHolderはviewの状態を保持するサブクラスです。スクロールをした時に処理がもたつかないよう（主にfindViewByIdなど）にいい感じに破棄しないようにしてくれます。
ここにはそれぞれのViewの参照を書きます。

今回の例では

```kotlin
    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        var textView: TextView = itemView.findViewById(R.id.textView)
    }
```

ここになります。
`RecyclerView.ViewHolder` を継承した `ViewHolder` クラスで、メンバにTextViewの参照を持っています。


## onCerateViewHolder
onCreateViewHolderでは実際に表示したいViewに対して先ほど作ったレイアウトをViewHolderに実際に代入（というよりインスタンス化）している部分です。
ここで生成されたViewHolderはその後 `onBindViewHolder` に向かいます。
ここは行が作られる度に呼び出されます。（１行なら１回　１００行あれば１００回　表示したい行にViewHolderがなければ何度も呼び出されます)

今回の例では

```kotlin
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val inflater = LayoutInflater.from(context)
        val view = inflater.inflate(R.layout.view_simple_string, parent, false)
        return ViewHolder(view)
    }

```

この部分です。
先ほど作ったレイアウトファイル `view_simple_string` をインフレートしたものをViewHolderに渡しています。

## onBindViewHolder
ここで実際にListを表示するための描画処理を行います。
`onCreateViewHolde` で生成されたViewHolderが流れてきて、そのViewに対してデータを渡し期待したレイアウトを実現させる部分です。
ここは行が作られる度ではなく表示される度に呼び出されます。

今回の例では

```kotlin
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val text = items[position]
        holder.textView.text = text
    }
```

このアダプターをインスタンス化したときに渡されたStringリストであるItemsからデータを取り出して、それを表示したいTextViewに渡すことで表示しています。簡単でしょう？


# データを用意する
今回は特にローカルDBにあるデータを引っ張ってきたり、APIを叩いてリスト形式のデータを使ったりするわけじゃないので適当なList<String>データを作っちゃいましょう。

```kotlin:SimpleStringListActivity.kt
    private fun createDummyData():List<String> {
        val list = ArrayList<String>()
        list.add("りんご")
        list.add("ぶどう")
        list.add("さかな")
        return list
    }
```

# AdapterをRecyclerViewにセットする
ここまでに作ったAdapterやデータをついにRecyclerViewにセットして表示してみます。
といっても簡単です。

```kotlin:SimpleStringListActivity
        val adapter = SimpleStringAdapter(this,createDummyData())
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = adapter
```

Adapterをインスタンス化
LayoutManagerをRecyclerViewにセット
インスタンス化したアダプターをRecyclerViewにセット

それだけです。

# LayoutManager
先ほどは急にLayoutmanagerなるものを出しました。
Viewの配置を決めるを決めるものです。
代表的なのは二つで今回使った `LinearLayoutManger` と格子状に並べる `GridLayoutmanager` です。

おわりで～す

