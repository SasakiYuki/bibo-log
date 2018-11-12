---
title: "Pythonにおけるオブジェクト指向"
description: "Python Djangoを勉強する際にPythonを知らなったので勉強しました。その際にPythonはどのような言語使用でオブジェクト指向を実現しているのか気になったので調べつつ纏めました。"
author:
  name: "Yuki Yamada"
date: 2018-10-16
draft: false	
categories:
- python
---

# オブジェクト指向とは
プログラミングを効率的に行うための考え方、手法の一つ。概念。
効率的に行うには `共通の設計思想` が大切で現在では主流の一つ。

関連のあるデータや処理をまとめて「オブジェクト」にし、コードを管理しやすくすることである。

pythonはオブジェクト指向言語だが、関数があったりと割と自由な言語である。
javaから来た人間としてはオー自由だなーと思うことが多い。（どっちがいいとかそういう話ではない）

## オブジェクト指向を構成する要素
- クラス
- メソッド
- データ
- プロパティ
- インスタンス
- カプセル化
- 継承
- ポリモーフィズム

では説明していきましょう。


# クラス
`Class` とは`オブジェクトの設計図` です。
クラス内にはオブジェクトを作る際に必要なデータやメソッド（後述、処理）などが記述されています。

定義の方法は簡単で

```
class `クラス名` :
```

これらの中にデータやメソッド追加していくことで一つのクラスになります。

## [memo] カラのclass
空のクラスというのも存在します
仕組み的にpythonでは `:`  以降何もないのは無理なので

```python
class EmptyClass:
    pass
```

と記述します。

## [memo] クラスの命名規則
ThisIsClass のように単語区切りで先頭は大文字に

# メソッドの定義
`method` とはクラス内に記述された`処理`。
定義されたデータを使って何かしたり、クラスに関する何かを定義したりします。
関数との大きな違いはクラスに属しているという点です。

```
class ClassName:
    def MethodName(self,その他引数):
        処理
```

メソッドは必ず一つの引数を持ちその一つとは `self`　です。
ただし呼び出し自体は

```
インスタンス化（後述）されたクラス.MethodName()
```

でselfは記述する必要がありません。
このselfとは自身を指しており、 `.java` なら `this` みたいなものです。
勝手に参照されるから不要なわけです。


## 実際にクラス、メソッドを作ってみる
身近な例であるモンスターストライクのモンスターをオブジェクトにしてみましょう

```python
class Monster:
    def display(self):
        print("Name:", self.name)
        print("Luck:", self.luck)
```


`Moster` というクラスに`display`というメソッドを作りました、
自身のnameとluckを表示するメソッドです。

これを実際に使ってみましょう

```python
monster = Monster()
monster.name = "クイバタ"
monster.luck = 99
monster.display()
```

クイバタを定義し、実際にdisplayを行うと

```
Name: クイバタ
Luck: 99
```

こんな具合で表示されます。成功です。

ちなみに オブジェクトを作り出すことを `インスタンス化` といいます
`monster = Monster()` この部分

インスタンス化したあとに名前やラックを付与していますが名前とラックは絶対に欲しい情報なので後から入れる形にすると忘れちゃいそうで怖いです。実際入れずに `display()` するとエラーが出ます。ではどうするか。
名前やラックはインスタンス化されたときにすでに付与されていてる形なら絶対に忘れません。
インスタンス化するときにそのままnameもluckも代入するためにはどのようにやればいいか。その際は `__init__` メソッドを使います。

```python
    def __init__(self, name, luck):
        self.name = name
        self.luck = luck
```

initメソッドにnameとluckを渡してこのインスタンス自身の nameとluckに代入します。

もちろんですがインスタンス化するところも変化します。

```ptyhon
monster = Monster("クイバタ", 12)
monster.display()
```

これでモンスターを　オブジェクト化　することができました。

## なぜ関数ではなくメソッドで実装するのか
関数としてもこの `display` は実装できます。

```
def display(monster):
    print("Name:", monster.name)
    print("Luck:", monster.luck)
```

この関数を用意すれば同じものが実装できます。一見どこからでも使えて、便利そうです。
しかし、例えば今後 Monsterクラスだけではなくユーザー情報をdisplayしたりアイテム情報をdisplayしようとしたときこのメソッド名が重複しそうです。

```
displayMonsterData()
displayUserData()
displayItemData()
```

解決策としてはこうなるでしょう。確かに何をdisplayするかメソッド名から一目でわかります。しかし今は三つだからいいですがこれがより複雑になった時、、、例えばモンスターも味方モンスターなのか敵モンスターなのかはたまたボスモンスターなのか...考えだしたらキリはありませんがそうなるたびにメソッド名が複雑になっていきそうです。

これらをそれぞれのクラスにしてみましょう。

```
Monster.display() // モンスターをdisplayしそうと一目でわかる
User.display()
Item.display()
```

それぞれの処理をクラスにカプセル化（後述）できた上にメソッド名も簡素になりました。これらがオブジェクト化する利点です。



のように命名がかぶって中の処理が大変なことになりそうです。
これらも

```
Monster.display()
User.display()
Item.display()
```

それぞれのクラスにメソッドがあるほうが処理が明確になり、可読性も上がるでしょう。

# データ
オブジェクトが持っている変数のことをデータと言います。

# カプセル化
オブジェクト指向では外からいじられる必要のないものを隠すことをカプセル化と言います。Pythonでデータを定義すると基本的に外からいじることができます。一番最初に名前やラックは外から入れましたがあのようなことです。

外からいじられる必要のないものとは何か、またそうするとどのような効果が見込めるか。
まずはいじられる必要のないものの例を挙げます。

今回使用している例では Monster は LevelとLuckを持っています。Luckの上限値は99です。
まずはそれを指定しましょう。

```python
class Monster:
    LUCK_LIMIT = 99

    def __init__(self, name, luck):
        self.name = name
        if (luck > Monster.LUCK_LIMIT):
            print(
                str(Monster.LUCK_LIMIT) +
                "より上は指定できません。" + str(Monster.LUCK_LIMIT) + "に設定します。")
            self.luck = Monster.LUCK_LIMIT
        else:
            self.luck = luck
```

まずは上部 `Luck_LIMIT = 99` を見てください。
変数名通り 運の上限値を99としています。このようにクラス内に定義した変数をクラス変数と言います。
で、それを利用してラックの上限を99にするようなコードがinstance時に行われているわけですが、今のコードではカプセル化できていません。
LUCK_LIMITは外からいじられたくない値ですが、今のように通常のクラス変数ではどこからでもアクセスして書き換えられてしまいます。まずはクラス変数の特性を確認しつつどうやってカプセル化するかを確認していきましょう。

### [memo]定数
定数を定義するときは可読性を考慮して
- 大文字
- 文字の区切りはアンダースコア　
などをすると未来の自分や他の人が見た際にすぐに定数だと気が付けます
pythonには定数を定義する文法は存在しない模様
javaなら private static などと指定します。


## クラス変数
クラス変数はどこからでもアクセスできます。

例えば今回の例ではLuckは99ですが10に変更できちゃったりするわけです。

```python
print(Monster.LUCK_LIMIT)
Monster.LUCK_LIMIT = 10
monster = Monster("クイバタ", 122)
monster.display()
print(Monster.LUCK_LIMIT)
```

実行結果

```
99
10より上は指定できません。10に設定します。
Name: クイバタ
Luck: 10
10
```

初めはLuck99ですがLUCK_LIMITに外からアクセスして10を指定した結果上限が10になっているのがわかると思います。

## 定数をカプセル化したい
上のようにそのままのクラス変数では読み、書きがどっちもできる状態になっています。
これはptyhonでは作られた変数のアクセス制限がpublicだから発生します。これではカプセル化されているとは言えません。
ただし、どうやらpythonにはprivateな変数はないようです。pythonエンジニアの慣習として `_` を一つつけて　「これはprivateな変数やぞ」という風に良心に任せる設計になっているようです。
個人的にはめちゃめちゃ気持ち悪いのでそれをなんとか！と思い少し調べてみると後述するマングリングでお茶を濁すパターンが多いらしい...

```
    __LUCK_LIMIT = 99
```

アンダースコアを二つつけるとマングリングによって疑似的にprivateにできます。

これでビルド時にエラーを出すことができました。しかしまだカプセル化は出来ていません。
モンスターのラックの上限値をインスタンス時は制御できていますが、その後制御できていません。具体的には

```python
monster = Monster("クイバタ", 1)
monster.display()
monster.luck = 120
monster.display()
```

こういうことをされちゃうと

```
Name: クイバタ
Luck: 120
```

ラックが上限を突破します。何が起きているかというとインスタンス変数が直接書き直されてしまっているため発生しています。
先ほどはクラス変数をprivateにしましたが、今度はインスタンス変数を__にしましょう。プロパティを用います。

### [memo]おさらい クラス変数とインスタンス変数とは
ザックリ説明すると
クラス変数はすべてのインスタンスで使用する変数です。
一方でインスタンス変数はインスタンス毎に値が管理されています。

クラスを介せばインスタンス化せずに扱える値で、.javaではstaticな変数です。
一方でインスタンス変数はインスタンスされた際に初めて生成されるのでインスタンス毎に別のものがメモリに記憶されます。

// TODO 追記　例

## プロパティ
プロパティはデータの参照や設定をするものを制御するときに使います。今回の例だと不適切な値（luckを上限を超えた120などがセットされてします）を制御するために使います。

使い方

```python
class Class名:
    @property
    def 変数名(self):
        処理
    @変数.setter
     def 変数名(self,変数);
        処理
```

今回の例だとこうです

```python
    @property
    def luck(self):
        return self.__level

    @luck.setter
    def luck(self, value):
        if value > self.__LUCK_LIMIT:
            value = self.__LUCK_LIMIT
        self.__luck = value
```

ここでもアンダースコアを付与することでprivateなインスタンス変数にしています。
LUCK_LIMITよりも高い時にLUCK_LIMITを代入するような処理にしています。このように代入を制御するようなものをPythonに限らずセッターと呼びます。

これで実際にLUCK_LIMITより多いものを代入しようとしても

```python
monster = Monster("クイバタ", 1)
monster.display()
monster.luck = 120
monster.display()
```

```実行結果
Name: クイバタ
Luck: 1
Name: クイバタ
Luck: 99
```

120を指定していますが99になっています。成功です。

ではやっとマングリングについて話しましょう。

## マングリング
先ほど話した通りpythonでは基本的に定義されたものは明示しない限りpublicなものでどこからでも参照できます。これではオブジェクト指向にのっとったプログラミングはできません。ではどのようにカプセル化するか。pythonではマングリングという機能で行います。
今までやってきたように __ （アンダースコア二つ）をすることでマングリングはできます。これを行うことでprivateなメソッド　メンバー（変数）　にすることができていたわけです。
ちなみに `__init__` はアンスコ２つからスタートしていますが末尾にアンスコ２つ以上ついている場合はマングリングは無効化されます。

ただし厳密にはマングリングを行うことでprivateになっているというわけではなく _クラス__名前という風に変換されるため外部から呼び出すことはできなくなっているということで、上のような書式にしてしまえばエラーもでずにアクセスできてしまいます。
それに気が付いてアクセスするのはもうオブジェクト指向が理解できていないか設計がうまくいっていないので考え直しましょう。
少なくとも__名前では呼べなくなるのでうっかり呼ぶことはなくなるので十分privateだろう？というのがpythonの考え方のようです。

# 継承
クラスは既存のクラスを使用して、新しいクラスを作ることができます。
意味わかんないですね。
例えばモンストだと友情コンボっていう弾いたときに自分以外のキャラにあたった時に攻撃が出てくるものがあります。
友情コンボの種類は無限にあります。
ほとんどのケースで１弾きに一回しか出ないという仕様は共通しています。まずはそれを書いてみます。

```python
class FriendAttack:
    __FRIEND_ATTACK_COUNT = 1

    @property
    def attack(self):
        return self.__attack

    def __init__(self):
        self.__attack = 0

    def on_hitted(self):
        if (self.__attack < self.__FRIEND_ATTACK_COUNT):
            self.active_friend_attack()
            self.__attack = 1

    def active_friend_attack(self):
        print("友情コンボ発動")

```

最大回数、つまり一回以上は発動しないような処理になっています。これは友情コンボの共通仕様です。

```python
attack = FriendAttack()
attack.on_hitted()
attack.on_hitted()
attack.on_hitted()
```

なんど呼び出しても一回の

```
友情コンボ
```

しか出てきません。

友情コンボは無限に種類はありますが一回しか呼び出されない、これだけは不変です。なのでこの仕様を「継承」しながらホーミングや十字レーザーを実装してみましょう。

継承方法

```
class 派生クラス(基底クラス):
    処理
```

では実際にFriendAttackを継承したホーミングクラスを作ります。

```python
class Homing(FriendAttack):
    def active_friend_attack(self):
        print("ホーミング友情")
```

Homingクラスを作り FriendAttackを継承しています。
そして先ほど定義したものと同じ `active_friend_attack()` を再定義しています。
これはオーバーライドと言い処理を上書きしているような感じです。

```
homing = Homing()
homing.on_hitted()
homing.on_hitted()
```

上と同じように呼び出してみると

```
ホーミング友情
```

指定したように表示されます。
継承することで　一回だけしか発動しないよ　という部分の処理や　発動するタイミング(on_hitted())処理などを省け、active_friend_attack()の中身のみを記述すればよくなりました。

このように共通した処理を基底クラスに記述して、派生クラスでそれ独自のものを記述することを継承を使ってオブジェクト指向プログラミングでは設計していきます。


# ポリモーフィズムとは
日本語では多態性。意味不明ですね。
もうちょっとかみ砕いた意味にすると同じメソッドでも与えるクラスによって処理が違うこと。意味不明ですね。
実例を出してみましょう。

```python
class FriendAttack:
    __FRIEND_ATTACK_COUNT = 1

    @property
    def attack(self):
        return self.__attack

    def __init__(self):
        self.__attack = 0

    def on_hitted(self):
        if (self.__attack < self.__FRIEND_ATTACK_COUNT):
            self.active_friend_attack()
            self.__attack = 1

    def active_friend_attack(self):
        print("友情コンボ")

class Homing(FriendAttack):
    def active_friend_attack(self):
        print("ホーミング友情")

class CrossLaser(FriendAttack): 
    def active_friend_attack(self):
        print("クロスレーザー")

def friend_attack(friend_attack):
    friend_attack.active_friend_attack()

homing = Homing()
friend_attack(Homing())
friend_attack(CrossLaser())
```

```実行結果
ホーミング友情
クロスレーザー
```

`def frined_attack(frined_attack)`　に渡すClassによって実行結果が変わっていることがわかると思います。

オブジェクトを気にせずメソッドを実行できるようにする、というのが大事なわけです。

より具体的な使い方にしてみましょう。
実際にはモンスターによって発動される友情コンボは違います。外から渡してあげましょう。

```python
class Monster:

    __LUCK_LIMIT = 99

    @property
    def luck(self):
        return self.__level

    @luck.setter
    def luck(self, value):
        if value > self.__LUCK_LIMIT:
            value = self.__LUCK_LIMIT
        self.__luck = value

    def __init__(self, name, luck, friend_attack):
        self.name = name
        if (luck > self.__LUCK_LIMIT):
            print(
                str(self.__LUCK_LIMIT) +
                "より上は指定できません。" + str(Monster.LUCK_LIMIT) + "に設定します。")
            self.luck = Monster.LUCK_LIMIT
        else:
            self.luck = luck
        self.friend_attack = friend_attack

    def display(self):
        print("Name:", self.name)
        print("Luck:", self.__luck)

    def on_hitted(self):
        self.friend_attack.on_hitted()

```

インスタンス化する際にFriendAttackをくれ、としています。
今回のケースではHomingを渡しました。それがなぜまかり通るかというとHomingはFriendAttackを継承しているからです。そして`on_hitted()` はHomingではオーバーライドされているため `ホーミング友情` が表示されるわけです。


# まとめ
あんまり `Python` はオブジェクト指向の学習には向かないかもしれないと思った。
この説明も.javaを知っているから理解できてる～みたいなのもあるかもしれない。
