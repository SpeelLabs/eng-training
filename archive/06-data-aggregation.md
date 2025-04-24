---
description: >-
  この章では、データベース内のデータを集計して分析するための機能について学びます。集計関数とグループ化を使うことで
  、データの傾向や集計値を効率的に得ることができます。
---

# 6. データの集計

### 6.1 集計関数

集計関数は、複数の行から計算された単一の値を返す関数です。主な集計関数は以下の通りです：

<table><thead><tr><th width="184.5347900390625">関数</th><th>説明</th></tr></thead><tbody><tr><td>COUNT()</td><td>行数を数える</td></tr><tr><td>SUM()</td><td>数値の合計を計算する</td></tr><tr><td>AVG()</td><td>数値の平均を計算する</td></tr><tr><td>MAX()</td><td>最大値を見つける</td></tr><tr><td>MIN()</td><td>最小値を見つける</td></tr></tbody></table>

> 📝 **ポイント**: 集計関数はテーブル全体や特定の条件で絞り込んだ行のグループに対して計算を行います。普通の関数 (LENGTHなど) が各行に対して個別に適用されるのに対し、集計関数は複数行を一つの値にまとめます。これが「集計」の意味です。

#### COUNT関数

`COUNT()` 関数は、指定した条件に一致する行の数をカウントします。

```sql
-- テーブル内の全レコード数を取得
SELECT COUNT(*) AS 商品数 FROM products;
```

<details>

<summary>実行結果</summary>

```
+--------+
| 商品数 |
+--------+
|      7 |
+--------+
1 row in set (0.00 sec)
```

</details>

特定のカラムを指定した場合、NULL 値ではない行のみをカウントします。

```sql
-- description列がNULLでない行の数を取得
SELECT COUNT(description) AS 説明あり FROM products;
```

<details>

<summary>実行結果</summary>

```
+---------+
| 説明あり |
+---------+
|       5 |
+---------+
1 row in set (0.00 sec)
```

</details>

特定の条件に一致する行の数をカウント。

```sql
-- 在庫がある商品の数を取得
SELECT COUNT(*) AS 在庫あり商品数
FROM products
WHERE stock > 0;
```

<details>

<summary>実行結果</summary>

```
+--------------+
| 在庫あり商品数 |
+--------------+
|            6 |
+--------------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `COUNT(*)` と `COUNT(カラム名)` の違いを理解することが重要です。`COUNT(*)` は全ての行を数えるのに対し、`COUNT(カラム名)` は指定したカラムが NULL でない行だけを数えます。この違いは特にNULL値を含むデータを扱う際に重要になります。

#### SUM関数

`SUM()` 関数は、指定したカラムの値の合計を計算します。

```sql
-- 商品の在庫数の合計
SELECT SUM(stock) AS 総在庫数 FROM products;
```

<details>

<summary>実行結果</summary>

```
+----------+
| 総在庫数 |
+----------+
|      145 |
+----------+
1 row in set (0.00 sec)
```

</details>

条件と組み合わせて使用。

```sql
-- カテゴリID=1の商品の在庫数合計
SELECT 
    SUM(stock) AS カテゴリ1総在庫数
FROM products
WHERE category_id = 1;
```

<details>

<summary>実行結果</summary>

```
+----------------+
| カテゴリ1総在庫数 |
+----------------+
|             90 |
+----------------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `SUM()` 関数は NULL 値を無視します。また、数値型のカラムにのみ使用できます。文字列や日付型には使用できません。`SUM()` は在庫数や金額の合計を計算する際によく使用されます。

#### AVG関数

`AVG()` 関数は、指定したカラムの値の平均を計算します。

```sql
-- 商品の平均価格
SELECT AVG(price) AS 平均価格 FROM products;
```

<details>

<summary>実行結果</summary>

```
+------------+
| 平均価格    |
+------------+
| 35142.8571 |
+------------+
1 row in set (0.00 sec)
```

</details>

より見やすく小数点以下を丸める。

```sql
-- 小数点以下を2桁に丸めた平均価格
SELECT ROUND(AVG(price), 2) AS 平均価格 FROM products;
```

<details>

<summary>実行結果</summary>

```
+------------+
| 平均価格    |
+------------+
|   35142.86 |
+------------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `AVG()` 関数も`SUM()` と同様に NULL 値を無視し、数値型のカラムにのみ使用できます。平均値は小数点以下の桁数が多くなる場合があるため、`ROUND()` 関数と組み合わせて使うことが多いです。

#### MAX 関数と MIN 関数

`MAX()` と `MIN()` 関数は、それぞれ指定したカラムの最大値と最小値を取得します。

```sql
-- 最も高い商品価格と最も安い商品価格
SELECT 
    MAX(price) AS 最高価格,
    MIN(price) AS 最低価格
FROM products;
```

<details>

<summary>実行結果</summary>

```
+------------+------------+
| 最高価格    | 最低価格    |
+------------+------------+
|  120000.00 |    2000.00 |
+------------+------------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `MAX()` と `MIN()` は数値だけでなく、文字列や日付など、順序付けできるデータ型であれば使用できます。文字列の場合は辞書順 (アルファベット順) で比較されることに注意してください。日本語の場合、文字コードの順序になるため、直感的でない結果になることがあります。

#### 計算フィールドと条件式 ⭐️

SELECT クエリー内で計算を実行できます。

```sql
-- 商品の合計金額 (価格 × 在庫数)
SELECT 
    name, 
    price, 
    stock, 
    price * stock AS 合計金額
FROM products;
```

<details>

<summary>実行結果</summary>

```
+--------------------+----------+-------+------------+
| name               | price    | stock | 合計金額    |
+--------------------+----------+-------+------------+
| スマートフォン        | 89000.00 |    25 | 2225000.00 |
| ラップトップPC       |120000.00 |    15 | 1800000.00 |
| コーヒーメーカー      | 15000.00 |    30 |  450000.00 |
| キーボード           |  6000.00 |    20 |  120000.00 |
| マウス              |  2000.00 |    30 |   60000.00 |
+--------------------+----------+-------+------------+
5 rows in set (0.00 sec)
```

</details>

`CASE` 式を使うと、条件に基づいて異なる値を返すことができます。

```sql
-- 価格帯による分類
SELECT 
    name, 
    price,
    CASE 
        WHEN price < 5000 THEN '低価格'
        WHEN price < 50000 THEN '中価格'
        ELSE '高価格'
    END AS 価格帯
FROM products;
```

<details>

<summary>実行結果</summary>

```
+--------------------+----------+----------+
| name               | price    | 価格帯   |
+--------------------+----------+----------+
| スマートフォン     | 89000.00 | 高価格   |
| ラップトップPC     |120000.00 | 高価格   |
| コーヒーメーカー   | 15000.00 | 中価格   |
| キーボード         |  6000.00 | 中価格   |
| マウス             |  2000.00 | 低価格   |
+--------------------+----------+----------+
5 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `CASE` 式は SQL における条件分岐で、`SELECT` 句だけでなく、`WHERE` 句や `ORDER BY` 句でも使用できます。条件は上から順に評価され、最初に一致した条件の値が返されます。どの条件にも一致しなければ `ELSE` の値が返され、`ELSE` がなければ `NULL` が返されます。

### 6.2 GROUP BY 句によるグループ化

「カテゴリごとの商品数」 「都市別の売上合計」 など、データをグループ化して集計したい場合に`GROUP BY` 句を使います。

`GROUP BY` 句を使用すると、指定したカラムの値が同じ行をグループ化し、各グループに対して集計関数を適用できます。

#### 基本的なグループ化

```sql
-- カテゴリごとの商品数
SELECT 
    category_id,
    COUNT(*) AS 商品数
FROM products
GROUP BY category_id;
```

<details>

<summary>実行結果</summary>

```
+-------------+--------+
| category_id | 商品数 |
+-------------+--------+
|           1 |      4 |
|           2 |      2 |
|           3 |      1 |
+-------------+--------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `GROUP BY` 句を使用する場合、SELECT 句に指定できるのは、(1) `GROUP BY` に指定したカラム、(2) 集計関数、のいずれかのみです。これは SQL 初心者がつまずく最も一般的なポイントの一つです。例えば、`SELECT name, category_id, COUNT(*) FROM products GROUP BY category_id` は正しくありません。なぜなら `name` は `GROUP BY` に指定されておらず、集計関数でもないからです。

#### 集計関数を組み合わせたグループ化 ⭐️

GROUP BY 句と複数の集計関数を組み合わせることができます。

```sql
-- カテゴリごとの統計情報
SELECT 
    category_id,
    COUNT(*) AS 商品数,
    ROUND(AVG(price), 0) AS 平均価格,
    MIN(price) AS 最低価格,
    MAX(price) AS 最高価格,
    SUM(stock) AS 総在庫数
FROM products
GROUP BY category_id;
```

<details>

<summary>実行結果</summary>

```
+-------------+--------+------------+------------+------------+----------+
| category_id | 商品数 | 平均価格   | 最低価格   | 最高価格   | 総在庫数 |
+-------------+--------+------------+------------+------------+----------+
|           1 |      4 |     54500  |    2000.00 |  120000.00 |       90 |
|           2 |      2 |     23500  |   12000.00 |   35000.00 |       25 |
|           3 |      1 |      3500  |    3500.00 |    3500.00 |       30 |
+-------------+--------+------------+------------+------------+----------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 複数の集計関数を組み合わせることで、各グループの詳細な分析が可能になります。これは 「グループごとのサマリー」 を作成する際に非常に役立ちます。

### 6.3 HAVING によるグループのフィルタリング

`WHERE` 句がグループ化の前に行を絞り込むのに対し、`HAVING` 句はグループ化した後の結果を絞り込みます。

#### 基本的な HAVING の使用

```sql
-- 商品が2つ以上あるカテゴリを抽出
SELECT 
    category_id,
    COUNT(*) AS 商品数
FROM products
GROUP BY category_id
HAVING COUNT(*) >= 2;
```

<details>

<summary>実行結果</summary>

```
+-------------+-------+
| category_id | 商品数 |
+-------------+-------+
|           1 |     4 |
|           2 |     2 |
+-------------+-------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `HAVING` 句は `GROUP BY` 句と一緒に使われ、グループ化した結果に対して条件を指定します。`HAVING` 句では集計関数を条件として使用できる点が特徴です。

#### WHERE 句と HAVING 句の違い ⭐️

`WHERE` 句と `HAVING` 句は一緒に使うこともできます：

* `WHERE` 句: 集計前の行を絞り込む (個々のレコードに対する条件)
* `HAVING` 句: 集計後のグループを絞り込む (グループに対する条件)

```sql
-- 価格が5,000円以上の商品だけを対象に、平均価格が50,000円以上のカテゴリを抽出
SELECT 
    category_id,
    ROUND(AVG(price), 0) AS 平均価格,
    COUNT(*) AS 商品数
FROM products
WHERE 
    price >= 5000  -- 個々の商品に対する条件（集計前）
GROUP BY 
    category_id
HAVING 
    AVG(price) >= 50000;  -- グループに対する条件（集計後）
```

<details>

<summary>実行結果</summary>

```
+-------------+------------+--------+
| category_id | 平均価格   | 商品数 |
+-------------+------------+--------+
|           1 |     71667  |      3 |
+-------------+------------+--------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `WHERE` と `HAVING` の違いを理解することが重要です。
>
> 1. `WHERE` は集計前のレコードに対する条件 (例：価格が5,000円以上の商品)
> 2. `HAVING` は集計後のグループに対する条件 (例：平均価格が50,000円以上のカテゴリ)
>
> 実行順序は、まず `WHERE` で絞り込み、次に `GROUP BY` でグループ化し、最後に `HAVING` でグループを絞り込みます。

#### 複雑な HAVING 条件 ⭐️

複数の集計条件を組み合わせることもできます。

```sql
-- 商品数が2つ以上かつ平均価格が10,000円以上のカテゴリ
SELECT 
    category_id,
    COUNT(*) AS 商品数,
    ROUND(AVG(price), 0) AS 平均価格
FROM products
GROUP BY category_id
HAVING 
    COUNT(*) >= 2 
    AND AVG(price) >= 10000;
```

<details>

<summary>実行結果</summary>

```
+-------------+--------+------------+
| category_id | 商品数 | 平均価格   |
+-------------+--------+------------+
|           1 |      4 |     54500  |
|           2 |      2 |     23500  |
+-------------+--------+------------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 複数の集計条件を組み合わせる場合も、論理演算子 ( `AND`, `OR` ) を使用できます。これにより、より詳細な分析が可能になります。

### 演習問題

#### 問題 1: 基本的な集計

商品テーブルに対して以下の集計クエリーを作成してください。

1. 商品の総数を取得する
2. 在庫がある商品 (stock > 0) の数を取得する
3. 最も高い商品価格と最も安い商品価格を求める
4. すべての商品の在庫数の合計を求める

<details>

<summary>解答</summary>

```sql
-- 1. 商品の総数
SELECT COUNT(*) AS 商品総数 FROM products;

-- 2. 在庫がある商品数
SELECT COUNT(*) AS 在庫あり商品数
FROM products
WHERE stock > 0;

-- 3. 最高価格と最低価格
SELECT 
    MAX(price) AS 最高価格,
    MIN(price) AS 最低価格
FROM products;

-- 4. 総在庫数
SELECT SUM(stock) AS 総在庫数 FROM products;
```

</details>

#### 問題 2: グループ別の集計 ⭐️

以下のクエリーを作成してください。

1. カテゴリごとの商品数を求める
2. カテゴリごとの平均価格を求め、平均価格の高い順に表示する
3. カテゴリごとの総在庫数を求める

<details>

<summary>解答</summary>

```sql
-- 1. カテゴリごとの商品数
SELECT 
    category_id,
    COUNT(*) AS 商品数
FROM products
GROUP BY category_id;

-- 2. カテゴリごとの平均価格（高い順）
SELECT 
    category_id,
    ROUND(AVG(price), 0) AS 平均価格
FROM products
GROUP BY category_id
ORDER BY 平均価格 DESC;

-- 3. カテゴリごとの総在庫数
SELECT 
    category_id,
    SUM(stock) AS 総在庫数
FROM products
GROUP BY category_id;
```

</details>

#### 問題 3: HAVINGによるフィルタリング ⭐️

以下のクエリーを作成してください。

1. 商品数が 2つ以上あるカテゴリを抽出する
2. 平均価格が 20,000円以上のカテゴリを抽出する
3. 総在庫数が 30個以上のカテゴリを、総在庫数の多い順に表示する

<details>

<summary>解答</summary>

```sql
-- 1. 商品数が2つ以上のカテゴリ
SELECT 
    category_id,
    COUNT(*) AS 商品数
FROM products
GROUP BY category_id
HAVING COUNT(*) >= 2;

-- 2. 平均価格が20,000円以上のカテゴリ
SELECT 
    category_id,
    ROUND(AVG(price), 0) AS 平均価格
FROM products
GROUP BY category_id
HAVING AVG(price) >= 20000;

-- 3. 総在庫数が30個以上のカテゴリ（多い順）
SELECT 
    category_id,
    SUM(stock) AS 総在庫数
FROM products
GROUP BY category_id
HAVING SUM(stock) >= 30
ORDER BY 総在庫数 DESC;
```

</details>

#### 問題 4: 組み合わせた集計とフィルタリング ⭐️

以下のクエリーを作成してください。

1. 価格が 10,000円以上の商品のみを対象に、カテゴリごとの商品数と平均価格を求める
2. 在庫がある商品 (stock > 0) のみを対象に、カテゴリごとの最低価格と最高価格を求める
3. 各カテゴリの 「在庫金額」 (価格×在庫数の合計) を計算し、10万円以上のカテゴリのみ表示する

<details>

<summary>解答</summary>

```sql
-- 1. 価格10,000円以上の商品のカテゴリ別集計
SELECT 
    category_id,
    COUNT(*) AS 商品数,
    ROUND(AVG(price), 0) AS 平均価格
FROM products
WHERE price >= 10000
GROUP BY category_id;

-- 2. 在庫がある商品のカテゴリ別価格範囲
SELECT 
    category_id,
    MIN(price) AS 最低価格,
    MAX(price) AS 最高価格
FROM products
WHERE stock > 0
GROUP BY category_id;

-- 3. カテゴリ別在庫金額（10万円以上）
SELECT 
    category_id,
    SUM(price * stock) AS 在庫金額
FROM products
GROUP BY category_id
HAVING SUM(price * stock) >= 100000
ORDER BY 在庫金額 DESC;
```

</details>
