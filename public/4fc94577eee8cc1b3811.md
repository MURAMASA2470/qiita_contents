---
title: bootstrap-material-designを使った時のコンパイルエラー
tags:
  - PHP
  - Bootstrap
  - npm
  - Laravel
  - YARN
private: false
updated_at: '2019-03-28T17:37:48+09:00'
id: 4fc94577eee8cc1b3811
organization_url_name: null
slide: false
ignorePublish: false
---
## 環境

### 動作環境

- Mac
- PHP 5.6.30
- Laravel 5.4
- npm 6.8.0
- yarn 1.13.0

### `composer.json`の中身↓

| パッケージ | バージョン |
|:-------|:--------|
|bootstrap| ^4.1.1|
|bootstrap-material-design | ^4.1.1|
|jquery | ^3.2.1|

この環境下で`npm run watch-poll`するとビルドエラーが発生する

どうやらBootstrapのバグのよう(たぶん)

## 解決方法

```file
./node_modules/bootstrap-material-design/scss/mixins/_forms.scss
```

このファイルの159行目, 下記の行を次のように置換する

```_forms.scss
@include form-validation-state("valid", $label-color);
```

↓︎

```_forms.scss
@include form-validation-state("valid", $label-color, $form-feedback-icon-valid);
```

プロジェクトのルートディレクトリで下記コマンド実行

```terminal
grep -l '@include form-validation-state("valid", $label-color);' ./node_modules/bootstrap-material-design/scss/mixins/_forms.scss | xargs sed -i.bak -e 's/@include form-validation-state("valid", $label-color);/@include form-validation-state("valid", $label-color, $form-feedback-icon-valid);/g'
```

