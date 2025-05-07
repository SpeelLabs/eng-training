---
description: >-
  この章では、データベース内のデータを集計して分析するための機能について学びます。集計関数とグループ化を使うことで
  、データの傾向や集計値を効率的に得ることができます。
---

# 6. データの集計

### 6.1 集計関数

集計関数は、複数の行から計算された単一の値を返す関数です。主な集計関数は以下の通りです。

<table><thead><tr><th width="184.5347900390625">関数</th><th>説明</th></tr></thead><tbody><tr><td>COUNT()</td><td>行数を数える</td></tr><tr><td>SUM()</td><td>数値の合計を計算する</td></tr><tr><td>AVG()</td><td>数値の平均を計算する</td></tr><tr><td>MAX()</td><td>最大値を見つける</td></tr><tr><td>MIN()</td><td>最小値を見つける</td></tr></tbody></table>

> 📝 **ポイント**: 集計関数はテーブル全体や特定の条件で絞り込んだ行のグループに対して計算を行います。普通の関数 (文字列関数や数値関数) が各行に対して個別に適用されるのに対し、集計関数は複数行を一つの値にまとめます。

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
| 商品数  |
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

> 📝 **ポイント**: `COUNT(*)` と `COUNT(カラム名)` の違いは非常に重要です。`COUNT(*)` は全ての行を数えますが、`COUNT(カラム名)` は指定したカラムが NULL でない行だけを数えます。実務ではこの違いを理解していないとバグの原因になります。

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
| 総在庫数  |
+----------+
|      145 |
+----------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `SUM()` 関数は NULL 値を無視します。また、数値型のカラムにのみ使用できます。在庫数や金額の合計を計算する際によく使われる重要な関数です。

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
|   35142.86 |
+------------+
1 row in set (0.00 sec)
```

</details>

より見やすく小数点以下を丸める例：

```sql
-- 小数点以下を切り捨てた平均価格
SELECT ROUND(AVG(price), 0) AS 平均価格 FROM products;
```

<details>

<summary>実行結果</summary>

```
+----------+
| 平均価格  |
+----------+
|    35143 |
+----------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `AVG()`  関数も`SUM()` と同様に NULL 値を無視します。平均を計算する際に NULL 値を 0 として扱いたい場合には、`IFNULL(カラム名, 0)` を使って変換する必要があります。また、整数型の列の平均を計算する場合でも、結果は小数点を含む値になることがあります。必要に応じて`ROUND()` 関数で丸めましょう。

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
+----------+----------+
| 最高価格  | 最低価格  |
+----------+----------+
|   120000 |     2000 |
+----------+----------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `MAX()` と `MIN()` は数値だけでなく、日付型にも使用できます。例えば最新の注文日や最も古い記録を見つけるのに便利です。

#### 計算フィールドと条件式 ⭐️

集計関数と計算式を組み合わせて、より複雑な集計を行うことができます。

```sql
-- 商品の合計金額（価格 × 在庫数）
SELECT 
    SUM(price * stock) AS 在庫金額
FROM products;
```

<details>

<summary>実行結果</summary>

```
+----------+
| 在庫金額  |
+----------+
|  4655000 |
+----------+
1 row in set (0.00 sec)
```

</details>

`CASE` 式を使うと、条件に基づいて異なる値を集計できます。

```sql
-- 価格帯ごとの商品数
SELECT 
    CASE 
        WHEN price < 5000 THEN '低価格'
        WHEN price < 50000 THEN '中価格'
        ELSE '高価格'
    END AS 価格帯,
    COUNT(*) AS 商品数
FROM products
GROUP BY 
    CASE 
        WHEN price < 5000 THEN '低価格'
        WHEN price < 50000 THEN '中価格'
        ELSE '高価格'
    END;
```

<details>

<summary>実行結果</summary>

```
+----------+--------+
| 価格帯   | 商品数  |
+----------+--------+
| 低価格   |      2 |
| 中価格   |      3 |
| 高価格   |      2 |
+----------+--------+
3 rows in set (0.01 sec)
```

</details>

> 📝 **ポイント**: 計算フィールドと条件式を組み合わせると、実務で必要なさまざまな分析ができるようになります。例えば、条件を満たす商品の売上合計や、特定の状態の注文の平均金額などを計算できます。

### 6.2 GROUP BY 句によるグループ化

「カテゴリごとの商品数」 「都市別の売上合計」 など、データをグループ化して集計したい場合に`GROUP BY` 句を使います。

**基本的なグループ化**

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
| category_id | 商品数  |
+-------------+--------+
|           1 |      4 |
|           2 |      2 |
|           3 |      1 |
+-------------+--------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `GROUP BY` 句を使用する場合、SELECT 句に指定できるのは、(1) `GROUP BY` に指定したカラム、(2) 集計関数、のいずれかのみです。例えば、`SELECT name, category_id, COUNT(*) FROM products GROUP BY category_id` は正しくありません。これは初学者がよく間違える点のひとつです。

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
+-------------+--------+----------+----------+----------+----------+
| category_id | 商品数  | 平均価格  | 最低価格  | 最高価格  | 総在庫数  |
+-------------+--------+----------+----------+----------+----------+
|           1 |      4 |    54500 |     2000 |   120000 |       90 |
|           2 |      2 |    23500 |    12000 |    35000 |       25 |
|           3 |      1 |     3500 |     3500 |     3500 |       30 |
+-------------+--------+----------+----------+----------+----------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 複数の集計関数を組み合わせることで、各グループの詳細な統計情報を一度に取得できます。実務では、このようなクエリを使って商品カテゴリごとのパフォーマンスや、地域別の売上傾向などを分析します。

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

> 📝 **ポイント**: `HAVING` 句では集計関数の結果に対して条件を指定できます。例えば「平均価格が10,000円以上のカテゴリ」や「総売上が100万円を超える顧客」などの条件で絞り込めます。

#### WHERE 句と HAVING 句の違い ⭐️

`WHERE` 句と `HAVING` 句は一緒に使うこともできます。

* `WHERE` 句: 集計前の行を絞り込む (個々のレコードに対する条件)
* `HAVING` 句: 集計後のグループを絞り込む (グループに対する条件)

```sql
-- 価格が 5,000円以上の商品だけを対象に、平均価格が 50,000円以上のカテゴリを抽出
SELECT 
    category_id,
    ROUND(AVG(price), 0) AS 平均価格,
    COUNT(*) AS 商品数
FROM products
WHERE 
    price >= 5000  -- 個々の商品に対する条件 (集計前)
GROUP BY 
    category_id
HAVING 
    AVG(price) >= 50000;  -- グループに対する条件 (集計後)
```

<details>

<summary>実行結果</summary>

```
+-------------+----------+--------+
| category_id | 平均価格  | 商品数  |
+-------------+----------+--------+
|           1 |    71667 |      3 |
+-------------+----------+--------+
1 row in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `WHERE` と `HAVING` の違いは重要です。例えば、「10,000円以上の商品の平均価格」 と 「平均価格が10,000円以上のカテゴリ」 は全く異なります。前者は WHERE 句、後者は HAVING 句を使います。実行順序も異なり、まず `WHERE` で絞り込み、次に `GROUP BY` でグループ化し、最後に `HAVING` でグループを絞り込みます。

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
+-------------+--------+----------+
| category_id | 商品数  | 平均価格  |
+-------------+--------+----------+
|           1 |      4 |    54500 |
|           2 |      2 |    23500 |
+-------------+--------+----------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 複雑な集計条件を組み合わせることで、「利益率が高く、かつ取引回数が多い顧客」 や 「在庫回転率が低く、かつ在庫金額が大きい商品カテゴリ」 など、ビジネス分析に役立つ情報を抽出できます。



***

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

**問題 4: 組み合わせた集計とフィルタリング ⭐️**

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
