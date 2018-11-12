---
title: "flutterTips-001-WebViewを使う"
description: "flutterでWebViewを使う方法を説明します。導入から実装まで。"
weight: 1
author:
  name: "Yuki Sasaki"
date: 2018-11-12
draft: false
categories:
- blogging
tags:
- flutter
- flutterTips
---

cover : "https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/gci/gci-thumb.png"
draft: false

FlutterでWebViewを使うためには少しいじる必要があります。

# flutter_webview_plugin
Flutterで Nativeの `WebView` を呼び出すには `flutter_webview_plugin` の依存を追加する必要があります。

```pubspec.yaml
dependencies:
  flutter_webview_plugin: "0.2.1+2"
```

バージョンは[こちら](https://pub.dartlang.org/packages/flutter_webview_plugin#-versions-tab-) からご覧ください。

ターミナル上で

```
flutter packages get
```

インストールすることで使えるようになります。

また、WebViewはWdigetTreeには統合されていないためSnackBarやDialogを使うところはできません。

インポート文は

```dart
import 'package:flutter_webview_plugin/flutter_webview_plugin.dart';
```

# 実装
Flutter Navigationを利用して起動します。
公式サンプルのままですが

```dart

new MaterialApp(
      routes: {
        "/": (_) => new WebviewScaffold(
              url: "https://www.google.com",
              appBar: new AppBar(
                title: new Text("Widget webview"),
              ),
            )
      },
    );
```

`WebviewScaffold` を使ってその中で諸々セットして使います。
特に難しいことはありません。


# その他の使い方
WebViewはシングルトンインスタンスで提供されているのでどこからでもイベントをハンドリングできます。

## WebView内でページが移動した際のイベント
WebViewで移動するイベントをハンドリングできます。

```dart
    final flutterWebViewPlugin = new FlutterWebviewPlugin();

    flutterWebViewPlugin.onUrlChanged.listen((String url) {
      print(url);
    });
```

## WebView内のスクロールイベント
WebView内のスクロールイベントをハンドリングできます。

```dart
    flutterWebViewPlugin.onScrollYChanged.listen((double offsetY) {
      print(offsetY);
    });

    flutterWebViewPlugin.onScrollXChanged.listen((double offsetX) {
      print(offsetX);
    });
```

## WebViewを隠す、隠されたやつを表示する
`WebView` を隠したり表示したりするには

```dart
    // 表示する
    flutterWebViewPlugin.launch("https://www.google.com", hidden: false);

    flutterWebViewPlugin.launch("url",hidden:true);
```

単純に隠したり表示したりするなら

```dart
    flutterWebViewPlugin.show();
    flutterWebViewPlugin.hide();
```

もあります。

## リロード

```dart
flutterWebViewPlugin.reloadUrl(url)
```


## JS実行

```dart
flutterWebViewPlugin.evalJavascript(code)
```

code は String です。

## その他イベント
- Stream<Null> onDestroy
- Stream<WebViewStateChanged> onStateChanged
- Stream<String> onError

WebViewが破壊されたとき
WebViewのステータスが変わった時
WebViewでエラーが起きた時　など。
