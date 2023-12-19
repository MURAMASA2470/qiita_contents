---
title: 結局、PHP(Laravel)のPDF出力ライブラリはどれを使えばよいのか
tags:
  - PHP
  - PDF
  - Laravel
  - PDF出力
  - 帳票
private: false
updated_at: '2023-12-10T23:40:12+09:00'
id: ba284c54d245fefabe5a
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

PHPでPDFのライブラリを選定している時に、残したメモを記事化します。
かなり多くある中で、プロジェクトに合ったライブラリを探すのは時間がかかりました。
少しでも皆さんのライブラリ選定の判断材料になれば幸いです。

:::note warn
情報は2023年9月時点のものです。
:::

## 採用したもの

この中で最終的に私が選んだのは`mpdf/mpdf`のLaravelラッパーである`carlos-meneses/laravel-mpdf`です。
使用できるCSSに若干のクセがあるものの、基本的なHTMLやCSSは対応しており、ある程度の複雑なレイアウトにも対応できます。
特に便利だったのが、ページ番号や改ページ制御などを標準搭載している点です。
他にもバーコード発行機能などもある為、商品/在庫管理システムなどでも使えるのではないでしょうか。

https://github.com/mccarlosen/laravel-mpdf


## 各項目の情報源

- 個人的な評価
    - 選定するにあたり、基準を満たしているかどうか(選定基準は上記の「採用したもの」を参照)
- サポート
    - 現在も定期的なアップデートがされているか
- 利用者数
    - GithubのUsed byの数を参照
- 日本語の記事
    - リポジトリ名で検索した際に、ヒットした日本語記事の体感の数
- ライセンス
    - リポジトリのライセンス
- 最終更新日
    - 最終コミット日付を参照




# PHPのPDF出力ライブラリ

## KnpLabs/snappy
- 個人的な評価: ✕
- サポート: 〇
- 利用者数: 不明
- 日本語の記事: 普通
- ライセンス: MIT
- 最終更新日: 2023/01
- 特筆事項: composer以外にサーバー本体にライブラリをインストールする必要がある。(wkhtmltopdf)

https://github.com/KnpLabs/snappy

## dompdf/dompdf
- 個人的な評価: △
- サポート: 〇
- 利用者数: 100k
- 日本語の記事: 多い
- ライセンス: LGPL-2.1
- 最終更新日: 2023/01
- 特筆事項: 使用できるCSSに制限あり, 単純な表を表現するだけなら〇

https://github.com/dompdf/dompdf

## tecnickcom/TCPDF
- 個人的な評価: ✕
- サポート: ✕
- 利用者数: 21.3k
- 日本語の記事: 多い
- ライセンス: LGPL-3
- 最終更新日: 2022/12
- 特筆事項: サポート終了(新バージョンを開発中だが未完成)、HTMLで使えるタグに制限あり

https://github.com/tecnickcom/TCPDF

## mpdf/mpdf
- 個人的な評価: 〇
- サポート: 〇
- 利用者数: 25.7k
- 日本語の記事: 少ない
- ライセンス: GPL-2.0
- 最終更新日: 2023/05
- 特筆事項: CSSにクセがあるが、他のに比べそのままデザインを出力しやすい。共通ヘッダーやページ番号などの機能あり。今回は使わないが、奇数偶数ページだけの処理や、ブックマーク、バーコード発行機能なども標準で備えている

https://github.com/mpdf/mpdf

# 上記のLaravel版のラッパー

## barryvdh/laravel-snappy
- 個人的な評価: ✕
- サポート: 〇
- 利用者数: 4.7k
- 日本語の記事: 少ない
- ライセンス: MIT
- 最終更新日: 2023/04
- 特筆事項: snappyのLaravelラッパー

https://github.com/barryvdh/laravel-snapp

## barryvdh/laravel-dompdf
- 個人的な評価: △
- サポート: 〇
- 利用者数: 72.9k
- 日本語の記事: 普通
- ライセンス: MIT
- 最終更新日: 2023/02
- 特筆事項: dompdfのLaravelラッパー

https://github.com/barryvdh/laravel-dompdf

## elibyy/tcpdf-laravel
- 個人的な評価: ✕
- サポート: △
- 利用者数: 1.4k
- 日本語の記事: 少ない
- ライセンス: MIT
- 最終更新日: 2023/09
- 特筆事項: TCPDFのLaravelラッパー

https://github.com/elibyy/tcpdf-laravel

## carlos-meneses/laravel-mpdf
- 個人的な評価: 〇
- サポート: 〇
- 利用者数: 1.5k
- 日本語の記事: 少ない
- ライセンス: MIT
- 最終更新日: 2023/05
- 特筆事項: mpdfのLaravelラッパー

https://github.com/carlos-meneses/laravel-mpdf
