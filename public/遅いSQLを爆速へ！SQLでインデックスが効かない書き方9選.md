---
title: 遅いSQLを爆速へ！SQLでインデックスが効きづらい書き方9選
tags:
  - SQL
  - MySQL
  - PostgreSQL
  - SQLServer
  - oracle
private: false
updated_at: '2023-12-18T11:05:25+09:00'
id: d06cb3b6ad6b43b12a4b
organization_url_name: null
slide: false
ignorePublish: false
---

今回は一般的にインデックスが効かないとされる書き方を9つ紹介します。
それぞれのデータベースの拡張機能やバージョンアップ等により、今回紹介する事例でもインデックスが効く可能性はありますが、
「インデックスが効かないパターンってなんだっけ、、」や「よく分からないけどSQLの速度が遅い！」と言う際に、参考にしてみてください。

## 1.LIKE文での中間一致・後方一致を使用する場合

LIKE文で中間一致(%keyword%)や後方一致(keyword%)を使用するとインデックスが効きかない場合があります。
理由を調べたところ、データベースのB-treeインデックスが前方一致検索に最適化されている為のようです。

https://qiita.com/suzukito/items/908a3523f4b90dd77c6a

### 実際の例

```sql
-- インデックスが効かない例
SELECT * FROM users WHERE name LIKE '%田中%';
SELECT * FROM users WHERE name LIKE '%田中';
```

### 回避策

回避策としては、前方一致検索を使用します。

```sql
-- インデックスを効くようにする例
SELECT * FROM users WHERE name LIKE '田中%';
```


## 2.インデックスカラムを関数や演算子で加工する場合

インデックスカラムを関数や演算子で加工するとインデックスが効きかない場合があります。

### 実際の例

以下の例では、`YEAR()`関数が`order_date`に適用されており、インデックスカラムを加工したことになります。

```sql
-- インデックスが効かない例
SELECT * FROM orders WHERE YEAR(order_date) = 2022;
```

### 3.回避策

関数の使用を避けて、元のカラムで条件を指定することで、インデックスが効率よく効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM orders WHERE order_date >= '2022-01-01' AND order_date <= '2023-12-31';
```


## 4.OR条件で列を検索する場合

OR条件で複数の列を検索すると、インデックスが効きかない場合があります。

### 実際の例

```sql
-- インデックスが効かない例
SELECT * FROM products WHERE category = 'books' OR price > 1000;
```

### 回避策

AND条件で条件に変更することや、`UNION句`などで結果を繋げるとインデックスが効率よく効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM products WHERE category = 'books' AND price > 1000;

-- または

SELECT * FROM products WHERE category = 'books'
UNION ALL
SELECT * FROM products WHERE price > 1000;
```



## 5.NULL値のカラムを検索する場合

NULL値を含むカラムを比較する場合、インデックスが効きかない場合があります。

### 実際の例

```sql
-- インデックスが効かない例
SELECT * FROM customers WHERE email IS NULL;
```

### 回避策

カラムに非NULL制約をつけ、デフォルト値にブランクを付けるなどして対応します。

```sql
-- インデックスを効くようにする例
COLUMN_NAME VARCHAR(50) NOT NULL DEFAULT ''
```



## 6.条件に否定文を使用する場合

否定文やNOT演算子を使用すると、インデックスが効きかない場合があります。

### 実際の例

```sql
-- インデックスが効かない例
SELECT * FROM customers WHERE name NOT IN('田中', '佐々木');
SELECT * FROM customers WHERE name IS NOT NULL;
SELECT * FROM customers WHERE name <> '田中';
```

### 回避策

否定文やNOT演算子を使用せず、等価条件を使用することで、インデックス効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM customers WHERE name IN('田中', '佐々木');
SELECT * FROM customers WHERE name = '田中';
```



## 7.データ型が異なる値と比較する場合

異なるデータ型を比較すると、DBによっては暗黙的に型変換がされる為エラーにはなりませんが、インデックスが効きかない場合があります。

### 実際の例

```sql
/* price: INT */

-- インデックスが効かない例
SELECT * FROM products WHERE price = '100';
```

### 回避策

データ型を一致させることで、インデックスが効率よく効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM products WHERE price = 100;
```



## 8.実テーブルではなくWITH句や副問い合わせで取得した仮想テーブルを参照する場合

WITH句や副問い合わせで取得したテーブルを参照する場合、元のテーブルはインデックスが効いていたとしても、取得した結果の仮想テーブルではインデックスが効きかない場合があります。

### 実際の例

```sql
-- インデックスが効かない例
WITH recent_orders AS (
    SELECT * FROM orders WHERE order_date > '2022-01-01'
)
SELECT * FROM recent_orders WHERE product_id = 100;
```

### 回避策

仮想テーブルではなく、実テーブルで条件を指定することで、インデックスが効率よく効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM orders WHERE order_date > '2022-01-01' AND product_id = 100;
```



## 9.作成したインデックスの順番と異なる場合

作成したインデックスの順番と異なる順序で条件を指定すると、インデックスが効きかない場合があります。

### 実際の例

```sql
-- 1:category, 2:item_nameの順番でインデックスを作成
CREATE INDEX category_item_name_index ON products(category, item_name);
```

```sql
-- インデックスが効かない例
SELECT * FROM products WHERE item_name = 'ABC book' AND category = 'books';
```

### 回避策

条件の順序をインデックスに合わせることで、インデックスが効率よく効くようになります。

```sql
-- インデックスを効くようにする例
SELECT * FROM products WHERE category = 'books' AND item_name = 'ABC book';
```

