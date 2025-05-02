---
description: サブクエリーとは、別のSQL文の内部に埋め込まれたSELECT文です。複雑なデータ取得や分析を単一のクエリーで実現するために使用します。
---

# 9. サブクエリー

### 9.1 サブクエリーの基本概念

サブクエリーは、SQL文の中に埋め込まれた別のSQL文（多くの場合はSELECT文）です。メインクエリー（外部クエリー）の条件や計算に、サブクエリーの結果を利用できます。

#### 9.1.1 サブクエリーの基本的な構造

サブクエリーは通常、括弧 `()` で囲まれ、外部クエリーの様々な場所に配置できます：

```sql
-- WHERE句内のサブクエリー
SELECT name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

この例では、サブクエリー `(SELECT AVG(price) FROM products)` が平均価格を計算し、その結果を外部クエリーの条件として使用しています。

> 📝 **ポイント**: サブクエリーは処理の流れとしては「内側から外側へ」実行されます。つまり、まずサブクエリーが実行され、その結果を使って外部クエリーが実行されます。サブクエリーを使うことで複数のクエリーを1つのステートメントにまとめられるため、コードが簡潔になり保守性が向上します。

### 9.2 WHERE句内のサブクエリー

WHERE句内のサブクエリーは、外部クエリーの条件の一部として使用されます。

#### 9.2.1 単一値を返すサブクエリー

単一の値（スカラー値）を返すサブクエリーは、比較演算子 (`>`, `<`, `=` など) と共に使用できます：

```sql
-- 平均価格より高い商品を検索
SELECT id, name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

この例を実行すると、平均価格よりも高い商品だけが表示されます。

実行結果:

```
+----+---------------+--------+
| id | name          | price  |
+----+---------------+--------+
|  1 | スマートフォン  |  89000 |
|  2 | ラップトップPC |  120000|
|  5 | ソファ         |  45000 |
+----+---------------+--------+
3 rows in set (0.01 sec)
```

> 📝 **ポイント**: スカラーサブクエリーは1行1列の値のみを返す必要があります。複数の値を返すサブクエリーを比較演算子と共に使用するとエラーになります。

#### 9.2.2 複数値を返すサブクエリー

IN、ANY、ALL などの演算子を使用すると、複数の値を返すサブクエリーと比較できます：

```sql
-- 電子機器カテゴリーの商品を検索
SELECT name, price
FROM products
WHERE category_id IN (
    SELECT id FROM categories WHERE name = '電子機器'
);
```

実行結果:

```
+---------------+--------+
| name          | price  |
+---------------+--------+
| スマートフォン  |  89000 |
| ラップトップPC | 120000 |
+---------------+--------+
2 rows in set (0.01 sec)
```

> 📝 **ポイント**: `IN` 演算子は、サブクエリーが返す値のリストのいずれかに一致するかどうかを調べます。`NOT IN` を使用すると、リストのどの値にも一致しないレコードを選択できます。

### 9.3 FROMまたはJOIN句内のサブクエリー ⭐️

サブクエリーを FROM 句や JOIN 句内で使用すると、仮想的なテーブル（導出テーブルまたは派生テーブル）として扱うことができます。

#### 9.3.1 FROM句内のサブクエリー

```sql
-- カテゴリー別の平均価格を計算
SELECT 
    c.name AS カテゴリー名,
    p_avg.avg_price AS 平均価格
FROM 
    categories c
JOIN (
    SELECT 
        category_id, 
        AVG(price) AS avg_price
    FROM 
        products
    GROUP BY 
        category_id
) p_avg ON c.id = p_avg.category_id;
```

この例では、サブクエリーがカテゴリー別の平均価格を計算し、その結果をカテゴリーテーブルと結合しています。

実行結果:

```
+--------------+----------+
| カテゴリー名  | 平均価格 |
+--------------+----------+
| 電子機器     | 104500   |
| 家電         |  20000   |
| 家具         |  41500   |
| 書籍         |   3850   |
+--------------+----------+
4 rows in set (0.01 sec)
```

> 📝 **ポイント**: FROM句内のサブクエリーは必ず別名（エイリアス）を付ける必要があります。この例では `p_avg` という別名をサブクエリーに付けています。これにより、外部クエリーからサブクエリーの結果を参照できます。

#### 9.3.2 JOIN句内のサブクエリー

```sql
-- 注文回数が2回以上の顧客を抽出
SELECT 
    c.name AS 顧客名,
    o_count.order_count AS 注文回数
FROM 
    customers c
JOIN (
    SELECT 
        customer_id, 
        COUNT(*) AS order_count
    FROM 
        orders
    GROUP BY 
        customer_id
    HAVING 
        COUNT(*) >= 2
) o_count ON c.id = o_count.customer_id;
```

実行結果:

```
+----------+----------+
| 顧客名   | 注文回数 |
+----------+----------+
| 山田太郎 |        2 |
| 鈴木一郎 |        2 |
+----------+----------+
2 rows in set (0.01 sec)
```

> 📝 **ポイント**: JOIN句内のサブクエリーを使うと、条件に一致するレコードだけに結合処理を限定できます。この例では、注文回数が2回以上の顧客だけが結果に含まれます。

### 9.4 SELECT句内のサブクエリー ⭐️

SELECT句にサブクエリーを配置すると、各行に対して計算された値を表示できます。

```sql
-- 各商品の価格と全商品の平均価格との差を表示
SELECT 
    name AS 商品名,
    price AS 価格,
    price - (SELECT AVG(price) FROM products) AS 平均との差額
FROM 
    products;
```

実行結果:

```
+------------------+--------+------------+
| 商品名           | 価格   | 平均との差額 |
+------------------+--------+------------+
| スマートフォン   |  89000 |      55213 |
| ラップトップPC   | 120000 |      86213 |
| コーヒーメーカー |  15000 |     -18787 |
| 電子レンジ       |  25000 |      -8787 |
| ソファ           |  45000 |      11213 |
| ダイニングテーブル|  38000 |       4213 |
| プログラミング入門|   3500 |     -30287 |
| データベース解説 |   4200 |     -29587 |
| 特別商品         |  50000 |      16213 |
+------------------+--------+------------+
9 rows in set (0.01 sec)
```

> 📝 **ポイント**: SELECT句内のサブクエリーは、各行ごとに一回実行されるわけではなく、通常は一度だけ実行されその結果が使い回されます。そのため、例えば「この行の価格と全体平均の差」といった計算が効率的に行えます。ただし、相関サブクエリー（後述）の場合は異なる動作になります。

### 9.5 相関サブクエリー ⭐️

相関サブクエリーは、外部クエリーの値を参照するサブクエリーです。サブクエリーが外部クエリーの各行に対して一度ずつ実行されます。

```sql
-- 各商品カテゴリーの中で最も高い商品を検索
SELECT 
    c.name AS カテゴリー名,
    p.name AS 商品名,
    p.price AS 価格
FROM 
    products p
JOIN 
    categories c ON p.category_id = c.id
WHERE 
    p.price = (
        SELECT MAX(price) 
        FROM products 
        WHERE category_id = p.category_id
    );
```

実行結果:

```
+--------------+------------------+--------+
| カテゴリー名 | 商品名           | 価格   |
+--------------+------------------+--------+
| 電子機器     | ラップトップPC   | 120000 |
| 家電         | 電子レンジ       |  25000 |
| 家具         | ソファ           |  45000 |
| 書籍         | データベース解説 |   4200 |
+--------------+------------------+--------+
4 rows in set (0.01 sec)
```

> 📝 **ポイント**: 相関サブクエリーでは、サブクエリー内で外部クエリーのカラム（この例では `p.category_id`）を参照しています。そのため、外部クエリーの各行ごとにサブクエリーが評価されます。これにより「各カテゴリーごとの最高価格の商品」のような、グループ内の特定の条件を満たすレコードを抽出できます。

### 9.6 EXISTS演算子との組み合わせ ⭐️

EXISTS演算子は、サブクエリーが少なくとも1つの行を返す場合に真となります。主に「条件を満たすレコードが存在するかどうか」を調べるために使用します。

```sql
-- 注文のある顧客のみを表示
SELECT 
    name AS 顧客名,
    email AS メールアドレス
FROM 
    customers c
WHERE 
    EXISTS (
        SELECT 1 
        FROM orders 
        WHERE customer_id = c.id
    );
```

実行結果:

```
+----------+---------------------+
| 顧客名   | メールアドレス      |
+----------+---------------------+
| 山田太郎 | yamada@example.com  |
| 佐藤花子 | sato@example.com    |
| 鈴木一郎 | suzuki@example.com  |
+----------+---------------------+
3 rows in set (0.01 sec)
```

> 📝 **ポイント**: EXISTS演算子は、サブクエリーの結果の内容ではなく、レコードが存在するかどうかだけを調べます。そのため、サブクエリーでは通常 `SELECT 1` と書きます（1は任意の値で、実際には何を選択しても同じ結果になります）。EXISTS演算子は、特に大きなテーブルに対して「関連レコードが存在するかの確認」を行う場合にパフォーマンスが良いことがあります。

### 9.7 サブクエリーのパフォーマンス考慮

サブクエリーは強力ですが、使い方によってはパフォーマンスに影響を与えることがあります。

#### 9.7.1 サブクエリーとJOINの選択

場合によっては、サブクエリーの代わりにJOINを使用した方が効率的なことがあります：

```sql
-- サブクエリーを使った例
SELECT 
    name, 
    price
FROM 
    products
WHERE 
    category_id IN (SELECT id FROM categories WHERE name = '電子機器');

-- JOINを使った同等の例
SELECT 
    p.name, 
    p.price
FROM 
    products p
JOIN 
    categories c ON p.category_id = c.id
WHERE 
    c.name = '電子機器';
```

> 📝 **ポイント**: どちらのアプローチが良いかは、データの量や分布、インデックスの有無など多くの要因に依存します。多くの場合、データベースのクエリーオプティマイザーが最適な実行計画を選択してくれますが、複雑なクエリーではパフォーマンスの違いが出ることがあります。

#### 9.7.2 サブクエリーの再利用

同じサブクエリーを複数回使用する場合は、導出テーブルとして定義すると効率的です：

```sql
-- 非効率な例（同じサブクエリーを2回使用）
SELECT 
    name, 
    price, 
    price - (SELECT AVG(price) FROM products) AS 平均との差額,
    price / (SELECT AVG(price) FROM products) * 100 AS 平均比率
FROM 
    products;

-- 効率的な例（サブクエリーを1回だけ評価）
WITH product_stats AS (
    SELECT AVG(price) AS avg_price FROM products
)
SELECT 
    name, 
    price, 
    price - avg_price AS 平均との差額,
    price / avg_price * 100 AS 平均比率
FROM 
    products, 
    product_stats;
```

> 📝 **ポイント**: `WITH` 句（Common Table Expression、CTE）を使用すると、サブクエリーの結果を一時的なテーブルとして定義できます。これにより、同じクエリーを複数回実行する必要がなくなり、効率が向上します。また、コードの可読性も向上します。

***

### 演習問題

以下の演習問題は、前回のテーブル結合のドキュメントで作成したサンプルデータベースを使用します。サンプルデータベースがまだ作成されていない場合は、以下のSQLスクリプトを実行してください。

```sql
-- 演習用データベースの作成
CREATE DATABASE IF NOT EXISTS join_practice;
USE join_practice;

-- 顧客テーブルの作成
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL
);

-- 注文テーブルの作成
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount INT NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- 商品カテゴリーテーブルの作成
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);

-- 商品テーブルの作成
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    category_id INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

-- 注文明細テーブルの作成
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price INT NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- 従業員テーブルの作成
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    manager_id INT,
    department_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(id)
);

-- サンプルデータの挿入
-- 顧客データ
INSERT INTO customers (id, name, email) VALUES
(1, '山田太郎', 'yamada@example.com'),
(2, '佐藤花子', 'sato@example.com'),
(3, '鈴木一郎', 'suzuki@example.com'),
(4, '田中裕子', 'tanaka@example.com');

-- カテゴリーデータ
INSERT INTO categories (id, name) VALUES
(1, '電子機器'),
(2, '家電'),
(3, '家具'),
(4, '書籍');

-- 商品データ
INSERT INTO products (id, name, price, category_id) VALUES
(1, 'スマートフォン', 89000, 1),
(2, 'ラップトップPC', 120000, 1),
(3, 'コーヒーメーカー', 15000, 2),
(4, '電子レンジ', 25000, 2),
(5, 'ソファ', 45000, 3),
(6, 'ダイニングテーブル', 38000, 3),
(7, 'プログラミング入門', 3500, 4),
(8, 'データベース解説', 4200, 4),
(9, '特別商品', 50000, NULL);

-- 注文データ
INSERT INTO orders (id, customer_id, order_date, total_amount) VALUES
(1, 1, '2025-04-01', 89000),
(2, 1, '2025-04-10', 15000),
(3, 2, '2025-04-05', 120000),
(4, 3, '2025-04-07', 42000),
(5, 3, '2025-04-15', 7700);

-- 注文明細データ
INSERT INTO order_items (order_id, product_id, quantity, price) VALUES
(1, 1, 1, 89000),  -- 山田太郎：スマートフォン
(2, 3, 1, 15000),  -- 山田太郎：コーヒーメーカー
(3, 2, 1, 120000), -- 佐藤花子：ラップトップPC
(4, 5, 1, 45000),  -- 鈴木一郎：ソファ
(5, 7, 1, 3500),   -- 鈴木一郎：プログラミング入門
(5, 8, 1, 4200);   -- 鈴木一郎：データベース解説

-- 従業員データ
INSERT INTO employees (id, name, manager_id, department_id) VALUES
(1, '山田太郎', NULL, 1),     -- 営業部長
(2, '佐藤花子', 1, 1),        -- 営業部員
(3, '鈴木一郎', 1, 1),        -- 営業部員
(4, '高橋次郎', NULL, 2),     -- 開発部長
(5, '田中三郎', 4, 2),        -- 開発部員
(6, '伊藤四郎', 4, 2),        -- 開発部員
(7, '渡辺五郎', 4, 1);        -- 営業部に異動した開発部員
```

#### 問題 9-1: WHERE句内のサブクエリー

平均価格より高い商品を、商品名と価格で一覧表示するクエリーを作成してください。結果は価格の高い順に並べてください。

<details>

<summary>解答</summary>

```sql
SELECT 
    name AS 商品名, 
    price AS 価格
FROM 
    products
WHERE 
    price > (SELECT AVG(price) FROM products)
ORDER BY 
    price DESC;
```

</details>

#### 問題 9-2: 複数値を返すサブクエリー

書籍カテゴリーに属する商品の注文をした顧客の名前を表示するクエリーを作成してください。重複する顧客名は排除してください。

<details>

<summary>解答</summary>

```sql
SELECT DISTINCT
    c.name AS 顧客名
FROM 
    customers c
JOIN 
    orders o ON c.id = o.customer_id
JOIN 
    order_items oi ON o.id = oi.order_id
WHERE 
    oi.product_id IN (
        SELECT id 
        FROM products 
        WHERE category_id = (
            SELECT id 
            FROM categories 
            WHERE name = '書籍'
        )
    );
```

</details>

#### 問題 9-3: FROM句内のサブクエリー ⭐️

各顧客の注文回数と注文合計金額を表示するクエリーを作成してください。注文がない顧客も表示し、その場合は注文回数を0、合計金額をNULLとしてください。

<details>

<summary>解答</summary>

```sql
SELECT 
    c.name AS 顧客名,
    IFNULL(o_summary.order_count, 0) AS 注文回数,
    o_summary.total_sum AS 合計金額
FROM 
    customers c
LEFT JOIN (
    SELECT 
        customer_id,
        COUNT(*) AS order_count,
        SUM(total_amount) AS total_sum
    FROM 
        orders
    GROUP BY 
        customer_id
) o_summary ON c.id = o_summary.customer_id;
```

</details>

#### 問題 9-4: 相関サブクエリーを使った問題 ⭐️

各カテゴリーで、そのカテゴリーの平均価格より高い商品の一覧を表示するクエリーを作成してください。カテゴリー名、商品名、価格、カテゴリー平均価格を表示してください。

<details>

<summary>解答</summary>

```sql
SELECT 
    c.name AS カテゴリー名,
    p.name AS 商品名,
    p.price AS 価格,
    (
        SELECT AVG(price) 
        FROM products 
        WHERE category_id = p.category_id
    ) AS カテゴリー平均価格
FROM 
    products p
JOIN 
    categories c ON p.category_id = c.id
WHERE 
    p.price > (
        SELECT AVG(price) 
        FROM products 
        WHERE category_id = p.category_id
    );
```

</details>

#### 問題 9-5: EXISTS演算子の使用 ⭐️

注文をしたことがない顧客の一覧を表示するクエリーを作成してください。

<details>

<summary>解答</summary>

```sql
SELECT 
    name AS 顧客名,
    email AS メールアドレス
FROM 
    customers c
WHERE 
    NOT EXISTS (
        SELECT 1 
        FROM orders 
        WHERE customer_id = c.id
    );
```

</details>
