---
title: Apple Safariの閲覧履歴をエクスポートする
tags:
  - Safari
  - SQLite3
  - history
  - 履歴
  - エクスポート
private: false
updated_at: '2023-12-23T00:39:53+09:00'
id: c29a9209c0d80956d913
organization_url_name: null
slide: false
ignorePublish: false
---
前回、[Google Chromeの履歴をエクスポートする方法](https://qiita.com/MURAMASA2470/items/d081e71d79c4dc36671e)を記事にしました。
今回は要望があったSafari版を書いていこうと思います。

Microsoft Edge版については、こちらにまとめてあります。

https://qiita.com/MURAMASA2470/items/35cf540cf44a3c1617ab


## 履歴データの保存場所

Safariの履歴は以下のディレクトリにあります。

```
~/Library/Safari/
```

この中に`History.db`というファイルがあるのでそれが履歴ファイルです

## 履歴データを開くための準備

Google Chromeの履歴の場合は問題ないですが Safariの場合、履歴の保存されている場所にTerminalでアクセスができません。
Mac本体の設定を変更することによりアクセスはできますが、少々危険が伴うため今回は別の方法を紹介します。
※`chmod`などを使用しても不可です。

```
> cd ~/Library/Safari/

// Finderでディレクトリを開く
> open .
```

Finderで開きましたら、その中にある`History.db`というファイルを任意の場所にコピーします。
**コピーは必ずSafariを完全に終了してから行ってください。(ファイルが正しく開けない時がありました)**
※今回はデスクトップにコピーしたものとします

コピーが完了したら、そのディレクトリに移動して作業の再開です。

```
> cd ~/Desktop/

> sqlite3 History.db

// テーブル一覧の表示
sqlite> .tables

```

今回は各テーブルの説明は省きます。
今回使うテーブルは`history_items`と`history_visits`の二つを使います。

```sqlite3
// .mode [モード名] でモード指定
sqlite> .mode csv

// .output [ファイル名].csv でファイル名指定
sqlite> .output result.csv

// ヘッダーをつける
sqlite> .headers on

// 抽出(アクセス時間、ドメイン、タイトル、URL)
 sqlite> select datetime(v.visit_time + 978307200, 'unixepoch', 'localtime') as date, i.domain_expansion, v.title,  i.url from history_items i left join history_visits v on i.id = v.history_item order by date desc;
```

sqliteを抜けると現在のディレクトリにresult.csvというファイルができています。



以上です。
