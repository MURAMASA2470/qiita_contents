---
title: PowerShellでパスに半角スペースが含まれるファイルの実行方法
tags:
  - Windows
  - PowerShell
  - path
private: false
updated_at: '2023-12-11T02:55:24+09:00'
id: 2ec52457923f6600067c
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに

PowerShellで`C:\Program Files\`配下などのファイルを実行する方法を記載します。
ディレクトリ移動して実行すればいいじゃん！と言われてしまえばそれまでですが、ワンライナーで書きたい時などにこの方法が使えます。
コマンドプロンプトだとダブルクォーテーションで囲むだけで実行できるのに、なぜPowerShellで出来ないのかは神のみぞ知る所です。

## 結論

パス全体をダブルクォーテーションで囲った後に、先頭に`&`を付けることで実行できます。

```powershell
&"C:\Program Files\hogehoge\hoge.exe"
```

## まずは普通に実行

そのまま実行すると以下のようになります。

```powershell
PS C:\> C:\Program Files\hogehoge\hoge.exe
# C:\Program : 用語 'C:\Program' は、コマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認
# 識されません。名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試
# 行してください。
# 発生場所 行:1 文字:1
# + C:\Program Files\hogehoge\hoge.exe
# + ~~~~~~~~~~
#     + CategoryInfo          : ObjectNotFound: (C:\Program:String) [], CommandNotFoundException
#     + FullyQualifiedErrorId : CommandNotFoundException
```

## ダブルクォーテーションを付けて実行してみる

ダブルクォーテーションを付けただけだと、文字列としてそのまま表示されてしまいますね。

```powershell
PS C:\> "C:\Program Files\hogehoge\hoge.exe"
# C:\Program Files\hogehoge\hoge.exe
```

## ではどうするか

パスの先頭に`&`を付けることで、ファイルを実行することが出来ます！

```powershell
PS C:\> &"C:\Program Files\hogehoge\hoge.exe"
```
