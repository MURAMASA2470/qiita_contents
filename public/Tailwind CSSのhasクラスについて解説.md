---
title: Tailwind CSSのhasクラスについて解説
tags:
  - 'tailwindcss'
  - 'CSS'
  - '新機能'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Tailwind CSS v3.4より、`:has()`疑似クラスが使用できるようになりました。
今回は追加された3つのクラスの紹介と、それぞれをCSSとTailwind CSSの両者で書いてみたいと思います。

https://tailwindcss.com/blog/tailwindcss-v3-4#new-has-variant


## :has()疑似クラスとは

> :has() は CSS の疑似クラス関数で、引数として渡される相対セレクターのいずれかが、
その要素から辿ってアンカーとして少なくとも一つの要素とマッチする場合に、その要素を表します。
この疑似クラスは、相対セレクターリストを引数として取ることで、参照している要素に関して親要素や前の兄弟要素を選択する方法を提供します。

https://developer.mozilla.org/ja/docs/Web/CSS/:has

### CSSでの使用方法

以下の指定で、子要素に`child`クラスを持っている`parent`クラスの要素だけ対象になります。

```css
.parent:has(.child) {

}
```

```html
<div class="parent">
  <div class="baby"></div>
</div>
<div class="parent">
  <!-- ↑この要素だけ対象になる↑ -->
  <div class="child"></div>
</div>
<div class="parent">
  <div class="baby"></div>
</div>
```

https://tailwindcss.com/blog/tailwindcss-v3-4#new-has-variant


## まずは普通の書き方(`has-[]`)

`has-[xxx]`を指定した要素の子要素に`xxx`が存在する場合のみ、スタイルが適応されます。

以下の例では、ダイアログのOKボタンを押下したら、背景色が赤色になるサンプルです。
※ちょっとチカチカしますね、、すみません、、、😢

![Animation-2.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/c4c8bde1-cb8e-602d-3418-836556d5b0d7.gif)

### CSSの場合

```css
div {
  background-color: red;
}

div:has(dialog[open]) {
  background-color: white;
}
```

### Tailwind CSSの場合

`div`要素に`bg-red-300 has-[dialog[open]]:bg-white`を指定します。

```html
<div class="w-screen h-screen flex items-center bg-red-300 has-[dialog[open]]:bg-white">

  <dialog open class="w-1/6 p-5 border-4">
    <p class="mb-2 text-center">Dialog</p>
    <form method="dialog">
      <button class="bg-blue-500 text-white font-bold p-3 w-full">OK</button>
    </form>
  </dialog>

</div>
```


## 親要素の配下に特定の要素が含まれる場合の書き方(`group-has-[]`)

こちら少々簡潔な表現が難しいです。
`group-has-[xxx]`を指定した要素の親要素に`group`クラスが存在する場合、その親要素の配下に`xxx`が存在する場合にのみ、スタイルが適応されます。

以下の例では、一覧の中にリンク付きの項目が存在する場合、テキストの右に「🔗」アイコンが表示されるサンプルです。

![image-2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/8d7e62ca-f5b4-4d63-20f5-4623a370f070.png)

### CSSの場合

```css
i {
  display: hidden;
}

i:has(> li > a) {
  display: inline;
}
```

### Tailwind CSSの場合

`i`要素に`hidden group-has-[a]:inline`を指定する。

```html
<div class="w-screen h-screen flex items-center">

  <dialog open class="w-1/6 p-5 border-4">
    <ul class="block text-left my-3">
      <li class="group">
        <a href="">Link Text</a>
        <i class="hidden group-has-[a]:inline">🔗</i>
      </li>
      <li class="group">
        <span>Plain Text</span>
        <i class="hidden group-has-[a]:inline">🔗</i>
      </li>
      <li class="group">
        <a href="">Link Text</a>
        <i class="hidden group-has-[a]:inline">🔗</i>
      </li>
      <li class="group">
        <span>Plain Text</span>
        <i class="hidden group-has-[a]:inline">🔗</i>
      </li>
      <li class="group">
        <span>Plain Text</span>
        <i class="hidden group-has-[a]:inline">🔗</i>
      </li>
    </ul>
    <form method="dialog">
      <button class="bg-blue-500 text-white font-bold p-3 w-full">OK</button>
    </form>
  </dialog>

</div>
```


### 兄弟要素に特定の要素が含まれる場合の書き方(`peer-has-[]`)


`peer-has-[xxx]`を指定した要素の兄弟要素に`peer`クラスが存在する場合、その兄弟要素の配下に`xxx`が存在する場合にのみ、スタイルが適応されます。

以下の例では、チェックボックスがチェック状態になると、'This Item Checked'と表示するサンプルです。

![Animation-1.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/a5583724-7910-cb8a-2d7e-2f9884e940ca.gif)

### CSSの場合

```css
p {
  display: hidden;
}

i:has(+ li > [checked]) {
  display: inline-block;
}
```

### Tailwind CSSの場合

```html
<div class="w-screen h-screen flex items-center">

  <dialog open class="w-1/6 p-5 border-4">
    <ul class="block text-left my-3">
      <li class="peer">
        <input type="checkbox">
        <span>Checkable Item</span>
      </li>
      <p class="hidden mb-2 opacity-60 peer-has-[:checked]:inline-block">This Item Checked</p>
    </ul>
    <form method="dialog">
      <button class="bg-blue-500 text-white font-bold p-3 w-full">OK</button>
    </form>
  </dialog>

</div>
```



# 参考

https://developer.mozilla.org/ja/docs/Web/CSS/:has

https://tailwindcss.com/docs/hover-focus-and-other-states#styling-based-on-descendants

https://tailwindcss.com/blog/tailwindcss-v3-4#new-has-variant
