---
title: 'Microsoft Edgeの閲覧履歴をエクスポートする(Windows, Mac対応)'
tags:
  - SQLite3
  - Edge
  - history
  - 履歴
private: false
updated_at: '2023-12-23T00:37:50+09:00'
id: 35cf540cf44a3c1617ab
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

インターネットブラウザの閲覧履歴を見返していると自分の人生を振り返ってるみたいで楽しいですよね。
私の場合はChromeの履歴が2013年からのものが残っていましたが、
それ以前はIEやFireFoxなどを使っていたので残念ながら残っていませんでした。


他のブラウザについては以下の記事でまとめています。
- Google Chrome

https://qiita.com/MURAMASA2470/items/d081e71d79c4dc36671e

- Apple Safari

https://qiita.com/MURAMASA2470/items/c29a9209c0d80956d913

## OS毎の履歴データの保存場所

履歴データは以下のディレクトリにあります

```
// Windowsの場合
~\AppData\Local\Microsoft\Edge\User Data\Default\

// Macの場合
~/Library/Application\ Support/Microsoft/Edge/Default/
```

この中に`History`というファイルがあるので、それがEdgeの履歴データになります

## sqlite3のインストール

履歴データの閲覧には、`sqlite3`が必要です。
まだインストールされていない方は、以下の記事を参考にインストールしてください。

https://neko-py.com/sqlite-install

## 履歴データの中身を確認

それでは以下のように進めていきます

```
// Windowsの場合
> cd '~\AppData\Local\Microsoft\Edge\User Data\Default\'

// Macの場合
> cd ~/Library/Application\ Support/Microsoft/Edge/Default/

// sqliteの中に入る
> sqlite3 History

// テーブル一覧の表示
sqlite> .tables
```

`History`の中には以下のようなテーブルがあります

| テーブル名 |
|:--|
| downloads |
| download_url_chains |
| meta |
| urls |
| keyword_search_terms |
| segments |
| segment_usage |
| visits |
| visit_source |

今回は訪問時間も欲しいので`urls`と`visits`を結合して使います
`urls`と`visits`の構造は以下のようになっています

| urls | visits |
|:----|:----|
| id | id |
| url | url |
| title | visit_time |
| visit_count | from_visit |
| typed_count | transition |
| last_visit_time | segment_id |
| hidden | visit_duration |
| favicon_id | |

## 履歴データを抽出してみる

今回は`csv`形式で出力しますが、他にも以下のようなものもあるそうです

|mode  |説明   |
|:-----|:-----|
|csv      |カンマ区切りで出力|
|column   |カラム毎に左揃えで出力|
|html     |HTMLのTABLE形式で出力|
|insert   |INSERT文として出力|
|line     |各カラム毎に行を分けて出力|
|list     |区切り文字でカラムを区切り1行で出力(デフォルト)|
|tabs     |タブ区切りで出力|
|tcl      |TCLのlist形式で出力|

それでは実際に出力していきます
※`visit_time`がUNIXタイムもどき(1601/01/01 0:00 からのマイクロ秒)になっているので一般的な形式に変換しています

```
// .mode [モード名] でモード指定
sqlite> .mode csv

// .output [ファイル名].csv でファイル名指定
// 今回はユーザーのホームディレクトリに保存
sqlite> .output ~/result.csv

// ヘッダーをつける
sqlite> .headers on

// 抽出
sqlite> select urls.id, datetime(visits.visit_time/1000000-11644473600,'unixepoch','localtime') as visit_time, urls.title, urls.url from visits left join urls on visits.url = urls.id order by visits.id desc;
```
抽出で使用したSQLの内容は以下のようになっています

```sql
select 
 urls.id,
 datetime(visits.visit_time/1000000-11644473600,'unixepoch','localtime') as visit_time, 
 urls.title, 
 urls.url 
from visits 
left join urls 
 on visits.url = urls.id 
order by visits.id desc;
```

sqliteを抜けるとホームディレクトリに`result.csv`というファイルができています。

もし`Error: database is locked`と出たら一度Google Chromeを終了してから、もう一度試す、
もしくは、Historyファイルを別名でコピーしたものに対して実施してみてください。

以上です。

