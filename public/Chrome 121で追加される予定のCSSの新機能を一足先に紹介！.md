---
title: Chrome 121で追加される予定のCSSの新機能を一足先に紹介！
tags:
  - CSS
  - CSS3
  - Chrome
private: false
updated_at: '2023-12-18T00:47:51+09:00'
id: cdc5742ef90d7c79a520
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

2024/1/17にリリース予定のChrome 121で追加されるCSSの新機能を一足先に紹介します。
Chrome 121で追加された機能については、こちらに記載していますので良かったら確認してみて下さい！

https://qiita.com/MURAMASA2470/items/9f6da9fc4a6b25d08671

# 追加される機能

## テキストハイライト関連のプロパティ値継承の追加

`::selection`や`::highlight`などハイライト関連のプロパティが子要素へ継承されるようになります。

### 使い方

```css
::selection {
  color: red;
}

::highlight {
  background-color: yellow;
}

```

上記の例では、選択テキスト（::selection）の色が赤くなり、ハイライト（::highlight）の背景色が黄色になります。


## スクロールバーの色を指定できるプロパティの追加

スクロールバーの色や太さを指定する為のプロパティが追加されました。
- `scrollbar-color` スクロールバーの色を変更
- `scrollbar-width` スクロールバーの幅を変更

`scrollbar-color`プロパティには、`color`プロパティで指定可能な値などが指定出来ます。

### 使い方

```css
::-webkit-scrollbar {
  /* スクロールバーの色を指定 */
  scrollbar-color: green;

  /* スクロールバーの幅を指定 */
  scrollbar-width: 6px
}
```


## font-paletteアニメーションの追加

`font-palette`でパレットを切り替える際に、アニメーションをつけることが出来ます。

### 使い方

```scss
/* フォントパレットのアニメーション */
@keyframes change-palette {
  0% {
    font-palette: normal;
  }
  100% {
    font-palette: colorful;
  }
}

/* アニメーションを適用する要素 */
div {
  animation: change-palette 2s infinite alternate;
}

```

## スペルエラーおよび文法エラー時のスタイルの追加

ブラウザが誤りとしてハイライトしたテキストにスタイルを適応するCSSが追加されました。
これにより、開発者はデフォルトのスペルエラーおよび文法エラーに対して、より読みやすい色を指定することが出来ます。

### 使い方

```css
/* スペルエラーを赤くする */
::spelling-error {
  color: red;
}

/* 文法エラーに下線をつける */
::grammar-error {
  text-decoration: underline wavy red;
}
```


## SVG向けのCSSマスキングの追加

Chrome 120でのCSSマスクの拡張に続き、SVG向けの新しいマスクが追加されました。

以下の4つのマスクがサポートされています。
- `mask-repeat` マスク画像の繰り返しの設定（マスク画像の敷き詰め方法）
- `mask-position` マスク画像の位置の設定
- `mask-composite` マスクの合成操作（複数のマスク画像の組合せ）
- `mask-mode` mask-image で定義されたマスクを輝度マスクとアルファマスクのどちらで扱うかを設定

また、マスク画像の参照元をローカルではなくURLを指定して表示できるようになります。

### 使い方

```css
/* SVGに適用される複数のマスク */
svg {
  mask: url("https://example.com/sample.svg");
}
```


## ルビに関するCSSの追加

新しいCSS表示プロパティの値`ruby`と`ruby-text`が追加されました。
これらはそれぞれ、`ruby`は`<ruby>`タグ、`ruby-text`は`<rt>`タグの代替プロパティになります。
専用のタグを使わずとも、`<div>`タグなどにこのプロパティを指定することで、ルビを扱えるようになります。

### 使い方

```css
/* Ruby要素の表示 */
.ruby {
  display: ruby;
}

/* Rubyテキストの表示 */
.ruby-text {
  display: ruby-text;
}
```


# 参考

https://developer.chrome.com/blog/chrome-121-beta?hl=ja
