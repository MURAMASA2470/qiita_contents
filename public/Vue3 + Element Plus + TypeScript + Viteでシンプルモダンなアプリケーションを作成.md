---
title: Vue3 + Element Plus + TypeScript + Viteでシンプルモダンなアプリケーションを作成
tags:
  - Vue.js
  - Vue3
  - ElementPlus
  - Vite
  - TypeScript
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## やりたいこと

VueのUIフレームワークである`Element Plus`を試す。
環境構築から、軽い画面作成まで行う。

## Element UI と Element Plus の違い

`Element UI` と `Element Plus` は、どちらも Vue.js のUIライブラリで、同じELEMEのチームによって開発・管理されています。
`Element UI`はVue 2に対応しており、Vue 3には対応していませんが`Element Plus`はVue 3 に対応しています。
`Element UI`はメンテナンスモードに入り、新機能やアップデートの提供は低頻度になっています。

## 環境

- Vue3
- Vite
- TypeScript
- Element Plus

## インストール

viteで新規プロジェクトを作成します。
実行すると対話形式で「プロジェクト名」「使用するフレームワーク」「言語」を聞かれるので、適当に解答します。
今回はフレームワークは`Vue`、言語は`TypeScript`とします。

### プロジェクトの作成

```bash
npm create vite@latest
```

### 一旦 起動確認

以下のコマンドで起動確認を行います。

```bash
cd プロジェクト名

npm install

npm run dev
```

このViteの画面が表示されたら、ここまではOKです。

![image-3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/8fb28025-6b73-fb17-dca8-75d849011062.png)

### Element Plus のインストール

次は本命の`Element Plus`をインストールしていきます。

```bash
npm install element-plus --save
```

次に`main.ts`と`App.vue`の中身を以下のように書き換えます。

```ts:main.ts
import { createApp } from 'vue'
import './style.css'

import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus)
app.mount('#app')
```

```vue:App.vue
<template>
  <el-button type="primary">ボタン</el-button>
</template>
```

以下のように表示されれば、`Element Plus`は無事に動いています。

![image-4.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/9ad453c6-0f31-654c-fb5f-e1bde5aed223.png)


## コンポーネントを適当に配置してみる

ここではソースは割愛しますが、公式のコンポーネントリファレンスを参考に、簡易的なレイアウト作成し、カードやテキストボックスなどを配置してみました。


https://element-plus.org/en-US/component/button.html

![image-2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/0514314d-a069-a4ba-e261-c7ba0fa479ae.png)

### 成果物

今回作ったものは以下のリポジトリにアップしてあります。

https://github.com/MURAMASA2470/vue3-vite-typescript-element_plus

## 参考

https://qiita.com/genenao21/items/95b99f452dcce5c7d706


