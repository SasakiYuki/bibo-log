---
title: "PythonDjango入門-DBに触れる"
description: "Python Django入門記事その3。DBを使ってみます。"
author:
  name: "Yuki Yamada"
date: 2018-11-05
draft: false	
categories:
- python
- Django
---

# データベースが必要になる理由
前回までにユーザーの登録フォームなどを作ってきましたが、特にデータの保存等はしてきませんでした。
Webアプリケーションを作る上で何かしらのデータを保存することは絶対に必要になってきます。データを個人で永続的に使うというだけであればデータベースでなくとも何かしらの形でファイルにアウトプットすればいいでしょうが、Webアプリケーションでは同時に別のユーザーから開かれる可能性があるのでファイルを開いていたら何か障害が起きかねません。そもそもアクセス速度やデータ分類などの面で問題がありそうです。

データベースはそれらを解決するために長年進化を続けてきました。特にRDBMS(リレーショナルデータベースマネジメントシステム　説明するにはちょっとDBの知識がいるので今は要するに『普通のDB』とでも思っていてください）は長く、現在も使われています。現在ではNoSQLなどRDBMSとは違うDBも出てきていますが、今回はこのRDBを学んでいきます。

データベース全体の特徴は

- なんといってもデータアクセス速度が速い
- 高度な検索が可能
- 同時アクセスされることを前提に作られている

# DjangoでDBを使う
上記の通りDBを使うことにメリットが非常にあることがわかりましたが、一口にDBといっても色々なものが存在します。
Djangoだと使われがちなのは

- SQLite
- MySQL
- PostgreSQL

でしょうか。
それぞれ特徴があります。すごく雑に説明すると（上から）

- djnagoプロジェクトを作ると初期設定はこれ　軽い、高速　だけど大規模に向いてないので学習や小規模など　ちなみにAndroidで採用されている
- 最も多く採用されているんじゃないかと言われている
- 導入コストが低く済む　

すべて共通してオープンソースで、SQLというデータアクセス言語を使っています。

今回はPostgreSQLでいきましょう！

# djangoにてPostgreSQLを設定をする

まずはインストールをします。
MacOSなら Homebrewを使ってすぐに終わります。

```
brew install postgresql
```

Winだったらまずはインストーラーを[こちら](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)からダウンロードしてください。

インストーラー起動するとディレクトリとかの設定は基本デフォルトで問題ないですが見慣れないスーパーユーザーのパスワードの設定をしてください、という項目があると思うので任意のものを設定してください。
またポートはデフォルトの5432でいいでしょう。
ロケールは デフォルトで

で、パスを通します。

環境変数設定で　C:\Program Files\PostgreSQL\11\bin　先ほどインストールした bin を通して

パスが通ってることを確認。

```
psql --version
```

通ってたら成功です。


`settings.py` をご覧ください。デフォルトのままであれば

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

このような記述をしてあるところがあると思います。
今はSQLiteで設定されているのでここをPostgreSQLに置き換えます。

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': DB名,
        'USER': ユーザー名,
        'PASSWORD': パスワード,
        'HOST': 'localhost',
        'PORT': 5432
    }
}
```

さぁ実際にモデルを作っていきましょう。

### No module named 'psycopg2'
って言われたら `pip install psycopg2` しましょう！

# モデルを作る
DBの設定ができたので実際に入れていくデータの構造を定義していきたいですね。この「データ構造」のことをモデルと言います。前々回MVCアーキテクチャの話をしましたがそれのMに相当する部分です。

実際に作ってみましょう。

`models.py` に書きます。

```python
class Member(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=20)
    isAdmin = models.BooleanField()
    birthday = models.DateField()
    home_page_url = models.URLField()
```

そんなに特別な部分はないのでわかるかと思いますが、idだけ補足しておくと名前付き引数で `primary_key` にTrueを渡しています。これはこのidという要素にはAuto(自動的)に重複しない値をいれてくれるようになるように書いてあります。

ではモデルができたところで `マイグレーション` をしていきましょう。

マイグレーションとは開発の途中でデータベースの構造を変更した際にどのような変更がされたか書くことです。
都度必要なので忘れないように。

最初に行うのはマイグレーションの設定ファイルをプロジェクト内に作成することです。

# マイグレーション

（マイグレーションファイルがない場合）
マイグレーションファイルを作る必要があります。

```
python manage.py makemigrations AppName
```

```
    - Create model Member
```

これが出てれば問題ないです。

migration管理ファイルが完成したら実際にマイグレーションを実行します。

```
python manage.py migrate 
```

これで実際にマイグレーションを行います。

# DBのCRUD 
データベースでの基本動作は

- create
- read
- update
- delete

の４つです。

それらをやっていきましょう。

# C Create 
ではさっそく保存ロジックを書いてみましょう。

```python
def post_form(request):
    if request.method == "POST" and request.POST["name"] and request.POST["birthday"] and \
            request.POST["home_page_url"]:
        if "isAdmin" in request.POST:
            is_admin = True
        else:
            is_admin = False
        member = Member.objects.create(
            name=request.POST["name"],
            isAdmin=is_admin,
            birthday=request.POST["birthday"],
            home_page_url=request.POST["home_page_url"]
        )
        member.save()
        return HttpResponse("保存完了")
    else:
        return HttpResponse("必須項目がPOSTされていません")
```

インスタンス化して.save()を呼ぶだけです。

```python
class MemberForm(forms.Form):
    name = forms.CharField(label="name")
    isAdmin = forms.BooleanField(label="isAdmin", required=False)
    birthday = forms.DateField(label="birthday")
    home_page_url = forms.URLField(label="home_page_url")
```

今までの知識を使うと Formクラスを継承したFormを作り、そこからPOSTさせて受け取って処理、、ということになりそうですがちょっとイケてないですね。せっかくフォームを独自で作ったのでPOSTメソッドから自分でparseして..ちょっと煩雑です。
そこで、ModelFormクラスを継承したフォーム作成を使ってみます。

```python:views.py
def post_model_form(request):
    if request.method == "POST":
        instance_member = Member()
        member = MemberModelForm(request.POST, instance=instance_member)
        member.save()
        return redirect(to="/dbman/index")
    else:
        return HttpResponse("必須項目がPOSTされていません")
```

バリデーションはちょっと雑になりましたがかなりsave回りがすっきりしたことがわかると思います。
空のMemberクラスをインスタンス化して、それをFormのinsntanceに渡します。第一引数であるrequest.POSTは言わずもがなですがPOSTの情報が入っているのでこのMemberModelFormのコンストラクタ内で自動的にPOSTがパースされてい入力されています。



```python:MemberModelForm
class MemberModelForm(forms.ModelForm):
    class Meta:
        model = Member
        fields = ["name", "isAdmin", "birthday", "home_page_url"]
```

ModelFormは中にMetaクラスを持っています。フォームに関する情報が用意されており、`model = ` に使用するモデルクラスを、 `fields = ` にフォームで表示する値をセットします。


# R Read
## 管理画面編
モデルを作っただけでは管理画面から編集はできません。「このモデルは管理画面から編集できますよ」という宣言をするにはそれぞれのアプリケーションの`admin.py` を編集します。

```python
from django.contrib import admin

from dbman.models import Member

admin.site.register(Member)
```

これで編集できるようになりますのでadmin画面を開いてみましょう。

ユーザーを作っていませんでしたね。作りましょう。

```python
python manage.py createsuperuser
```

作ったアカウントで入ってみると作ったモデルが表示されているかと思います。
ここから追加することもできるのでぜひ確認してみてください。

## コード上で
パーセントコーディングのfor文でリストをさばきます。

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<table>
    <tr>
        <th>id</th>
        <th>名前</th>
        <th>管理者か</th>
        <th>誕生日</th>
        <th>ホームページURL</th>
    </tr>
    {% for member in members %}
        <tr>
            <th>{{ member.id }}</th>
            <th>{{ member.name }}</th>
            <th>{{ member.isAdmin }}</th>
            <th>{{ member.birthday }}</th>
            <th>{{ member.home_page_url }}</th>
        </tr>
    {% endfor %}
</table>
</body>
</html>
```

```python
def post_form(request):
    if request.method == "POST" and request.POST["name"] and request.POST["birthday"] and \
            request.POST["home_page_url"]:
        if "isAdmin" in request.POST:
            is_admin = True
        else:
            is_admin = False
        member = Member.objects.create(
            name=request.POST["name"],
            isAdmin=is_admin,
            birthday=request.POST["birthday"],
            home_page_url=request.POST["home_page_url"]
        )
        member.save()
        return redirect(to="/dbman/index")
    else:
        return HttpResponse("必須項目がPOSTされていません")

def render_index(request):
    members = Member.objects.all()
    return render(request, "dbman/index.html", {"members": members})
```

urlsももちろん変更しますがsave後にリダイレクトされるように修正します。

# U Update
情報のアップデートを行います。
基本的な流れとしては
対象のidをpathなどから取得　そのidのアイテムを取得してその情報をformに表示 postされたら変更情報を保存する

この流れでいきたいと思います。

まずは `urls.py` の変更からいきましょう。

```python:urls.py
    path("edit/<int:user_id>", views.render_edit, name="edit"),
    path("post_edit/<int:user_id>", views.post_edit, name="post_edit")
```

一覧画面からのidを渡されることを想定してフォームを表示する法の画面にはnameを先に設定しておいてみました。

Edit用のフォームを用意します。基本的には登録フォームと同じです

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>TITLE</title>
</head>

<body>
<h1>ユーザー情報の編集</h1>
<form method="POST" action="{% url  "post_edit" id %}">
    {% csrf_token %}
    {{ form }}
    <input value="post" type="submit">
</form>
</body>
```

違うところは actionのところで `post_edit` のほかにも id を 引数として受け取っているところでしょう。これはurlのpathに相当します。

次にviewsをいじって実際にDBから対象のデータを取得してみましょう。

```python:views.py
def render_edit(request, user_id):
    member = Member.objects.get(id=user_id)
    return render(request, "dbman/edit.html", {"id": user_id, "form": MemberModelForm(instance=member)})
```

`urls.py` から受け取ったパスのidを使ってそのまま `Member.objects.get(id=user_id)` で取得します。
あとは基本いつも通りです。 

実際の更新処理に関しても

```python
def post_edit(request, user_id):
    member = Member.objects.get(id=user_id)
    member_form = MemberModelForm(request.POST, instance=member)
    member_form.save()
    return redirect_index()
```

Createとほぼ一緒です。インスタンスを作るのではなくprimaryKeyであるIDを指定してあげてsaveを呼ぶだけです。

# D Delete
DeleteもCreateやUpdateと特に違うことはしません。

```python:views.py
def delete(request, user_id):
    member = Member.objects.get(id=user_id)
    member.delete()
    return redirect_index()
```

関数だけ用意してみました。あとはpathから呼んでも、どんな形でもいいので実装してみてください。
