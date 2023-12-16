---
title: GatsbyでMaterial-UIを適応した際にスタイルが崩れてしまう問題
tags:
  - React
  - material-ui
  - gatsby
private: false
updated_at: '2020-08-16T00:05:06+09:00'
id: d7fb6366c2f8b6be6d91
organization_url_name: null
slide: false
ignorePublish: false
---
自分用メモ

CRA(create-react-app)で作成したものをGatsbyに移行した際に， Material UIのスタイルが崩れてしまう問題に遭遇したときに対処した方法


GithubのMateril-UIのissueにありました。
> Gatsby & Material-UI break in production builds #18197
> https://github.com/mui-org/material-ui/issues/18197

## gatsby-plugin-material-uiのインストール

```shell
npm install --save gatsby-plugin-material-ui
```
## gatsby-config.jsに追記
gatsby-config.jsがなければ，トップディレクトリに新規作成

```js:gatsby-config.js
module.exports = {
  plugins: [`gatsby-plugin-material-ui`]
}
```
## 静的ファイル書き出し

```shell
gatsby build
```

以上でスタイルが崩れていた問題を解決できました。
