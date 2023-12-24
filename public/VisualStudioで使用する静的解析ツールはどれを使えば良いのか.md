---
title: VisualStudioで使用する静的解析ツールはどれを使えば良いのか
tags:
  - VisualStudio
  - コードレビュー
  - 静的解析
  - StyleCop.Analyzers
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---


## 採用したもの

結果としては、`StyleCop.Analyzers` + α を採用しました。
まず導入の過程として、コーディング規約レベルの指摘を減らし、本来確認したいコード全体の一貫性や、共通関数群を使わず勝手に独自実装していないかなどの確認に時間を割きたかった という思いがあった為、
今回の選定の判断基準としては、プロジェクトのコーディング規約(主にコードのスタイル面)をどこまでカバーできるか です。

ただ`StyleCop.Analyzers`は日本語に対応していませんので、指摘メッセージが英語で出てきてしまいます。
英語だとメンバーが読まずにスルーしてしまう事が多いため、選定時も日本語であることが重要視されていました。
そこで一部メッセージを日本語化したものを自分のリポジトリに作成し、そちらを使用しています。

https://github.com/MURAMASA2470/StyleCopAnalyzers

## 静的解析ツールとは

動的解析ツールとは異なり、ソースコードを実行することなく、静的な状態で分析し、潜在的なバグやセキュリティの問題、コーディング規約の違反などを検出するツールです。
JavaScriptでコードの品質を担保する為に使われる`ESLint`も、これに該当します。

## 静的解析ツールの導入により改善出来ること

- 目視ではなく機械的なチェックを行うことでコーディング規約レベルの問題点は、レビュー前にほぼ気付くことが出来る
- 分析ツールからの指摘や修正提案によって、開発者のスキルが向上する
- 確認時間の短縮や開発効率を上げる事で、機械的に判断できない部分の確認に時間を割ける


## ツールの種類

### 静的解析ツール

コードの規則や複雑性に基づいて分析し、警告やエラーを表示可能
今回主に紹介するツール

### 動的解析ツール

主にテストツール。実際にパラメーターを入れた際の挙動などの分析が可能
テストコードを書く必要はあるが、単体テストレベルの確認ができる

### 整形ツール

ある規則に基づいて、コードの自動整形が可能
VisualStudioでは編集タブまたは「Shift+Alt+F」などで実行する


## VisualStudioに標準搭載されている機能

実はVisualStudioには標準でこんな機能が付いています。

### ドキュメントフォーマット機能

ルールに沿ったソースの自動整形ができる(書式ルールは設定から変更可能) インデント崩れや余白位置の誤りなどは、この機能でほとんど修正可能。

### コードメトリクス分析機能

保守のしやすさ(保守容易性指数)をサイクロマティック複雑度、継承の深さ、コードの行数などを元に、ファイル、メソッド単位などで算出してくれる読みづらいソースやバグを生みやすいソースなどを数値的に可視化してくれるので、設計の見直しなどに役立つ。

### アナライザー機能

VisualStudio 2015からは コード分析の機能が標準で搭載されており、コーディング中にリアルタイムでエラーや警告を表示してくれる。

## 主な静的解析ツール

### VisualStudio標準の分析ツール

`Microsoft.CodeAnalysis.CSharp.*`と言う名前のやつ。
VisualStudio 2015以降から標準で搭載されている。
標準機能だが、これでかなりの範囲をカバー可能。

### .NetAnalyzers(旧FxCopAnalyzers)

Microsoft公式のアナライザー。
.NET 5から標準で搭載されており、日本語にも対応している。
コードの複雑さや、セキュリティ面での分析が可能。

https://github.com/dotnet/roslyn-analyzers

https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers/

### StyleCop.Analyzers

今回採用したもの。
他のツールよりスペースの位置、可読性などの見た目部分に特化している。
日本語に対応していない。

https://github.com/DotNetAnalyzers/StyleCopAnalyzers

https://www.nuget.org/packages/StyleCop.Analyzers

### Roslynator

Microsoft公式のアナライザー。
CLIからの操作に対応 (バッチから操作したりと応用が利く。)
日本語に対応していない。

https://github.com/dotnet/roslynator

https://www.nuget.org/packages/Roslynator.Analyzers

### NetFabric.Hyperlinq.Analyzer

Enumerator関連、主にLINQのことを指摘してくれます。

https://github.com/NetFabric/NetFabric.Hyperlinq.Analyzer

https://www.nuget.org/packages/NetFabric.Hyperlinq.Analyzer


### Meziantou.Analyzer

非同期、引数名違い、正規表現関連の指摘などをしてくれます。
少し口うるさいアナライザーです。
全て有効にするのではなく、好きなものを部分的に有効にする方法をおすすめします。

https://github.com/meziantou/Meziantou.Analyzer

https://www.nuget.org/packages/Meziantou.Analyzer