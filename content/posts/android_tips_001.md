---
title: "AndroidTips-001-押されたら何かをするを実装する"
description: "Android初心者向け記事。OnClickListenerの説明。"
author:
  name: "Yuki Yamada"
date: 2018-10-02
draft: false	
categories:
- Android
- androidtips
---

# サンプルコード

```kotlin:MainActivity
import android.os.Bundle
import android.support.v7.app.AppCompatActivity
import android.view.View
import android.widget.ImageView
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        setupButton()
    }

    private fun setupButton() {
        // 1
        val button = findViewById<ImageView>(R.id.redImageView)
        button.setOnClickListener(object : View.OnClickListener {
            override fun onClick(v: View?) {
                // DO IT !!
            }
        })
        
        // 2
        button.setOnClickListener {
            // DO IT !!
        }

        // 3
        redImageView.setOnClickListener {
            // DO IT !!
        }
    }
}

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/redImageView"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:src="@android:color/holo_red_dark" />

</android.support.constraint.ConstraintLayout>
```

# 解説
`setupButton` というメソッドを定義してその中で「押されたら...?」の処理をするための準備をしています。

## findViewById
`findViewByID` は `AppCompatActivity` を継承したことによって使うことができるメソッドです。
`setContentView` などによって設置された`View` の中から `id` を使って対象の `View` を探すことができるメソッドです。
使い方は簡単で、

```kotlin
        val button = findViewById<ImageView>(R.id.redImageView)
```

findViewById<型、今回はImageView>(リソースID) 

です。戻り値に取り出したViewが返ってきます。

## setOnClickListener
findViewByIdで取り出したViewに対して `setOnClickListener` というメソッドを呼び出しているのがわかると思います。
`OnClickListener` というのは `interface` と言われるものなのですが今は一旦説明を省きます。
とりあえずは中かっこのブロックで省略することができるので下のように使うと便利です。

## kotlinではさらにfindViewByIdも省ける
Kotlin Android Extensions という仕組みを使うことでリソースIDをそのままViewとして扱うことができます。
3の例がそれです。

対応した `import kotlinx.android.synthetic.main.activity_main.*` syntheticファイル（自動生成）をimportすることで使えるようになります。

