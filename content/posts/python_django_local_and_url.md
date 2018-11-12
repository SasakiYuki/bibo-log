---
title: "Pythonにおけるオブジェクト指向"
description: "Python Django入門記事その１。簡単なpythonの説明から実際にdjangoを使ってみます。"
author:
  name: "Yuki Yamada"
date: 2018-10-20
draft: false	
categories:
- python
- Django
---

# Pythonの特徴 
初期リリースは91年で開発当初はオブジェクト指向を意識した設計になっていませんでしたが、途中からオブジェクト指向が組み込まれリリースに合わせてそれらは含まれる形になりました。
あまり日本では注目されることのなかった言語のようですが、機械学習やGoogle app engineにpythonが採用（そもそもGoogleでは社内標準言語としてpythonが採用されていますが）されたため日本でも注目を集め今では珍しくないレベルまで来ています。
スクリプト言語であり学習に向いているとされています。その理由としてはそもそもスクリプト言語が実行しやすかったり（コンパイルが不要）、Pythonの記法がシンプルであることから言われています。中でもインデントブロックは大きな特徴の一つです。


# Webフレームワークとは
さて、では実際にPythonでWebアプリケーションを作ろうとなった時にWebフレームワークの選定が必要になります。
そもそもWebフレームワークとは
Webアプリケーションを作成する際に骨組み（足場）になってくれるもののことを指します。フレームワーク自体が機能群のことで、それのWb版だと思っていただければ問題ありません。最大のメリットは「書くコードが減る」ということです。フレームワークによって大なり小なりありますが作成に必要な機能が盛り込まれており１から書く必要がなくなります。また、書き方なども統一されるため可読性が上がるなどのメリットもあります。
Pythonで有名なWebフレームワークは

- Django
- Flask
- Bottle

などが有名どころです。
下に行くほど軽量なフレームワークとなっていきます。

今回はこの中でも実際に採用されている万能な大型フレームワークであるDJangoを使っていきたいと思います。

# Djangoとは

```
Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design.
```

公式からの引用ですがハイレベルなWebフレームワークで超スピードで実用的なWebアプリケーションを構築するぜ、と言っています。

フルスタックフレームワークと言われる、いわゆる全部盛りフレームワークで、複雑なデーターベース主体のアプリケーションを簡単に書くことができたり、管理者用GUIを自動生成したりするなど便利な機能がたくさん載っています。
また、プロジェクトの構成が単純で生成時のファイルが少ないのもメリットと言えるでしょう。

# セットアップ
早速セットアップしていきます

雑にpythonのパスを通します。(TODO)

IDEはPyCharmを使います

# 新規Djangoプロジェクトの作成

ではさっそくPyCharmを起動してプロジェクトを作成します。

New Project -> Djangoを選択

## プロジェクトファイル
自動生成されるコードの紹介

- __init__.py 

Pythonでは特別な意味を持つファイルで、このディレクトリをPythonパッケージであるということを伝えるために存在しています。

- settings.py

Djangoで生成したアプリケーションの設定をつかさどっているファイルです。

- urls.py

URL宣言が含まれているファイルでルーティングはここで行います。

- wsgi.py

作ったPythonプロジェクトをWebサーバーにデプロイする際に使用されるファイルです（エントリポイントを定義している）
Webアプリケーションのメインのプログラムとなります。

- manage.py

プロジェクトで実行される機能に関するプログラムが入っています。



## 早速ハローワールド
作られたDjangoプロジェクトをローカルサーバーで起動してみます。
本来であれば先ほど紹介したWSGIをWebサーバーにアップロードして動作を確認、等のことをしなければならないのですがDjangoでは試験用にWebサーバーを起動してアプリケーションを動かすことができます。

`SHIFT ALT R ` を押すと `manage.py` が実行されるので runserver してみましょう（ポートは任意で問題ありません。デフォルトだと8000番が開かれます。

そして指定されたURLに飛ぶとハローワールドできていることが確認できると思います。

以上でハローワールドは終了です。

# MVCアーキテクチャ
ハローワールドも終わったことですしさっそくDjangoでWebアプリケーションを作りたくなっているかと思いますが、その前に重要な「MVCアーキテクチャ」について理解しましょう。
MVCとは

M Model
V View
C Controller

のイニシャルをとっていてそれぞれ

Model データのアクセス関係の処理を担当している部分です　主にWebアプリとデータベースとのやり取りを担当してくれます

View 見た目を担当しています。画面に表示されるものです。

Controller ユーザーからの操作を受け付けたりこのWebアプリケーションではどうするべきかの処理などを制御する部分です

例えば

ユーザーがWebページにアクセスしました
コントローラーはビューを参照して画面を表示しつつモデルにDBからデータを引っ張ってくるよう処理して結果を表示する、、

などということが行われています。

それぞれの担当を分けてロジックを当てはめよう、と簡単に覚えておいてください。

 # プロジェクトとアプリケーション
Djangoでプロジェクトを先ほど作りましたが、プロジェクト内に直接コードを書いていくわけではありません。このプロジェクト内に異なる複数のアプリケーションを作り、それらを組み合わせて一つのプロジェクトとします。それぞれのアプリケーションはＭＶＣを持ち、独立しています。

# V Viewを作る
今説明したアーキテクチャーの説明で Viewというものが出てきました。実際にこのViewを使ってハローワールドをしたいのですがプロジェクトを作った段階ではそれは存在しません。

`views.py` を作成しましょう。

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello World")
```

まず初めの行では `HttpResponse` というクラスをインポートしています。
Webブラウザなどからアクセスされた際に送り返す内容を管理することができるクラスです。

## index関数
引数にrequestをもらっています。これはアクセスされた際に渡される `HttpRequest` というクラスのインスタンスが渡されるわけですが、今は一旦何かが渡されている、というくらいにとどめておきましょう。

でその戻り値としてテキストのHello Worldを今回は渡しています。

これでViewの準備はできましたが、まだアプリケーションに反映はされていません。
なぜかというとURLが特定のパスと関連づいていないからです。

ではどうすればいいいか urls.pyをいじります。

# Urls.py

```python
urlpatterns = [
    path('admin/', admin.site.urls),
]
```

初期状態では管理画面を呼び出すための `admin` のみがある状態となっています。
足らないので追加しましょう

```python
from django.contrib import admin
from django.urls import path

from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path("",views.index)
]
```

先ほど作ったviewsをインポートします。
そしてpathにルートパス（つまり空）を用意して、呼び出す関数を指定します。

path(アドレス,処理)

実際に runserverしてみるとルートパスにてハロワされているのがわかると思います。

## 複数のアプリケーションのルーティング

前の項でdjangoに限らずWebサービスは複数のアプリケーションを組み合わせてできているといいました。
複数のアプリケーションを作りそれらを Urls.pyでルーティングしてみましょう。

### 設定ファイルを分ける
まずはアプリケーションを追加してみましょう。
サンプルアプリを追加します

` CRT SHIFT R `  
```
startapp sample
```

すると必要なコード群が生成されるのでその中の views.pyを書き換えましょう。
注意しなければならないのは 最初に作ったアプリケーションのディレクトリではなく今作った sample/views.pyを参照します。

処理内容自体は先ほど作った views.py と代わり映えのしないもので問題ありませんが関数を二つ定義してみましょう。

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("index page")


def simple(request):
    return HttpResponse("Simple page")
```

`index page` と表示するやつと `Simple page` と表示するものの二種類です。

作ったこれらの関数を先ほどと同様 urls.pyにルーティングします。こちらも同様に気を付けてほしいのですがsample/urls.pyに書き加えます。

```python
from django.contrib import admin
from django.urls import path

from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", views.index),
    path("simple", views.simple)
]
```

ほぼ先ほどと変わりませんが、今回はsimple path を使うことにしました。

そして /urls.pyも修正しましょう。

```python
from django.contrib import admin
from django.urls import path, include

from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", views.index),
    path("sample/", include("sample.urls"))
]
```

urlsのインポートに今まではpathだけでしたが、特定のパスを関連付けるための includeも追加します。
`sample` パスに先ほど作ったulrsを使うよというものを追加して実行してみましょう。

開いたポートに対してパスを指定します。今回だったら `sample/simple` のように追加して画面が正しく表示されれば実装は成功です。

### 【こぼれ話】URLの構成

`scheme://host.domain[:port]/path/dataname`

URLはこのような構成によって成り立ちます。
今回はこの中でよく利用されるスキームとホストとパスについて簡単に説明します。

#### スキーム
URLの先頭に来るもので使用するプロトコルを指定する部分です。１０個ほど存在しますが基本的には `http` や`https` を使用します。

#### ホスト
そのままホスト名を指しホストとはリソースを提供しているサーバーのことです。ドメインとサブドメインを掛け合わせたもので、 `http://twitter.com` なら twitter.com の部分

#### パス
先ほどから指定しまくっているパスはホスト以降のスラッシュで区切られている部分を指しています。
パスに応じてどういうアイテムを返すのかはプログラムの処理次第です。
ApacheなどのWebサーバーはディレクトリーに紐づいているためそのファイルを指定しなければいけません。

# URLのクエリやHTTPメソッドをさばく
今までの例ではURLは静的なもので呼び出していましたが、実際のURLは

http://example.com/photo/{id}

といった形や

http://example.com/photo?id=1

といったように動的にアクセスしたい情報へのURLが変わってきます。

このようなものをクエリパラメータと言います。
実際にクエリ内容の入ったURLを処理してみましょう。

```
?id=119 
```

views.pyの中のメソッドを書き換えてみましょう
先ほど 引数に渡されている `request` は `HttpRequest` のインスタンスですよ、といいましたがこの中にクエリの情報が入っているので出してみます。

```python
    photoId = request.GET["id"]
    return HttpResponse("query id is " + photoId)
```

Responseも何がクエリされていたのかわかるようにちょっと変えてみました。

実際に上のURLのように叩いてみるとクエリから値を受け取ったことがわかると思います。
うまくは行きましたが、完全ではありません。クエリ内容をとってURLを叩くとエラーが出てきてしまうかと思います

`MultiValueDictKeyError`

まぁ要するにコード上で必要とされているものがねーぞというエラーです。
もしかしたらidが渡されないなんてことはあり得そうなので処理を分けないといけません。

```python
    if "id" in request.GET:
        result = "query id is " + request.GET["id"]
    else:
        result = "plz send id"
    return HttpResponse(result)
```

requestのGETに id というものが含まれていますか？というのを `in` で確認します

https://babylonian-tec.kibe.la/shared/entries/46b058fb-deb3-4270-bf5e-a585ef1480ce

制御構文の基本でやった辞書型がここ(GET)で使われているため in が使えるわけです。

## クエリではなくURL内のパスをそのまま扱う

上の例ではクエリにidを渡してそれを使用しましたが、URLのパスに含まれた値をそのまま使ってみます。

```
def pattern(request, user_id, photo_id):
    return HttpResponse("userId = " + user_id + "\nphotoId = " + str(photo_id))
```

新規で関数を作ってみました。引数はrequestのみでなく userId:String　と photo_id:Int を想定しています。
こちらは特別なことは特にしていません。
キモはURLPattersnのほうです。


```
    path("<user_id>/<int:photo_id>", views.pattern)
```

そのままですが渡された値をuser_idという変数に入れたり、photo_id(int)をpatternに渡しています。
intを指定することでもし文字列がきたらそもそもURLが間違っている判定になり処理されることはありません。

## HTTPメソッド
HTTPでは同じパスに対して異なる処理を提供することが可能です。

- GET	データの取得
- POST データの作成　データの追加
- PUT	データの更新、データの作成
- DELETE	データの削除

主なのはこの辺です。

POSTメソッドの生成及び受け取りは次回テンプレートを使ってフロントを整えてから確認するので今回は送られてきたメソッドが何かを判断する処理を追加します。

```python
def http_method(request):
    if request.method == "GET":
        return HttpResponse("GET Method")
    elif request.method == "POST":
        return HttpResponse("POST Method")
    elif request.method == "PUT":
        return HttpResponse("PUT Method")
    elif request.method == "DELETE":
        return HttpResponse("DELETE Method")
    else:
        return HttpResponse("Other Method")
```


次回テンプレートを使って色々する