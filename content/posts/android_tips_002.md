---
title: "AndroidTips-002-画面移動してみる"
description: "Android初心者向け記事。Intentの説明"
author:
  name: "Yuki Yamada"
date: 2018-10-03
draft: false	
categories:
- blogging
tags:
- Android
- androidtips
---

# サンプルコード
```kotlin:MainActivity
import android.content.Context
import android.content.Intent
import android.os.Bundle
import android.support.v7.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        setupButton()
    }

    private fun setupButton() {
        redImageView.setOnClickListener {
            val intent = Intent(this@MainActivity, FormActivity::class.java)
            startActivity(intent)
        }
    }
}

```

# Intent
アプリの中での移動に限らず外への移動（例えば標準のWebブラウザに飛ばす）などに用いられる画面遷移用のコンポーネントです。
どこに移動するか　や　移動の際に渡すデータ　などを指定できます。
今回は単純に移動するためだけに使っているシンプルな使い方です。その際のIntentのコンストラクタは

```kotlin
Intent(context:Context, 移動するクラス:Class<?>)
```

です。今回の例では

```kotlin
            val intent = Intent(this@MainActivity, FormActivity::class.java)
```

でインスタンス化しています。
それでstartActivityをすることで画面移動をしています。

# もう少しスマートにIntentしてみる
上の方法で十分Intentは要件を満たしていますがもう少しスマートに書いてみましょう。

```kotlin:MainActivity
    private fun setupButton() {
        redImageView.setOnClickListener {
            startActivity(FormActivity.createIntent(this@MainActivity))
        }
    }
```

```kotlin:FormActivity
class FormActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_form)
    }

    companion object {
        fun createIntent(context: Context): Intent {
            return Intent(context, FormActivity::class.java)
        }
    }
}
```

移動したいActivity側にStaticなメソッドの `createIntent` というものを用意しました。
これは自分(FormActivity)に飛ぶようなIntentを返すメソッドです。これを用意することにより
#### どこから呼び出されているかメソッドが使われている部分を見れば一目でわかる
といった利点があります。

さらに、このcreateIntentもっと大きなメリットがあります。それは値を渡しつつIntentする際に真価を発揮します。

# 値を渡すには？
先ほどIntentの説明で移動の際に渡すデータを指定することができるといいました。
せっかくなのでやってみましょう。

```kotlin
            val intent = Intent(context, FormActivity::class.java)
            intent.putExtra("intent_name", name)
```

先ほどと同じようにIntentを作ったあとそのIntentに対して `putExtra` を呼んでいます。
このputExtraを使うことでIntentに値をセットすることができ引数は

```kotlin
intent.puExtra(キー:String,値(Int String Double...だいたいなんでも！)
```

です。サイズの制限はありますが、ごにょっとすれば大体なんでも送れます。なんでも送ればいいというわけでもないですが。

キーはStringで渡すときに指定して、取り出すときにそれを使います。

では実際にコードを見てみましょう。

```kotlin:FormActivity
class FormActivity : AppCompatActivity() {
    private val name by lazy {
        intent.getStringExtra(INTENT_KEY_NAME)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_form)
        setupTextView()
        setupButton()
    }

    private fun setupTextView() {
        textView.text = name
    }

    private fun setupButton() {
        blueImageView.setOnClickListener {
            startActivity(MainActivity.createIntent(this@FormActivity))
        }
    }

    companion object {
        private const val INTENT_KEY_NAME = "intent_key_name"
        fun createIntent(context: Context, name: String): Intent {
            val intent = Intent(context, FormActivity::class.java)
            intent.putExtra(INTENT_KEY_NAME, name)
            return intent
        }
    }
}
```

まずは `createIntent` を見ていただくとわかると思いますが引数が変わっています。
先ほどはcontextだけでしたが `name` を新規で渡すようになりました。
そしてintentにputしてそのIntentを返しています。

```kotlin:MainActivity
            startActivity(FormActivity.createIntent(this@MainActivity,"ヤマダユキ"))
```

MainActivity からこのように呼ばれると intent_key_name の名前空間に　”ヤマダユキ”　が入ります。

そしてFormActivityが起動されたら

```kotlin
    private val name by lazy {
        intent.getStringExtra(INTENT_KEY_NAME)
    }
```
この部分でnameに先ほど入れた”ヤマダユキ”が呼び出され代入されます。

この by lazy ですが　nameが必要になった時に初めて中かっこの中の処理を実行する、といったものです。

さて、もう気づいたかと思いますが `createIntent` を使うことによってnameという引数を絶対に忘れることなくIntentを起動できるようになりました。もしこれがcreateIntentを使わないやり方で起動しようとすると nameを置き忘れたり、そもそもIntentのキーを間違えたり、はたまた変更箇所が増えたり、、と色々問題が出てきそうですね。

ぜひスマートに画面遷移していきましょう。

