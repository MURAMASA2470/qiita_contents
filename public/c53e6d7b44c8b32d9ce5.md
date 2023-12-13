---
title: youtube-dlを利用した動画ダウンローダーを作成したので紹介
tags:
  - Python
  - YouTube
  - Python3
  - youtube-dl
private: false
updated_at: '2023-12-10T21:36:23+09:00'
id: c53e6d7b44c8b32d9ce5
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

Pythonの`youtube-dl`というライブラリを使用して、簡単にYoutubeなどの動画や音声をダウンロードおよび変換できるツールを作成したので紹介します。

https://github.com/MURAMASA2470/youtube_downloads

# youtube-dlとは

youtube-dlは、Python製のコマンドラインツールで、様々な動画共有サイトから動画や音声をダウンロードするためのツールです。YouTubeだけでなく、多くのサポートされているサイトからメディアをダウンロードすることができます。

### 公式リポジトリ
https://github.com/ytdl-org/youtube-dl

### サポートされている世界中のメディアサイト
https://ytdl-org.github.io/youtube-dl/supportedsites.html


# 使い方

## 環境

- Python 3.x

Python3以上で動作します。2系では動作しない為、バージョンアップしてください。

## インストール

当ツールでは以下のライブラリを使用します。

- youtube_dl
- ffmpeg
- ffmpeg-python

```bash
python -m pip install youtube_dl ffmpeg ffmpeg-python
```

## 実行

```bash
python ./ydl.py
> フォーマット(mp3またはmp4): "ここに変換したいフォーマットを入力"
> URL: "ここにダウンロードしたいURLを入力"
```

## オプション

```bash
usage: ydl.py [-h] [-f FORMAT] [-u URI] [-l LIST]

  -h, --help           : ヘルプメッセージを表示して終了

  -f, --format Format  : ダウンロードするファイルフォーマットを選択 (mp3またはmp4)。

  -u, --uri URI        : ダウンロードするメディアのURIを指定。

  -l, --list LIST      : URLリストのためのテキストファイルを指定。
                         リスト内のURLから一括でリソースをダウンロード。
```

# ダウンロード速度が遅い場合の対処法

Youtube側のアップデートにより、ダウンロード速度が著しく低下するときがあります。
youtube-dlのリポジトリのリリースされたものでは、更新が追い付かない為、`master`ブランチの最新を取得し、インストールすることで、速度を改善できることがあります。
※それでも改善されない場合は、対応するまで待ちましょう。

## MacおよびLinuxの場合

```bash
# gitから最新のコミットのSHAハッシュを取得

hash=$(git ls-remote https://github.com/ytdl-org/youtube-dl.git | head -n 1 | cut -c 1-40)

wget https://github.com/ytdl-org/youtube-dl/archive/$hash.zip

unzip -n $hash.zip && cd youtube-dl-$hash

python -m pip uninstall -y youtube_dl && python -m pip install .

cd .. && rm -r youtube-dl-$hash

```

## Windowsの場合

```bash
# gitから最新のコミットのSHAハッシュを取得

$hash=$(git ls-remote https://github.com/ytdl-org/youtube-dl.git | Select-Object -First 1).Substring(0, 40)

Invoke-WebRequest -Uri https://github.com/ytdl-org/youtube-dl/archive/$hash.zip -outfile ./youtube-dl-$hash.zip

Expand-Archive -Path .\youtube-dl-$hash.zip -DestinationPath ./ ; cd youtube-dl-$hash

python -m pip uninstall -y youtube_dl ; python -m pip install .

cd .. ; rm -r youtube-dl-$hash


```
