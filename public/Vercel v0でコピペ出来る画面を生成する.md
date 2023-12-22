---
title: Vercel v0でコピペ出来る画面を生成する
tags:
  - React
  - Vercel
  - 生成AI
  - v0
private: false
updated_at: '2023-12-23T06:23:09+09:00'
id: 86864b8b324f854fd068
organization_url_name: null
slide: false
ignorePublish: false
---

## v0とは

AIを活用したVercelの生成UIシステムです。
`shadcn/ui`と`Tailwind CSS`を使用し、Reactベースのコードが生成されます。
生成されたコードは、各プロジェクトでそのまま利用できます。

公式によると、今後は`React`や`Tailwind CSS`だけに限らず、`Svelte`や`Vue`など他のUIフレームワークを追加される可能性があるとのことです。
2023/12時点ではまだBeta版の為、精度や柔軟性はあまり高くなく、データフェッチなどもサポートされていませんが、絶賛開発中とのことなので今後に期待できるかもしれません。

https://v0.dev/faq



## 実際に触ってみる

### 1. プロンプト

```
商品詳細のページを生成してください。
項目は、商品ID,商品名,金額,在庫数,備考 です
```

### 1. 生成されたもの

プロンプトで指定した通り、商品ID、商品名、金額、在庫数、備考 の5つの項目を持つ画面が生成されました。
ボタンが端によっていたり、まだレイアウトは微妙な感じです。
項目名も日本語で指示しましたが、英語になっています。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/ca092e30-19f8-1bca-aad5-3bc2edac076a.png)

初回のプロンプトでは、1つの指示で3パターンほどデザインを生成してくれるようです。
好きなデザインパターンを選択して次のプロンプトを入力すると、そのパターンをベースに修正を加えていきます。

![image-1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/e41fbbb6-e480-9040-a1f0-09558de0e6c8.png)

### 2. プロンプト

2回目以降のプロンプトは、画面下部の黒いテキストボックスから改善修正指示を行えます。

```
登録画面を表示して
```

### 2. 生成されたもの

項目はそのままで、登録画面のようにしてもらいました。
テキストだけだったページが、テキストボックスに置き換わりました。

![image-2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/318a656a-0744-ed43-7129-116dd49430df.png)

### 3. プロンプト

項目を指定して、テキストボックスの入力タイプを変えてもらいましょう。

```
Product IDは数字のみ入力できるようにしてください
```

### 3. 生成されたもの

テキストボックスの`type`属性が`number`になりました。
項目名を指定することにより、修正する場所を識別出来るようです。

![image-3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/c59eedf4-a097-9e53-ddb4-121547ed8b91.png)

### 4. プロンプト

ボタンの位置が気に食わないので、テキストボックスの下に移動してもらいましょう。

```
登録ボタンは入力フィールドの下に配置して
```

### 4. 生成されたもの

ボタンサイズとレイアウトはそのままですが、左端によってくれました。

![image-4.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/60c08c3a-8604-7543-3eb2-763d1833e379.png)

### 5. プロンプト

まだ気に食わないので、ボタンを横並びにしてもらいましょう。

```
Register ProductボタンとClear Fieldsボタンは横並びにして
```

### 5. 生成されたもの

いい感じに横並びになりました。

![image-5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/bbaa46fe-23f3-7ad4-c844-6e43995f4aab.png)

### 6. プロンプト

最後に、項目名を日本語にしてもらいましょう。

```
各項目の名称を日本語にして
```

### 6. 生成されたもの

項目名だけでなく、ボタンのテキストやプレースホルダまで日本語にしてくれました。

![image-6.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/3bbcc68c-8b36-9750-4fe2-316eb45a3967.png)


## 実際に生成されたコード

ReactのJSXと、静的なHTMLの2種類を出力することが出来ます。

### Reactのコード

```jsx:index.js
/**
 * v0 by Vercel.
 * @see https://v0.dev/t/nZylkpRBj8K
 */
import { Button } from "@/components/ui/button"

export default function Component() {
  return (
    <div className="flex flex-col md:flex-row justify-between items-start md:items-center space-y-6 md:space-y-0 md:space-x-6 bg-white dark:bg-gray-800 shadow rounded p-6">
      <div className="space-y-4">
        <h1 className="text-2xl md:text-3xl font-semibold">製品登録</h1>
        <div className="flex flex-col sm:flex-row sm:space-x-4 items-start">
          <div className="flex flex-col space-y-2">
            <h3 className="text-lg font-medium">製品ID</h3>
            <input className="border rounded p-2 text-gray-500" placeholder="1234567890" type="number" />
          </div>
          <div className="flex flex-col space-y-2">
            <h3 className="text-lg font-medium">製品名</h3>
            <input className="border rounded p-2 text-gray-500" placeholder="例：製品" />
          </div>
        </div>
        <div className="flex flex-col space-y-2">
          <h3 className="text-lg font-medium">価格</h3>
          <input className="border rounded p-2 text-gray-500" placeholder="$99.99" />
        </div>
        <div className="flex flex-col space-y-2">
          <h3 className="text-lg font-medium">在庫数量</h3>
          <input className="border rounded p-2 text-gray-500" placeholder="200" />
        </div>
        <div className="flex flex-col space-y-2">
          <h3 className="text-lg font-medium">備考</h3>
          <textarea className="border rounded p-2 text-gray-500" placeholder="これは製品のサンプルノートです。" />
        </div>
        <div className="flex space-x-4">
          <Button className="w-full" size="lg">
            製品を登録する
          </Button>
          <Button className="w-full" variant="outline">
            フィールドをクリア
          </Button>
        </div>
      </div>
    </div>
  )
}
```

### HTMLのコード

```html:index.html
<!--
// v0 by Vercel.
// https://v0.dev/t/nZylkpRBj8K
-->

<div class="flex flex-col md:flex-row justify-between items-start md:items-center space-y-6 md:space-y-0 md:space-x-6 bg-white dark:bg-gray-800 shadow rounded p-6">
  <div class="space-y-4">
    <h1 class="text-2xl md:text-3xl font-semibold">製品登録</h1>
    <div class="flex flex-col sm:flex-row sm:space-x-4 items-start">
      <div class="flex flex-col space-y-2">
        <h3 class="text-lg font-medium">製品ID</h3>
        <input type="number" class="border rounded p-2 text-gray-500" placeholder="1234567890" />
      </div>
      <div class="flex flex-col space-y-2">
        <h3 class="text-lg font-medium">製品名</h3>
        <input class="border rounded p-2 text-gray-500" placeholder="例：製品" />
      </div>
    </div>
    <div class="flex flex-col space-y-2">
      <h3 class="text-lg font-medium">価格</h3>
      <input class="border rounded p-2 text-gray-500" placeholder="$99.99" />
    </div>
    <div class="flex flex-col space-y-2">
      <h3 class="text-lg font-medium">在庫数量</h3>
      <input class="border rounded p-2 text-gray-500" placeholder="200" />
    </div>
    <div class="flex flex-col space-y-2">
      <h3 class="text-lg font-medium">備考</h3>
      <textarea class="border rounded p-2 text-gray-500" placeholder="これは製品のサンプルノートです。"></textarea>
    </div>
    <div class="flex space-x-4">
      <button class="inline-flex items-center justify-center text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 bg-primary text-primary-foreground hover:bg-primary/90 h-11 rounded-md px-8 w-full">
        製品を登録する
      </button>
      <button class="inline-flex items-center justify-center rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 border border-input bg-background hover:bg-accent hover:text-accent-foreground h-10 px-4 py-2 w-full">
        フィールドをクリア
      </button>
    </div>
  </div>
</div>
```

## おわりに

今回は細かい指示を何回かに分けて生成しましたが、設計書などをテキスト化して与えれば、いい感じに少ない試行回数で出来そうです。


## 参考

https://v0.dev
