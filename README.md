# qiita_contents

## 使い方

```bash
# パッケージの復元
npm install

# プレビュー画面の起動
npx qiita preview
```

## Qiita CLI のログイン

```bash
# Qiitaトークンが必要
npx qiita login
```
詳細な内容は[こちら](https://github.com/increments/qiita-cli)から確認

## Qiitaとの記事を取り込む

```bash
npx qiita pull
```

## 記事の作成

```bash
# ファイル拡張子は「md」
npx qiita new '記事のファイル名'
```

## 記事の投稿・更新

```bash
# 特定の記事だけ公開
npx qiita publish '記事のファイル名'

# 全ての記事を公開
npx qiita publish --all
```
※上記`publish`コマンドは使用せず、`main`ブランチにPUSHすると自動的にQitiaへ反映される運用とする

## 記事内のヘッダー

```bash
---
title: newArticle001 # 記事のタイトル
tags:
  - "" # タグ（ブロックスタイルで複数タグを追加できます）
private: false # true: 限定共有記事 / false: 公開記事
updated_at: "" # 記事を投稿した際に自動的に記事の更新日時に変わります
id: null # 記事を投稿した際に自動的に記事のUUIDに変わります
organization_url_name: null # 関連付けるOrganizationのURL名
slide: false # true: スライドモードON / false: スライドモードOFF
ignorePublish: false # true: `publish`コマンドにおいて無視されます（Qiitaに投稿されません） / false: `publish`コマンドで処理されます（Qiitaに投稿されます）
---
```

## Qiita CLI をアップデートする

```bash
npm install @qiita/qiita-cli@latest
```