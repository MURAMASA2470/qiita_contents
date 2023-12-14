---
title: Chrome 120で追加されたCSSの新機能を紹介
tags:
  - CSS
  - CSS3
  - Chrome
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---


## カスタムプロパティのsyntaxの追加(`<image>`)

`@property`や`registerProperty()`で登録したカスタムプロパティのsyntaxに`<image>`が使用できるようになりました。
`<image>`を使用すると、カスタムプロパティの値を url() の値と、グラデーションなどの生成される画像に制限できます。

### 書き方

```css
@property --property-name {
  syntax: "<image>";
  inherits: false;
  initial-value: #55c500;
}
```

`<image>`は以下の関数やデータ型を表現可能です。

- `url()` 画像
- `<gradient>` データ型
  - `linear-gradient()` 線形グラデーション
  - `radial-gradient()` 放射グラデーション
  - `repeating-linear-gradient()` 反復線形グラデーション
  - `repeating-radial-gradient()` 反復放射グラデーション
  - `conic-gradient()` 扇形グラデーション
- `element()` ウェブページの一部
- `image()` 画像、画像フラグメント、単色の色
- `cross-fade()`  2つ以上の画像の合成
- `image-set()` 、解像度に基づいて定義された画像の選択

https://developer.mozilla.org/ja/docs/Web/CSS/image

https://developer.mozilla.org/ja/docs/Web/CSS/gradient



## カスタムプロパティのsyntaxの追加(`<transform-function>`及び`<transform-list>`)

`<transform-function>`及び`<transform-list>`を使用すると、カスタムプロパティ上で、遷移やアニメーションを直接使用することもできます。

以下の形式構文を確認すると、`<transform-function>`と`<transform-list>`は現状同じ意味を示すようです。
```
transform =
  none              |
  <transform-list>

<transform-list> =
  <transform-function>+
```

### 書き方

```css
@property --property-name {
  syntax: "<transform-function>";
  inherits: false;
  initial-value: #55c500;
}
```

`<transform-function>`及び`<transform-list>`は以下の関数やデータ型を表現可能です。

- `matrix()` 二次元の同次変換行列を記述します
- `matrix3d()` 三次元の変換を4×4の同次行列で記述します
- `perspective()` ユーザーと z=0 平面との間の距離を設定します
- `rotate()` 要素を二次元平面上で特定の点を中心に回転します
- `rotate3d()` 要素を三次元空間で特定の軸を中心に回転します
- `rotateX()` 要素を水平軸を中心に回転します
- `rotateY()` 要素を垂直軸を中心に回転します
- `rotateZ()` 要素を Z 軸を中心に回転します
- `scale()` 要素を二次元平面上で拡大または縮小します
- `scale3d()` 要素を三次元空間で拡大または縮小します
- `scaleX()` 要素を水平に拡大または縮小します
- `scaleY()` 要素を垂直に拡大または縮小します
- `scaleZ()` 要素を Z 軸方向に拡大または縮小します
- `skew()` 要素を二次元平面上で歪ませます
- `skewX()` 要素を水平方向に歪ませます
- `skewY()` 要素を垂直方向に歪ませます
- `translate()` 要素を二次元平面上で平行移動させます
- `translate3d()` 要素を三次元空間で平行移動させます
- `translateX()` 要素を水平方向に平行移動させます
- `translateY()` 要素を垂直方向に平行移動させます
- `translateZ()` 要素を Z 軸方向に平行移動させます


https://developer.mozilla.org/ja/docs/Web/CSS/transform-function


## メディアクエリ スクリプト機能の追加

javascriptなどのスクリプト言語が、ブラウザでサポートされているかどうかを確認するために使用します。
有効なオプションは、以下の3種類です。
- `enabled` スクリプトが利用できる状態で有効です
- `initial-only` スクリプトが利用できる状態のページロード時(`loded`イベント発生時)に有効です
- `none` スクリプトが利用できない状態で有効です
※`initial-only`はページロード完了のタイミングは`DOMContentLoaded`イベントの発生タイミングとは異なります。

### 使い方

```css
.js-disabled {
  display: none;
}

@media (scripting: none) {
  .js-disabled {
      /* ここにスクリプトが利用できない場合のCSSを記載 */
      display: block;
  }
}
```

従来の`<noscript>`タグの自由度が上がった感じの機能となっています。


## `:dir()`疑似セレクタの追加


`:dir`疑似セレクタは、要素のテキストが特定の方向（LTR＝左から右、RTL＝右から左）に書かれているかどうかに基づいてスタイルを適用するためのCSSの擬似セレクタです。

主に多言語対応のWebサイトで、異なる言語のテキストが混在している場合に便利です。
例えば、日本語（左から右）とアラビア語（右から左）のような複数の言語が同じページに表示される場合などに使用します。

### 使い方

```css
:dir(ltr) {
  /* 左から右の書き方の要素に適用されるスタイル */
}

:dir(rtl) {
  /* 右から左の書き方の要素に適用されるスタイル */
}
```

## CSS指数関数の追加

以下の4つのCSS指数関数が追加されました。
- `pow()` べき乗
- `sqrt()` 平方根
- `hypot()` ユークリッド距離の計算
- `log()` 自然対数関数
- `exp()` 指数関数

これらの関数は`calc()`関数内でのみ使用可能です。

### 使い方

```scss
.calc {
  // pow() べき乗
  width: calc(pow(2, 3) * 10px); /* 2の3乗を計算して10pxに乗じる */

  // sqrt() 平方根
  width: calc(sqrt(25) * 2); /* 25の平方根を計算して2倍する */

  // hypot() ユークリッド距離の計算
  width: calc(hypot(3, 4) * 10px); /* 3と4のユークリッド距離を計算して10pxに乗じる */

  // log() 自然対数関数
  width: calc(log(8) * 10px); /* 8の自然対数を計算して10pxに乗じる */

  // exp() 指数関数
  width: calc(exp(2) * 10px); /* eの2乗を計算して10pxに乗じる */
}
```

## CSSマスキングのベンダープレフィックスの解除

マスキング関連のCSSに、ベンダープレフィックス(`-webkit-mask*`)の付与が不要になりました。
以下のプロパティが対象です。
- `mask-image` マスクレイヤーとして使用する画像の指定
- `mask-repeat` マスク画像の繰り返しの設定（マスク画像の敷き詰め方法）
- `mask-position` マスク画像の位置の設定
- `mask-size` マスク画像の大きさの設定
- `mask-composite` マスクの合成操作（複数のマスク画像の組合せ）
- `mask-mode` mask-image で定義されたマスクを輝度マスクとアルファマスクのどちらで扱うかを設定
- `mask-origin` マスクの基準位置を設定
- `mask` 上記のプロパティを一括指定できる省略形


## CSSネストの緩和

素のCSSでネストを行う際に`&`記号や`:is()`を使用することなく、要素のネストが可能になりました。
以下の記事にもあるように、従来はSCSSのネストとは少し違ったクセのある挙動をしていました。

https://qiita.com/amiami/items/54c4fa66edbbbeaa2b08

### 使い方

```scss
/* ～Chrome 119 */
.card {
  & h1 {}
}

/* Chrome 120～ */
.card {
  h1 {}
}
```

# 参考

https://developer.chrome.com/blog/chrome-120-beta?hl=ja

https://developer.chrome.com/blog/new-in-chrome-120?hl=ja

https://www.w3.org/TR/css-values-4/