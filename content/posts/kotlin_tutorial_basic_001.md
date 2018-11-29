---
title: "Kotlin超入門①"
description: "Koltinの最初の一歩になってほしい記事。Java解読者ではなく、プログラミング初心者向け。オブジェクト指向は含まれません"
weight: 1
author:
  name: "Yuki Sasaki"
date: 2018-11-20
draft: false
categories:
- blogging
tags:
- flutter
- flutterTips
---

# 実行環境
本当は実行環境をローカルに作りたいところですが、今回は皆さんで実行環境を合わせるためにWeb上のツールを使いましょう。

https://try.kotlinlang.org/#/Examples/Hello,%20world!/Simplest%20version/Simplest%20version.kt


# ハローワールド
コンソール上にて `Hello world` を表示してみましょう。
実行結果に何かを表示するときには `print()` 関数を使います。

```kotlin
fun main(args: Array<String>) {
    print("Hello world")
}
```

printのかっこ内に文字を入力することでコンソール上に表示されます。

`fun main` ~~~　の当たりは今は必要なものだと思って流し読みしてください！

## 文字or数字
今回は `Hello world` という `文字列` を表示しました。
文字列は上記の通り `" "` のようにダブルコーテーションで囲むことで明示的に文字列であるとコンパイラーに伝えます。

では数字はどうでしょう。
数字では特に何かで囲むことなく表示することがあります。ではやってみましょう。

```kotlin
    print(1)
```

上の通り数字では特に囲う必要はありません。

この仕様は `print()` だけに限らず後述する変数やメソッドの場合にも「文字列ならダブルコーテーションで囲む」「数字ならそのまま入力」するとなりますので忘れないようにしましょう。

# 変数
`Kotlin` に限らずプログラミングには「変数」と言われる概念が存在します。プログラミングの中ではデータは変数というもので扱います。データとは数字であったり文字列であったりします。

変数という言葉は日常生活には馴染みがないですが、よく言われるのは「データを入れるための箱」と例えられるくらい簡単なものです。

## 変数の定義
Kotlinには変数の定義の仕方がいくつかあります。

- 後に再代入可能な型を宣言した変数
- 後に再代入が不可能な型を宣言した変数
- 後に再代入可能な型を宣言しない変数
- 後に再代入不可能な型を宣言しない変数

の４種類存在します。

まず大きく分けて「再代入が可能かどうか」

### 代入
先ほどから代入、再代入という言葉が出ていますがプログラミングにおける代入とは変数に値を入れることです。用語として今後出てくるので確実に覚えてください。

### 再代入が不可能な変数の定義

```kotlin
    val 変数名 = 初期化する値
```

kotlinでは変数を定義する際に絶対に初期化する値を代入しなきゃいけません。なぜしなければならないかは明確に理由が存在しますが、今はしなければならないものとして考えておいてください。
実際にval で宣言してみましょう。

```kotlin
    val amount = 1
    val name = "山田"
    print(amount)
    print(name)
```

`amount` という変数名で数字を入れています。
また、次に `name` という変数名で `山田` という文字列を入れています。

それぞれを出力するようにprint()の関数に変数を渡すとそれぞれ表示されます。

```kotlin
    val amount = 1
    val name = "山田"
    print(amount)
    print(name)
    name = "ユキ"
```

また、print後に再代入しようとするとコンパイルエラーとなりビルドは通りません。

ここで再代入可能なほうに置き換えたらどうなるか確認してみましょう。

### 再代入が可能な変数
再代入が可能な変数は `val` ではなく `var` として宣言します。

```kotlin
fun main(args: Array<String>) {
    var amount = 1
    var name = "山田"
    print(amount)
    print(name)
    name = "ユキ"
    print(name)
}
```

`val` -> `var` に置き換えただけのコードです。
先ほどは `ユキ` の部分でエラーが発生しましたが `var` は再代入が可能な変数の定義になるのでエラーは発生せず実行結果としても二度目の代入となる  `ユキ` が表示されます。

### [tips]なぜvalを使う？
一見してみるとvarが便利でvalを使う必要がないように思いますが決してそんなことはありません。
少し難しい話にはなるんですが、自分でない誰かがコードを読んだときにこれらの違いが明確に出てきます。

varが出てきたら意識的に「これは後で変更される値だな」という意識でリーディングしていくことになります。一方でvalで宣言されている変数を見た場合「この値は後程変更されることがない、実質宣言された変数として読み替えてリーディング’できる状態ということになります。

今は少し理解は難しいかもしれませんが、意識的に変更がない変数は「val｝、変更がある場合は「var」を使っていくことだけは意識してみるといいと思います。


### 変数の宣言時に型を指定する
変数の宣言時に「型」を指定することができます。

###  型？
変数には型という概念が存在します。
型とは変数に「文字列」か「整数」か「小数点」かなどその変数がどういった種類のものかを示すものです。
例えば ageという変数があったとします。このageは入ったとしたら
- １０歳（文字列）
- 10 (整数）

などが自然です。これが例えば

```kotlin
var age:Float  = 2.4 // 2.4歳??
```

 2.4歳のような小数点の値が入ると違和感が発生します。
では変数にはどのような型が存在するでしょうか。

色々ありますが代表的な種類としては以下です。

- Int
- String
- Float
- Boolean

それぞれ入る値の種類が違います。

例えば上の例だと

Int -> 	符号つき整数 -2147483648～2147483647
String -> 文字列
Float -> 	32ビット単精度浮動小数点数 
Boolean -> 真偽値 True or False

### 型を宣言しつつ変数を宣言する場合

```kotlin
fun main(args: Array<String>) {
    var amount:Int = 1
    var name:String = "山田"
    print(amount)
    print(name)
    name = "ユキ"
    print(name)
}
```

変数名のあとに `:型` を置くことで宣言できます。

これは絶対にするべきものではありません。書く人それぞれに「絶対にすべき」ケースがあったり「どちらでもいい」「むしろ書かない」などのルールが存在します。

### [tips]変数のルール
言語的なルールで数字から命名はスタートすることはできません
またその他にもお作法的なルールが存在します。（絶対守る必要はないけど、多くの場合で守られているルール）

- 変数名は小文字スタート大文字区切り　ex. userName mailAddress など
- 単語スタート　英単語

実践で変数を宣言する場合はルールを守ることで多人数開発時にスムーズになることがありますので守っていきましょう。

# 制御構文
kotlinに限らずプログラミングでは制御構文と言われるものが存在します。
例えば「ｘｘの時ｙｙする」ものだったり「ｘｘの間ｙｙを繰り返す」などの制御構文があります。具体的に下で説明していきます。

## if文
if文は「条件に合っているとき何かをする」といった制御構文です
日常に置き換えてみると

喫茶店に行く　もし「シフォンケーキがあったら」　シフォンケーキを注文する

といったように「もし」「if」のように使います。

これを実際にkotlinの構文に起こしてみると下のようになります

```kotlin
if(条件式) {
    // 何かする
}
```

となります。今回のシフォンケースのでいうと条件式には「シフォンケーキがあったら」という条件式が入ります。

if文の詳しい解説をする前に条件式について詳しく解説します。

### 条件式
条件式とは真偽値が返ってくる式のことです。
変数の型の説明で `Boolean` という型があることを説明しました。これは true or false　という２択の値が入る型です。Boolean型は真偽型と言われます。似たように真偽値も true or false のどちらかが返ってくる式のことを指します。
では条件式と通常の式の違いはなんでしょう。

```kotlin
    val number1 = 1+1
    val number2 = 4-2
    val boolean1 = number1 == number2
    print(boolean1)
```
number1 は `1+1` が加算されて `2` というint型の整数が入る通常の式です。
number2も同様に `4-2` が実行され `2` が代入される通常の式です。
そして boolean1 は number1 と number2 の値を `==` した値が入っています。これに何が入るかというと `true` という真偽値が入ります。

この `==` というのは比較演算子と言われるものの一つです。

### 比較演算子
演算子|意味|例|オペランドの型
-------- | ---- | -- | ------------------- |
`<` | 未満 | 2 < 3	| 数値 
`>`  | 超える | 2 > 3 | 数値
`<=` | 以下 | 2 <= 3 | 数値
`>=` | 以上 | 2 >= 3 | 数値
`==` | 等しい | 2 == 3 |任意
`!=`  | 等しくない | 2 != 3 |任意

改めて上のサンプルコードを見てみると `true` が返ってくるのがわかっているかと思います。

ぶれましたが if文に戻ってみましょう。

### 条件式のユースケース

```kotlin
	val tanakaAge = 12
    val yamadaAge = 10
    
    if (tanakaAge > yamadaAge) {
        print("田中のほうが年上です")
    }
```

この例で式が評価されると `true` です。
すると if文の中に入ってprint()関数が実行されます。 `{}` で挟まれた部分をブロックと言います。


ただこれだともし田中のほうが年下だった場合に何も処理がされなくて困ります。

その場合は `else` 文を使います。

```kotlin
	val tanakaAge = 9
    val yamadaAge = 10
    
    if (tanakaAge > yamadaAge) {
        print("田中のほうが年上です")
    } else {
        print("田中のほうが年下です")
    }
```

ifブロックの後で `else` をすることで「if条件文で当てはまらなかったら...?」のブロックを生成することができます。

ただしこれでもまだ一つ抜けがあります。同い年だった場合です。
その時は `else if` 文を使います。

```kotlin
	val tanakaAge = 10
    val yamadaAge = 10
    
    if (tanakaAge > yamadaAge) {
        print("田中のほうが年上です")
    }else if (tanakaAge == yamadaAge) {
      print("同い年です")  
    } else {
        print("田中のほうが年下です")
    }
```

これですべてのケースに対応することができました。

## When文
When文はif文に近い使い方ができます。対象をとってその値がｘｘだったら...?という使いかたをします。

```kotlin
    val yamadaLevel = 3
    when(yamadaLevel) {
        0 -> print("初心者すぎます")
        1 -> print("もう一レベル上げて")
        2 -> print("合格です")
        else -> print("値不正")
    }
```

値を直接指定して使います。もう少し応用した使い方がありますが今はこの程度にしておきましょう。

## for文

for文は繰り返し処理をするための制御構文です。

```kotlin
    for(i in 1..10) {
        print(i)
    }
```

上の例は1から10まで繰り返すfor文です。

実行してみてください。

次にコレクションを使ったfor文をやります。

```kotlin
    val list = arrayOf(1,30,22,14)
    for(i in list) {
        println(i)
    }
```

リストを渡すと一つずつ In の左の変数に取り入れられます。

### コレクション
上で雑に「リストを渡す」という言葉出てきましたが、変数をコレクション形式として扱う方法があります。

```kotlin
val intList = arrayOf(1,2,3,4,5)
val stringList = arrayOf("a","b","c")
```

arrayOf関数を使うことでコレクションとして使えます。

コレクションの対象のコレクションにアクセスするには

```kotlin
    val stringList = arrayOf("a","b","c")
    println(stringList[0])
```

`[]` ないに数字を入れることで取り出せます。
ちなみに注意するべきは 0 からスタートするという点です。一見１番目に入っていそうな `a` ですが 0番目に入っていますので注意。

# クラス、メソッド
Kotlinに限らず「オブジェクト指向」に則ったプログラミング言語はクラス、メソッドに処理を格納（カプセル化）することで人間が読みやすい、保守しやすいコードを作っていくことを前提に設計されています。

オブジェクト指向は理解するのが少し難しい部分で、それだけで本になるほど大事ですが難しい部分です。オブジェクト指向は理解しなくてもコードは書けますが、いずれ理解しなければいけない日が来ます。が今日ではない！

「クラス、メソッド」がオブジェクト指向においてどういう意味を持つのかは一旦置いておいて「クラス、メソッド」はどうやって使うのか　をまず理解してみましょう。

## クラス
`Class` とは`オブジェクトの設計図` です。（説明が説明になっていないパターンのやつ）
もう少しかみ砕いて言うと、クラス内にはオブジェクトを作る際に必要なデータやメソッド（後述、処理のことを言います）などが記述されています。

定義の方法は簡単で

```
class `クラス名` {}
```

これらの中にデータやメソッド追加していくことで一つのクラスになります。

クラスを作ってみましょう。
名前と年齢の変数を定義した `User` というクラスを作ります。

```kotlin
class User {
    var name:String =""
    var age:Int = 0 
}
```

ではこの `User` を使ってみます。

```kotlin
fun main(args: Array<String>) {
    val yamada = User()
    yamada.name = "yamada"
    yamada.age = 21
    
    print(yamada.name)
    print(yamada.age)
}

```

まずは１行目　`yamada` という変数名に `User` を代入しているのが見れるかと思います。
先ほど作った `User` クラスを作り出し変数に格納しています。データの何もないユーザーオブジェクトを作ったわけです。
このクラスを変数にぶち込むことを `インスタンス化` と言います。
その後インスタンス化した `yamada` 変数の中身をいじっています。
yamada(Userクラス)は `name` と `age` を持っているのでそれらをいじってユーザーに正常な値を代入します。

`yamada.name` .変数　とつなげることでクラス内に存在する変数を参照することができます。その後は今までの変数と同じように代入することができます。

### コンストラクタ
上の例ではインスタンス化したあと変数にアクセスしたことで yamada やら 21やらが代入できましたがこれだと少し困ったことがあります。

代入し忘れ

それを防ぐためにコンストラクタという仕組みがあります。
要するにクラスをインスタンス化するときにnameもageも上げれば解決じゃない？という話です。

どうやるか

```kotlin
class User(val name:String,val age:Int) {}
```

非常に簡単でクラス宣言時に`()`を追加してそのまま`name` と`age`を宣言するだけです。こうすることでインスタンス化時にこれらの値が渡されないとコンパイルエラーとなります。

```kotlin
  	val yamada = User("yamada",23)
```


## メソッド
メソッドは処理を切り分けるために使います。
同じような処理が発生しそうであったり、処理をひとくくりにすることで可読性を担保するためにメソッドという形でまとめます。

多くのメソッドはクラスに属しています。
クラスをインスタンス化することで呼び出せる「インスタンスメソッド」とそうでなくとも呼び出せる「スタティックメソッド」の二種類存在します

### メソッドを定義する
```kotlin
fun メソッド名() {
}
```

基本の形はたったのこれだけです。一つ定義してみましょう。
先ほどのUserクラスにメソッドを追加してみます。

```kotlin
class User {
    var name:String =""
    var age:Int = 0 
    
    fun displayUserName() {
        println("私の名前は $name です")
    }
}
```

`displayUserName()` というメソッドを用意してみました。
メソッドの中身は名前を表示しているだけです。

では下で実際にこの`displayUserName()` を呼び出して見ましょう。

[tips]$name
--
kotlinでは簡単に文字列の中に変数を使うことができます。
dollarを付けて、その後ろに変数名をいれるだけです。


### 呼び方
メソッドを定義したら呼び出す必要があります。
インスタンス化したクラスから `.`で繋いで定義したメソッドを呼びます。

上の例で作った `displayUserName` を呼び出してみましょう。

```kotlin
fun main(args: Array<String>) {
  	val yamada = User()
    yamada.name = "yamada"
    yamada.age = 21
    
   yamada.displayUserName()
}
```

インスタンス化したUserである`yamada`から`.`でつなげて作った`displayUserName()`を呼び出しています。かっこを忘れないように気を付けてくださいね。

### 引数
メソッドには引数を持たせることができます。
引数とはメソッドを呼び出すときにメソッドの実行に必要な値を外から渡すことができる仕組みです。

```kotlin
fun methodName(引数、引数、、、、） {
    処理
}
```

例を挙げます 先ほどのUserクラスを使いまわします。

```kotlin
fun main(args: Array<String>) {
  	val yamada = User()
    yamada.name = "yamada"
    yamada.age = 21
    
  	yamada.displayUserName("おいどん")
}

class User {
    var name:String =""
    var age:Int = 0 
    
    // personalPronounsは一人称の意
    fun displayUserName(personalPronouns:String) {
        println("$personalPronouns の名前は $name です")
    }
}
```

さっきの`displayUserName`は一人称が「私」で固定でしたが、そんなもんその時の気分によります。だから外から指定できたほうがいいでしょう。

ということで引数として`personalPronouns`という`String`を渡すことにしました。
これによって外から値を指定できたことになります。

また引数はいくらでも渡すことができます。

例を挙げます。一人称が　おいどん　なのに語尾が　です　は違和感しかありませんので語尾も指定できるようにします。

```kotlin
    fun displayUserName(personalPronouns:String,endingOfWord:String) {
        println("$personalPronouns の名前は $name $endingOfWord")
    }
```

`,` で連ねていきます。呼び出すほうも同様に`,`で引数を連ねます。

```kotlin
    yamada.displayUserName("おいどん","ごわす")
```


### 戻り値
戻り値とはメソッドを呼び出し、最終的に戻ってくる値のことです。
つまりメソッドとは何らかの処理を実行し、その処理結果に応じて何らかの値を返すことができるということです。

使い方は

```kotlin
fun methodName(引数、引数、、、、）:戻り値の型 {
    処理
    return 戻り値
}
```

例を挙げます。
Userクラスをまた使いまわしますが、今のところageを入力したのに特に使っていませんでした。使いましょう。引数として現在の年を入力することで生まれ年を返すメソッドを作ります。

```kotlin
    fun calculateBirthYear(currentYear:Int):Int {
        return currentYear - age;
    }
```

現在の年から年齢を引くことで（穴がありまくりですが今回はちょっと見逃して）生まれ年が計算できます。

では呼び出してみましょう。

```kotlin
fun main(args: Array<String>) {
  	val yamada = User()
    yamada.name = "yamada"
    yamada.age = 23
    
  	yamada.displayUserName("おいどん")
    yamada.displayUserName("おいどん","ごわす")
    println(yamada.calculateBirthYear(2018))
}

class User {
    var name:String =""
    var age:Int = 0 
    
    // personalPronounsは一人称の意
    fun displayUserName(personalPronouns:String) {
        println("$personalPronouns の名前は $name です")
    }
    
    fun displayUserName(personalPronouns:String,endingOfWord:String) {
        println("$personalPronouns の名前は $name $endingOfWord")
    }
    
    fun calculateBirthYear(currentYear:Int):Int {
        return currentYear - age;
    }
    
}
```


### 同名メソッド
メソッドは同名の物を定義することができます。
ただし、引数が違う場合のみです。
Javaでは同名メソッドを複数用意しますがkotlinではそういったことはしなくてもいいような仕組みが用意されています。

まずは一般的な同名メソッドの使い方から見ていきましょう

というかすでに実はやっていますね。
例で挙げた `displayUserName` は同名ですが引数が違うおかげで定義できています。
もしこれが引数が同じで処理の中身だけ違う場合はコンパイルエラーが発生するでしょう。コンパイラーがどちらを呼び出せばいいか判断できないからです。

では次にkotlinでの応用的な使いかたを見てみます。

```kotlin
    fun displayUserName(personalPronouns:String,endingOfWord:String = "です") {
        println("$personalPronouns の名前は $name $endingOfWord")
    }
```

`endingofWord` を定義してすぐに代入しています。今回は`です`を代入しています。
こうすることで`です`が初期値として定義され、もう一つのメソッドは消すことができます。コード全文を見てみればピントくると思います。

```kotlin
fun main(args: Array<String>) {
  	val yamada = User()
    yamada.name = "yamada"
    yamada.age = 23
    
  	yamada.displayUserName("おいどん")
    yamada.displayUserName("おいどん","ごわす")
    println(yamada.calculateBirthYear(2018))
}

class User {
    var name:String =""
    var age:Int = 0 
    
    // personalPronounsは一人称の意
    
    fun displayUserName(personalPronouns:String,endingOfWord:String = "です") {
        println("$personalPronouns の名前は $name $endingOfWord")
    }
    
    fun calculateBirthYear(currentYear:Int):Int {
        return currentYear - age;
    }
    
}
```

`displayUserName()`は一つに減りましたが、引数が一つの`yamada.displayUserName("おいどん")`もコンパイルエラーなく実行され、さらに初期値である`です`が表示されているかと思います。
