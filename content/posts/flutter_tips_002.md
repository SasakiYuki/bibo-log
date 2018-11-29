---
title: "flutterTips-002-ローカルにデータを保存する(SharedPreferences)"
description: "flutterでローカルにデータを保存する方法を説明します。導入から実装まで。今回はSharedPreferences。"
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
cover : "https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/gci/gci-thumb.png"
---

# FlutterではSharedPreferences
端末に簡単なデータを保存する際Flutterでは `SharedPreferences` で保存します。
これは Key value方式での保存で、AndroidDeveloperならそのまま `SharedPreferences` として使えます。iOSDeveloperなら `NSUserDefaults` がそれに相当します。

SharedPreferencesはXML形式で保存されます。
アプリケーション固有のもので、アプリのアンインストールをしたりアプリのデータを消すと消えます。

データは非同期でディスクに保存されますが、コールバック等がなく書き込みがディスクに保存されることが「絶対」には保証できないため重要なデータを保存するのには向いていません。

AndroidはUIthreadでも扱うことができましたが、flutterでは async awaitを用いた非同期取得になりますのでご注意を。

# pubspec.yamlに依存関係を追加
```yaml:pubspec.yaml
  shared_preferences: ^0.4.3
```

バージョンは[こちら](https://pub.dartlang.org/packages/shared_preferences#-versions-tab-)から確認してください。

その後いつも通り

```
flutter packages get
```

# 使ってみる
プロジェクトを作った際に生成されるカウントアップの例を改造してSharedPreferencesに保存されるようにしてみます。

まずはコードから

## コード

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Flutter Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => new _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0) + 1;
    });

    await prefs.setInt('counter', _counter);
  }

  Future<int> _getPrefCount() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    return (prefs.getInt('counter') ?? 0);
  }

  @override
  Widget build(BuildContext context) {
    _getPrefCount().then((value) {
      setState(() {
        _counter = value;
      });
    });
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Center(
        child: new Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            new Text(
              'You have pushed the button this many times:',
            ),
            new Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: new FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: new Icon(Icons.add),
      ),
    );
  }
}
```

## インスタンスの取得方法
非同期で取得する必要があります。
async/awaitで取得していきます。

```dart
    final SharedPreferences prefs = await SharedPreferences.getInstance();
```

## 値のセット
値のセットは上でインスタンス化した `SharedPreferences` から `setXX` でセットします。これはそれぞれの型に存在します。

例ではint型をsetしています。

```dart
await prefs.setInt('counter', _counter);
```

その他のメソッド
--

- setString(String, String)
- setStringList(String, List<String>)
- setBool(String,bool)
- setInt(String, int)
- setDouble(String, double)

すべてが `Future<bool>` が戻り値です。

## 値の取得
値の取得は上でインスタンス化した `SharedPreferences` から `getXX` で取得します。これはそれぞれの型に存在します。

例ではint型の値をgetしています。

```dart
      _counter = (prefs.getInt('counter') ?? 0) + 1;
```

その他のメソッド
--

- getString(String)
- getBool(String)
- getInt(String)
- getDouble(String)

またすべてのキーを返す ` getKeys()` や 対応するキーの型を指定しない `dynamic get(String)` も存在します。

## 値の削除
remove(String) メソッドが存在します。
今回例では使用していませんが使い方はset,getと同じです。


