---
title: "PythonDjango入門-テンプレートを使ってみる "
description: "Python Django入門記事その２。テンプレートを使ってそれらしいものを表示します。"
author:
  name: "Yuki Yamada"
date: 2018-10-20
draft: false	
categories:
- blogging
tags:
- python
- Django
---

# テンプレートを使ってそれらしいものを表示する
[前回の資料](https://babylonian-tec.kibe.la/shared/entries/82cc8623-706c-4e8f-91ba-261d459c9c76)では各種関数の戻り値に `HttpResponse` を用いてテキストを表示していました。
簡単なテキストを表示するだけならこれでも問題ありませんが、現実でそんなケースはむしろないです。


Webアプリケーションで物を作るなら HTML/CSSやJS,またファイルなどの静的ファイルを活用してユーザーによりよいユーザー体験を届けたいところですが、Pythonは直接HTMLファイルを扱うのは苦手です。そもそも直接HTML/CSSをいじるだけならDjangoを使う必要はありません。

そこでPythonに適した形でHTMLを扱いところです。それを「テンプレート」といいます。テンプレートは変数などPythonで扱っている値をHTMLに代入し表示を行うものでDjangoで用意されているテンプレートは `DTL` と呼ばれます。(Django Template Language)

基本的にはHTMLのように使えます。

# テンプレートを作成、表示する
ではテンプレートを作成してみましょう。

## テンプレートはどこに作成するか
`Project` -> `App` -> `templates`(create) -> `AppName`(create)

createと書かれているファイルを作成します。
例えば私の今回のケースだと
ProjectName:app1
AppName:app1,app2 その中の app2にtempletesというフォルダを作成
templetesというフォルダの中にapp2というフォルダを作りその中にテンプレートを置きます。

ではテンプレートは  `index.html` を作成しましょう。

Pycharmで作成すると

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
```

単純なHTMLが吐き出されました。これを少しだけいじってさっそくテンプレートっぽく使ってみます。

## テンプレート（HTML）で変数を扱う

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>
<body>
```

titleを `{{}}` で囲みました。
これがテンプレートで変数を使うように定義する方法です。

では .py のほうもいじっていきましょう。 title という変数を渡したいですね。

```python
from django.shortcuts import render

def index(request):
    return render(request, "app2/index.html", {"title": "タブ名変更"})
```

index関数を再利用してごにょってみました。

`render`をimportしHTMLをレンダリングしています。

### render関数
テンプレートをレンダリング際に使うショートカット関数です。
第一引数に `HttpRequest` 2, レンダリングするテンプレート 3, 辞書型の変数s
戻り値はTemplateResponseを返します。

では実際に実行してみましょう。

タブ上のテキストが変わっているのが確認できると思います。

以上で簡単なテンプレートの作り方が分かったかと思います。

# ユーザーから入力を受け付ける

ユーザーから入力を受け付けてみましょう。

[前回](https://babylonian-tec.kibe.la/notes/python/66#http%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89) Httpメソッドについてすごくざっくり説明しました。
Httpメソッドには大きく分けて二つ　ユーザー視点に立つと

- データをよこせ
- データを渡すから何かしろ（何か データをDBに　DBから削除　置き換え　等）

です。

では代表的なPOSTメソッドの使い道である　ユーザー登録　「っぽいもの」を作りましょう。
（実際にはDBに登録したりなどが必要ですが今回はガワと受け取りだけやってみましょう。）

## まずはHTMLの用意から

フォーム画面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>

<body>
<h1>ユーザー登録</h1>
<form method="POST" action="{% url "user_info" %}">
    {% csrf_token %}
    <input placeholder="id" name="id" type="text"/>
    <input placeholder="password" name="password" type="text"/>
    <input value="post" type="submit">
</form>
</body>
```

受け取った情報を表示する画面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>

<body>
<h1>ユーザー情報</h1>
<h2>id</h2>
<p>{{ id }}</p>
<h2>password</h2>
<p>{{ password }}</p>
</body>
</html>

```

```python:views.py
from django.http import HttpResponse
from django.shortcuts import render


def render_register_form(request):
    return render(request, "register/register.html", {"user_info": "user_info"})


def register(request):
    if request.POST["id"] and request.POST["password"]:
        return render(request, "register/user_info.html", {
            "id": request.POST["id"],
            "password": request.POST["password"]
        })
    else:
        return HttpResponse("必須項目がPOSTされていません")
```

```python:ulrs.py
from django.contrib import admin
from django.urls import path

from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('form/', views.render_register_form),
    path('registered_user_info', views.register, name="user_info")
]
```
ではコード(スクリプト）を元に解説していきましょう。

## テンプレートタグ
HTML中に `{% %}` といったタグが見られるかと思います。
これはテンプレートタグと言われるものでDjangoに用意されているタグ機能です。
結構種類がたくさんありますがここでは url というテンプレートタグを使用しています。その他にもifテンプレートタグやリープテンプレートタグがありテンプレートをプログラミングちっくに技術することができます。

使い方は非常にシンプルで

```html
{% url name %}
```

と書き、 このnameとしていしたURLが書き出されます。

今回のケースでは

` <form method="POST" action="{% url "user_info" %}"> `

フォームのアクションで指定していますのでフォーム内のinputがpostされた際には user_info の名前をもつURLに飛びます。

ではこのサンプル中ではどこに飛ぶかと言いますと
urls.pyで指定している

` path('registered_user_info', views.register, name="user_info")` 

こいつが呼び出されます。


### [memo]CSRF対策
CSRFとは Cross site request forgeries の略で外部から該当サイトへのフォーム送信などを送る攻撃です。
正しいフォームから送信されたアクセスかどうかを確認する必要があるのですが、それがDjangoには組み込まれているためそれを有効化してねということが `{%  csrf_token %}` となります。


# よりDjangoを使う意味を持たせるために

ちょっとまだ全体的にコードが芋くさいですね。
よりテンプレートを使いこなしてみましょう

## 条件分岐

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{% if id <= 10 %}
    <h1>登録が早い</h1>
{% elif id <= 100 %}
    <h1>普通</h1>
{% else %}
    <h1>遅め</h1>
{% endif %}
</body>
</html>
```

Pythonの構文と変わりませんが、最後に `endif` が必要です。

```python
    path("<int:user_id>", views.render_sample)
```

URLにidを含めて動作を確認してみてください。

## ループ
```html
{% for item in list %}
    <h1>{{ item }}</h1>
{% endfor %}
```

リストの展開も可能

## 継承
テンプレートはなんと継承させることができます。
大まかなLPなどの骨組みが決まっているケース、そうでなくともヘッダーやフッターは固定なことが多いでしょうからそれらをベースのテプレートとして組み込み、それらを継承してテンプレートを作っていくこともできます。

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    {% block head %}
        <meta charset="UTF-8">
        <title>{% block title %}{% endblock %}</title>
    {% endblock %}
</head>
<body>
{% block content %}
{% endblock %}
<div id="footer">
    {% block footer %}
        footer
    {% endblock %}
</div>
</body>
</html>
```

継承元は↑のように作ります。
block で囲み　タイトルを付けます。

```jinja
{% extends "register/base.html" %}
{% block title %}
    Extends Title
{% endblock %}
{% block footer %}
    Extends Footer
{% endblock %}
```

継承先ではblock title 中身を書く、で書くことができます。

## Formクラスを使ってみる

上の例ではフォームこそ作れましたが正直Djangoっぽい書き方ではありませんでした。
DjangoにはFormを作る要のクラスが存在します。その使い方を説明してきます。

### まずはFormを継承したクラスを作成

```python
from django import forms


class RegisterForm(forms.Form):
    id = forms.CharField(label="id")
    password = forms.CharField(label="password")
```

Formクラスを継承した `RegisterForm` を作りました
そして格納される id, passwordという変数を用意(idとpassword)
実際に代入されているのは `CharField` というインスタンスです。
これはテキストを入力させるものでその他にも数字やメールなど様々なFieldが存在しています。
引数で渡しているラベルは入力欄の横に表示されるテキスト用です。

### 作ったRegisterFormを受け取るHTMLを作成

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>TITLE</title>
</head>

<body>
<h1>ユーザー登録</h1>
<form method="POST" action="{% url "user_info" %}">
    {% csrf_token %}
    {{ form }}
    <input value="post" type="submit">
</form>
</body>
```

変数としてformを受け取ろうとしているだけで特に前回と変わりはありません。

### インスタンス化して渡す
```python
    return render(request, "register/register_form.html", {"form": RegisterForm()})
```

作ったインスタンスを渡すだけです。

## CSSを適用させる

Cssは静的ファイルに属しますので
アプリケーション > static(mkdir) -> .css

で配置しましょう。

てきとーにCSSを作ったら
適用させたい.htmlに

```jinja
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" type="text/css" href="{% static "style.css" %}" />
    <title>{{ title }}</title>
</head>
```

loadしていつも通りできようさせるだけです。