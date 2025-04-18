---
description: データベースに蓄えられた情報を取り出すには、SELECT 文を使用します。データ検索はデータベース操作の中で最も頻繁に行われる作業です。
---

# 4. 基本的なデータ検索

### 4.1 SELECT 文の基本構文

SELECT の基本的な構文は次のとおりです。

```sql
SELECT カラム1, カラム2, ...
FROM テーブル名
WHERE 条件;
```

* **SELECT**: 取得したいカラム (列) を指定します
* **FROM**: データを取得するテーブルを指定します
* **WHERE**: 特定の条件に一致する行だけを取得します (省略可能)

> 📝 **ポイント**: SQL コマンドは大文字でも小文字でも動作しますが、SQL キーワード (SELECT, FROM, WHEREなど) は大文字、テーブル名やカラム名は小文字で書くのが一般的な慣習です。読みやすさのために統一したスタイルを使いましょう。

### 4.2 データの取得

#### 全ての列を取得する

テーブルの全ての列を取得するには、アスタリスク ( `*` ) を使用します。

```sql
-- カテゴリテーブルからすべての列を取得
SELECT * FROM categories;
```

<details>

<summary>実行結果</summary>

```
+----+----------+--------------------+
| id | name     | description        |
+----+----------+--------------------+
|  1 | 電子機器  | 電子機器とガジェット  |
|  2 | 家具     | テーブル、椅子など     |
|  3 | 書籍     | 書籍、電子書籍、雑誌   |
+----+----------+--------------------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `SELECT *` は簡単ですが、大きなテーブルでは不要なデータも取得するため効率が悪くなります。本番環境では必要なカラムだけを指定するのが良い習慣です。しかし学習段階では、テーブルの全体像を把握するのに役立ちます。

#### 4.2.2 特定の列を取得する

必要な列のみを選択して取得することもできます：

```sql
-- 商品の名前と価格だけを取得
SELECT name, price FROM products;
```

<details>

<summary>実行結果</summary>

```
+----------------------+----------+
| name                 | price    |
+----------------------+----------+
| スマートフォン          | 89000.00 |
| ラップトップPC         |120000.00 |
| コーヒーメーカー        | 15000.00 |
+----------------------+----------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 実務では、必要な列だけを選択することでパフォーマンスが向上します。特に大きなテーブルの場合は重要です。また、列名を指定する順序は任意です - 結果にはその順序で表示されます。

### 4.3 WHERE 句による絞り込み

データベースから特定の条件に一致する行だけを取得したい場合、`WHERE` 句を使用します。

#### 4.3.1 基本的な比較演算子

WHERE 句では以下の比較演算子が使用できます。

<table><thead><tr><th width="180.89483642578125">演算子</th><th>説明</th></tr></thead><tbody><tr><td><code>=</code></td><td>等しい</td></tr><tr><td><code>&#x3C;></code> または <code>!=</code></td><td>等しくない</td></tr><tr><td><code>&#x3C;</code></td><td>より小さい</td></tr><tr><td><code>&#x3C;=</code></td><td>以下</td></tr><tr><td><code>></code></td><td>より大きい</td></tr><tr><td><code>>=</code></td><td>以上</td></tr></tbody></table>

> 📝 **ポイント**: SQL では等価比較に `=` を使います。これは多くのプログラミング言語で使われる `==` と異なるので、混乱しやすいポイントです。`==` は SQL では使えません。

例：

```sql
-- 価格が10,000円以上の商品を取得
SELECT name, price
FROM products
WHERE price >= 10000;
```

<details>

<summary>実行結果</summary>

```
+----------------------+----------+
| name                 | price    |
+----------------------+----------+
| スマートフォン          | 89000.00 |
| ラップトップPC         |120000.00 |
| コーヒーメーカー        | 15000.00 |
+----------------------+----------+
3 rows in set (0.00 sec)
```

</details>

```sql
-- カテゴリIDが1の商品
SELECT name, category_id
FROM products
WHERE category_id = 1;
```

<details>

<summary>実行結果</summary>

```
+--------------------+-------------+
| name               | category_id |
+--------------------+-------------+
| スマートフォン     |           1 |
| ラップトップPC     |           1 |
+--------------------+-------------+
2 rows in set (0.00 sec)
```

</details>

#### 4.3.2 論理演算子

複数の条件を組み合わせるために、論理演算子を使用します：

* **AND**: 両方の条件が真の場合に真
* **OR**: 少なくとも 1つの条件が真の場合に真
* **NOT**: 条件の真偽を反転

> 📝 **ポイント**: 複合条件では、括弧 `()` を使って評価の優先順位を明確にすることが重要です。特に `AND` と `OR` を混在させる場合は必須です。`AND` は `OR` より優先順位が高いです (掛け算と足し算の関係と同じ) 。

```sql
-- カテゴリー ID が 1 かつ価格が 50,000円以上の商品
SELECT name, price, category_id
FROM products
WHERE category_id = 1 AND price >= 50000;
```

<details>

<summary>実行結果</summary>

```
+--------------------+----------+-------------+
| name               | price    | category_id |
+--------------------+----------+-------------+
| スマートフォン     | 89000.00 |           1 |
| ラップトップPC     |120000.00 |           1 |
+--------------------+----------+-------------+
2 rows in set (0.00 sec)
```

</details>

```sql
-- カテゴリー ID が 1 または価格が 10,000 円未満の商品
SELECT name, price, category_id
FROM products
WHERE category_id = 1 OR price < 10000;
```

<details>

<summary>実行結果</summary>

```
+--------------------+----------+-------------+
| name               | price    | category_id |
+--------------------+----------+-------------+
| スマートフォン     | 89000.00 |           1 |
| ラップトップPC     |120000.00 |           1 |
| マウス             |  2000.00 |           1 |
| キーボード         |  6000.00 |           1 |
+--------------------+----------+-------------+
4 rows in set (0.00 sec)
```

</details>

```sql
-- カテゴリー ID が 1 ではない商品
SELECT name, category_id
FROM products
WHERE NOT category_id = 1;
-- または: WHERE category_id <> 1;
```

<details>

<summary>実行結果</summary>

```
+----------------------+-------------+
| name                 | category_id |
+----------------------+-------------+
| コーヒーメーカー     |           2 |
| 木製テーブル         |           2 |
+----------------------+-------------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `NOT` は条件を否定するもので、`NOT A = B` は `A <> B` と同じ意味です。どちらの書き方も可能ですが、`NOT` 複雑な条件の否定に便利です (例：`NOT (A = B OR C = D)` )。

#### 4.3.3 条件演算子 ⭐️

より複雑な条件を指定するために、以下の条件演算子が使えます。

**BETWEEN 演算子**

指定した範囲内の値を持つレコードを取得します。

```sql
-- 価格が 5,000円から 20,000円の商品
SELECT name, price
FROM products
WHERE price BETWEEN 5000 AND 20000;
```

<details>

<summary>実行結果</summary>

```
+----------------------+----------+
| name                 | price    |
+----------------------+----------+
| コーヒーメーカー     | 15000.00 |
| キーボード           |  6000.00 |
+----------------------+----------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `BETWEEN A AND B` は、「A 以上 B 以下」 を意味します。つまり境界値も含みます。「5,000円より大きく 20,000円未満」 のように境界値を含まない範囲を指定したい場合は、`price > 5000 AND price < 20000` というように比較演算子を使います。

**IN 演算子**

指定したリスト内の値を持つレコードを取得します。

```sql
-- カテゴリー ID が 1 または 3 の商品
SELECT name, category_id
FROM products
WHERE category_id IN (1, 3);
```

<details>

<summary>実行結果</summary>

```
+--------------------+-------------+
| name               | category_id |
+--------------------+-------------+
| スマートフォン     |           1 |
| ラップトップPC     |           1 |
| マウス             |           1 |
| キーボード         |           1 |
| プログラミング本   |           3 |
+--------------------+-------------+
5 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `IN` 演算子は、複数の `OR` 条件を簡潔に書くことができます。`category_id = 1 OR category_id = 3 OR category_id = 5` は `category_id IN (1, 3, 5)` と同じです。コードが読みやすくなるだけでなく、実行効率も向上することがあります。

**LIKE** **演算子 ⭐️**

パターンマッチングを使用して文字列を検索します。

* `%` - 0 文字以上の任意の文字列
* `_` - 任意の 1 文字

```sql
-- 名前に「フォン」を含む商品
SELECT name 
FROM products 
WHERE name LIKE '%フォン%';
```

<details>

<summary>実行結果</summary>

```
+--------------------+
| name               |
+--------------------+
| スマートフォン     |
+--------------------+
1 row in set (0.00 sec)
```

</details>

```sql
-- 名前が「マ」で始まる商品
SELECT name 
FROM products 
WHERE name LIKE 'マ%';
```

<details>

<summary>実行結果</summary>

```
+--------+
| name   |
+--------+
| マウス |
+--------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `LIKE` 演算子は便利ですが、大規模なデータベースではパフォーマンスに影響することがあります。特に `'%文字列%'` のように先頭と末尾に `%` を置くパターンは検索が遅くなりがちです。可能であれば、より具体的なパターン ( `'文字列%'` など) を使用すると効率的です。

**IS NULL / IS NOT NULL演算子**

NULL値 (値が存在しない) を持つレコードを検索します。

```sql
-- 説明 (description) が NULL の商品
SELECT name 
FROM products 
WHERE description IS NULL;
```

<details>

<summary>実行結果</summary>

```
+--------------------+
| name               |
+--------------------+
| マウス             |
| キーボード         |
+--------------------+
2 rows in set (0.00 sec)
```

</details>

```sql
-- 説明 (description) が NULL でない商品
SELECT name 
FROM products 
WHERE description IS NOT NULL;
```

<details>

<summary>実行結果</summary>

```
+----------------------+
| name                 |
+----------------------+
| スマートフォン       |
| ラップトップPC       |
| コーヒーメーカー     |
| 木製テーブル         |
| プログラミング本     |
+----------------------+
5 rows in set (0.00 sec)
```

</details>

> ⚠️ **注意**: NULL 値と比較する場合は、必ず `IS NULL` または `IS NOT NULL` を使用してください。`= NULL` や v`!= NULL` では正しく動作しません。これは SQL を学ぶ際の重要なポイントです。

#### 4.3.4 NULL とは

NULLは 「値が存在しない」 または 「値が不明」 であることを表します。これは 「0」 や 「空文字」 とは異なる特別な状態です。

例えば、

* 顧客の電話番号が登録されていない場合 → NULL
* 商品の説明文が書かれていない場合 → NULL
* まだ決定していない出荷日 → NULL

**NULLの重要な特性**:

* NULL は通常の比較演算子 ( =, <, >など) では比較できません
* NULL かどうかを確認するには、`IS NULL` または `IS NOT NULL` を使います
* NULL 同士の比較 (NULL = NULL ) は false になります
* NULL 値を含む計算結果は通常 NULL になります

> 📝 **ポイント**: NULL の扱いは初心者にとって混乱しやすいポイントの一つです。NULL は「値がない」 ことを表し、空文字列 ( '' ) や数値の 0 とは異なります。例えば、電話番号が空文字 ( '' ) の場合は 「電話番号が存在するが中身が空」 を意味し、NULL の場合は 「電話番号自体が存在しない (未登録) 」を意味します。

#### 4.3.5 複合条件による検索 ⭐️

複数の条件を組み合わせた複雑な検索もできます：

```sql
-- カテゴリー ID = 1 の商品で、価格が 5万円以上または在庫が 10個未満のもの
SELECT name, price, stock, category_id
FROM products
WHERE category_id = 1 
  AND (price >= 50000 OR stock < 10);
```

<details>

<summary>実行結果</summary>

```
+--------------------+----------+-------+-------------+
| name               | price    | stock | category_id |
+--------------------+----------+-------+-------------+
| スマートフォン     | 89000.00 |    25 |           1 |
| ラップトップPC     |120000.00 |    15 |           1 |
| キーボード         |  6000.00 |     8 |           1 |
+--------------------+----------+-------+-------------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 複雑な条件では、括弧を使って優先順位を明確にすることが非常に重要です。上記の例では、`AND` が `OR` より優先されるため、意図した結果を得るには括弧が必須です。括弧がないと「カテゴリ ID = 1 AND 価格 ≥ 50000 」 OR 「在庫 < 10」 という条件になってしまいます。

***

### 演習問題

#### 問題 4-1: 基本的な SELECT 文

以下のクエリーを作成・実行してください。

1. 商品テーブルからすべてのデータを取得する
2. 商品の名前と価格だけを取得する
3. 在庫がある商品 (stock > 0) だけを表示する

<details>

<summary>ヒント</summary>

* すべての列を取得するには `SELECT *` を使います

- 特定の列だけを取得するには、列名をカンマ区切りで指定します

* 条件で絞り込むには `WHERE` 句を使います

</details>

<details>

<summary>解答</summary>

```sql
-- 1. 商品テーブルからすべてのデータを取得
SELECT * FROM products;

-- 2. 商品の名前と価格だけを取得
SELECT name, price FROM products;

-- 3. 在庫がある商品（stock > 0）だけを表示
SELECT name, price, stock FROM products WHERE stock > 0;
```

</details>

#### 問題 4-2: 比較演算子の使用

以下の条件に一致する商品を取得するクエリーを作成してください。

1. 価格が 30,000円以上の商品
2. 在庫数が 5個以下の商品
3. カテゴリー ID が 1 の商品

<details>

<summary>ヒント</summary>

* 比較演算子 `>=` 、`<=`、 `=` などを使用します

- カンマで区切って複数のカラムを表示できます

</details>

<details>

<summary>解答</summary>

```sql
-- 1. 価格が30,000円以上の商品
SELECT name, price FROM products WHERE price >= 30000;

-- 2. 在庫数が5個以下の商品
SELECT name, stock FROM products WHERE stock <= 5;

-- 3. カテゴリIDが1の商品
SELECT name, category_id FROM products WHERE category_id = 1;
```

</details>

#### 問題 4-3: 論理演算子の使用

以下のクエリーを作成・実行してください。

1. 価格が 10,000円以上かつカテゴリー ID が 1 の商品
2. 在庫が 10個以下または価格が 100,000円以上の商品
3. カテゴリー ID が 1 ではない商品

<details>

<summary>ヒント</summary>

* 複数の条件を組み合わせるには `AND`、`OR`、`NOT` を使います

- 「〜ではない」 という条件は `NOT` または `<>` で表現します

</details>

<details>

<summary>解答</summary>

```sql
-- 1. 価格が10,000円以上かつカテゴリIDが1の商品
SELECT name, price, category_id
FROM products
WHERE price >= 10000 AND category_id = 1;

-- 2. 在庫が10個以下または価格が100,000円以上の商品
SELECT name, stock, price
FROM products
WHERE stock <= 10 OR price >= 100000;

-- 3. カテゴリIDが1ではない商品
SELECT name, category_id
FROM products
WHERE NOT category_id = 1;
-- または: WHERE category_id <> 1;
```

</details>

#### 問題 4-4: 条件演算子の使用 ⭐️

以下のクエリーを作成・実行してください。

1. 価格が 5,000円から 50,000円の範囲にある商品
2. カテゴリー ID が 1, 3, 5のいずれかに該当する商品
3. 商品名に 「PC」 または 「フォン」 を含む商品

<details>

<summary>ヒント</summary>

* 範囲を指定するには `BETWEEN` 演算子を使います

- 複数の値のいずれかに一致するかを調べるには `IN` 演算子を使います

* 文字列のパターンマッチングには `LIKE` 演算子と `%` (ワイルドカード) を使います

</details>

<details>

<summary>解答</summary>

```sql
-- 1. 価格が5,000円から50,000円の範囲にある商品
SELECT name, price
FROM products
WHERE price BETWEEN 5000 AND 50000;

-- 2. カテゴリIDが1, 3, 5のいずれかに該当する商品
SELECT name, category_id
FROM products
WHERE category_id IN (1, 3, 5);

-- 3. 商品名に「PC」または「フォン」を含む商品
SELECT name
FROM products
WHERE name LIKE '%PC%' OR name LIKE '%フォン%';
```

</details>

#### 問題 4-5: 複合条件の使用 ⭐️

以下の条件に一致する商品を検索するクエリーを作成してください：

1. カテゴリー ID が 1 または 2、かつ価格が 30,000円以上の商品
2. 商品名に 「ス」 で始まる、または 「ル」 で終わる商品
3. 説明 (description) が NULL でない、かつ在庫が 10個以上ある商品

<details>

<summary>ヒント</summary>

* 複合条件では、カッコを使うと条件の優先順位を明確にできます

- 文字列の前方一致は `LIKE 'パターン%'`、後方一致は `LIKE '%パターン'` で表現します

* NULL値の検索には `IS NULL` または `IS NOT NULL` を使います

</details>

<details>

<summary>解答</summary>

```sql
-- 1. カテゴリIDが1または2、かつ価格が30,000円以上の商品
SELECT name, category_id, price
FROM products
WHERE (category_id = 1 OR category_id = 2) AND price >= 30000;

-- 2. 商品名に「ス」で始まる、または「ル」で終わる商品
SELECT name
FROM products
WHERE name LIKE 'ス%' OR name LIKE '%ル';

-- 3. 説明（description）がNULLでない、かつ在庫が10個以上ある商品
SELECT name, description, stock
FROM products
WHERE description IS NOT NULL AND stock >= 10;
```

</details>
