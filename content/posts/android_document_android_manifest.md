---
title: "AndroidManifestについて"
description: "勉強会で説明する際に用いた追加資料です。AndroidManifestについて簡単なマトメになっています。"
author:
  name: "Yuki Sasaki"
date: 2018-10-04
draft: false
categories:
- blogging
tags:
- Android
---

# AndroidManifestとは
AndroidManifest.xmlはすべてのプロジェクトに等しく一つ存在するものです。

作っているAndroidアプリに関する情報をAndroidのOS(システム）に伝える役目を担っており、コード実行の際に必要になる重要な情報を記述する必要があります。

プロジェクトの基底ディレクトリに `AndroidManifest.xml` として置く必要があり名前を変えることなどはできません。

`XML` で記述されており、タグで要素を構築します。

## AndroidManifest内で行うこと

- アプリを構成する要素
- ユーザーパーミッション
- スタイルや名前、アイコンなどを指定
- Intentのフィルター

## ファイル内の規則

AndroidManifestで必須の要素は `<manifest>` `<application>` のみです。
これらは省くことはできず、逆にそれ以外は任意ということになります。（実質必要というものは存在します）
また `manifest` と `application` はそれぞれ一回ずつのみ宣言され、階層は `manifest` -> `application` の中に存在しないといけません。

実際に見てみましょう。
例えばAndroidStudioでプロジェクトを初期で作った時に生成されるmanifestは以下です。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="string">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

manifestタグの中にapplicationが存在しているのがわかると思います。

注目してもらいたいのは２行目 `xmlns:android="http://schemas.android.com/apk/res/android"`
この.xmlファイルはAndroidManifestですよ、と宣言しているということになります。
また、xml上に `android` のネームスペースを定義しています。またこの中の属性は絶対に `"http://schemas.android.com/apk/res/android"` である必要がありますのでご注意ください。

次に application内を見てみると様々な情報が書いてあることがわかると思います。
上から

- バックアップ処理をするか
- アイコン
- アプリ名
- 丸アプリアイコン
- RtLsupportの有無
- アプリ全体のテーマ設定

を現しています。

## activityタグ
アプリ内で利用しているActivityはここで記述する必要があります。
MainActivityが作られているのですでにここに `<activity android:name=".MainActivity">` と記述されていますが例えば SubActivityを作った場合は追加で `<activity android:name=".SubActivity">` と記述する必要があります。
記述しないとビルド時にそのクラスが認識されずアプリが落ちることになります。現在はAndroidStudio上でActivityを生成すると自動で記述されることもあり忘れることは少なくなりました。

## intent-filterタグ
Activityタグ内に `intent-filter` というタグが存在しているのがわかると思います。
```
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
```

MainActivityのintentに要素を付け加えるタグです。
intentとはAndroidの用語でアプリの画面遷移時に使われる用語です。

```
使い方の例
MainActivityからSubActivityにインテント（遷移）させるときにあのデータを渡す必要がある
```

### intent-filter
まず必須で知る必要があるのはたったの二つです。

```
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
```

これだけ。例ではMainActivityタグの中に入っていますがどういう意味か
上が「メインの画面」であることを示す
下が「アプリが起動した際はこいつを呼ぶ」という定義

です。
なので極端な例を出すと

```
        <activity android:name=".MainActivity" />
        <activity android:name=".SubActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
```

`SubActivity` が定義されていて `MainActivity` という名前でも `SubActivity` にこれら二つの要素を付けたら `SubActivity` がメイン画面だし、アプリ起動時にはこちらが呼ばれます。


その他にもintent-filterは[deeplink](https://medium.com/raccoonwallet/raccoonwallet%E3%81%AFdeeplink%E3%81%AB%E5%AF%BE%E5%BF%9C%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B%E8%A9%B1-21621a685fc3)などの技術に用いられたりしますが、一旦忘れておいてください。