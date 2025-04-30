---
description: データベースの大きな利点は、複数のテーブルに分散したデータを必要に応じて結合できる点にあります。この章では、テーブル結合の基本と応用について学びます。
---

# 8. テーブルの結合

### 8.1 テーブル結合の基本概念

リレーショナルデータベースでは、データを複数のテーブルに分割して保存します。これにより、データの重複を減らし、整合性を高めることができます。しかし、必要な情報を得るためには、これらのテーブルを結合する必要があります。

**8.1.1 なぜテーブルを結合するのか**

例えば、次のような2つのテーブルを考えてみましょう。

**商品テーブル (products)**

```
+----+----------------+--------+-------------+
| id | name           | price  | category_id |
+----+----------------+--------+-------------+
|  1 | スマートフォン   |  89000 |           1 |
|  2 | ラップトップPC   | 120000 |           1 |
|  3 | コーヒーマシン   |  15000 |           2 |
+----+----------------+--------+-------------+
```

**カテゴリ**ー**テーブル (categories)**

```
+----+---------------+
| id | name          |
+----+---------------+
|  1 | 電子機器       |
|  2 | 家電          |
|  3 | 家具          |
+----+---------------+
```

商品の一覧と各商品のカテゴリ名を表示したい場合、テーブル結合を使用します。

> 📝 **ポイント**: テーブル結合を使うと、正規化されたデータベースから必要な情報を取得できます。これにより、データの重複を避けながらも、関連する情報を1つの結果セットとして表示できます。例えば、商品テーブルにカテゴリ名をそのまま保存する代わりに、category\_id (外部キー) を使用して関連付けることで、データの一貫性と効率性が向上します。

### 8.2 エイリアスの基本

テーブルやカラムに別名 (エイリアス) を付けると、SQL が読みやすくなります。特に結合クエリーで:は、別名を使用することで、どのテーブルのどのカラムを参照しているかが明確になります。

#### 8.2.1 エイリアスの設定方法

エイリアスは `FROM` 句や `JOIN` 句でテーブル名の後に指定します。

```sql
-- エイリアスの基本的な使い方
SELECT 
    p.name,  -- products テーブルの name 列
    c.name   -- categories テーブルの name 列
FROM 
    products p,  -- products テーブルに p という別名を付ける
    categories c  -- categories テーブルに c という別名を付ける
WHERE 
    p.category_id = c.id;
```

または、`AS` キーワードを使って明示的に指定することもできます。

```sql
SELECT p.name, c.name
FROM products AS p, categories AS c
WHERE p.category_id = c.id;
```

> 📝 **ポイント**: エイリアスは、特に複数のテーブルを結合する場合や、カラム名が重複する場合に有用です。コードの可読性が向上し、記述量も減ります。短い別名 (p, c など) を使うことで、タイピング量も減らせます。

### 8.3 内部結合 (INNER JOIN)

内部結合 (INNER JOIN) は最も基本的な結合タイプで、両方のテーブルで一致するレコードのみを返します。

#### 8.3.1 INNER JOIN の基本構文

```sql
SELECT 列1, 列2, ...
FROM テーブル1
INNER JOIN テーブル2 ON 結合条件;
```

`INNER` キーワードは省略可能で、単に `JOIN` と書くこともできます。

```sql
-- 商品とカテゴリーの結合 (INNER JOIN)
SELECT 
    p.id AS 商品ID, 
    p.name AS 商品名, 
    p.price AS 価格, 
    c.name AS カテゴリ名
FROM 
    products p
INNER JOIN 
    categories c ON p.category_id = c.id;
```

<details>

<summary>実行結果</summary>

```
+--------+----------------+--------+------------+
| 商品ID  | 商品名         | 価格    | カテゴリー名   |
+--------+----------------+--------+------------+
|      1 | スマートフォン   |  89000 | 電子機器    |
|      2 | ラップトップPC   | 120000 | 電子機器    |
|      3 | コーヒーマシン   |  15000 | 家電        |
+--------+----------------+--------+------------+
3 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 内部結合では、結合条件に一致するレコードのみが結果に含まれます。つまり、結合先のテーブルに一致するレコードがないデータは、結果から除外されます。たとえば、カテゴリー ID が NULL の商品や、存在しないカテゴリー ID を持つ商品は結果に表示されません。

### 8.4 外部結合 (OUTER JOIN)

外部結合は、結合条件に一致しないレコードも結果に含める場合に使用します。

#### 8.4.1 LEFT JOIN (左外部結合)

LEFT JOIN は、左側のテーブル (FROM 句に指定したテーブル) のすべてのレコードと、右側のテーブルの一致するレコードを返します。右側のテーブルに一致するレコードがない場合、NULL 値が返されます。

```sql
-- LEFT JOIN (左外部結合) の例
SELECT 
    p.id AS 商品ID, 
    p.name AS 商品名, 
    c.name AS カテゴリ名
FROM 
    products p
LEFT JOIN 
    categories c ON p.category_id = c.id;
```

結合条件が一致しない場合の動作を確認するため、カテゴリーの存在しない商品を追加してみましょう。

```sql
-- カテゴリーが存在しない商品を追加
INSERT INTO products (id, name, price, category_id)
VALUES (4, '特別商品', 50000, 99);
```

LEFT JOIN の結果:

```
+--------+----------------+------------+
| 商品ID  | 商品名         | カテゴリ名   |
+--------+----------------+------------+
|      1 | スマートフォン   | 電子機器    |
|      2 | ラップトップPC   | 電子機器    |
|      3 | コーヒーマシン   | 家電        |
|      4 | 特別商品        | NULL       |
+--------+----------------+------------+
4 rows in set (0.00 sec)
```

> 📝 **ポイント**: LEFT JOIN は、結合条件に一致しないレコードも表示したい場合に便利です。たとえば、すべての商品を表示し、カテゴリーがある場合はそれも表示したい場合などに使います。実務では、データの欠損があっても全レコードを確認したい場合によく使用されます。

#### 8.4.2 RIGHT JOIN (右外部結合)

RIGHT JOIN は、LEFT JOIN の逆で、右側のテーブルのすべてのレコードと、左側のテーブルの一致するレコードを返します。

```sql
-- RIGHT JOIN (右外部結合) の例
SELECT 
    p.name AS 商品名,
    c.id AS カテゴリID,
    c.name AS カテゴリ名
FROM 
    products p
RIGHT JOIN 
    categories c ON p.category_id = c.id;
```

<details>

<summary>実行結果</summary>

```
+----------------+------------+----------+
| 商品名          | カテゴリID  | カテゴリ名 |
+----------------+------------+----------+
| スマートフォン   |          1 | 電子機器  |
| ラップトップPC   |          1 | 電子機器  |
| コーヒーマシン   |          2 | 家電      |
| NULL           |          3 | 家具     |
+----------------+------------+----------+
4 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: RIGHT JOIN は左側のテーブルに対応するレコードがなくても、右側のテーブルのすべてのレコードを表示します。この例では、「家具」 カテゴリーに対応する商品がないため、商品名は NULL になっています。実務では LEFT JOIN を使用することが多く、RIGHT JOIN はあまり頻繁には使用されません。

### 8.5 自己結合の基本 ⭐️

同じテーブルを異なる役割で結合する技法を自己結合と呼びます。例えば、従業員テーブルで上司と部下の関係を表現する場合などに使用します。

#### 8.5.1 自己結合の例

従業員テーブル (employees) を例に考えてみましょう。

```
+----+-------------+------------+
| id | name        | manager_id |
+----+-------------+------------+
| 1  | 山田太郎     | NULL       | (社長、上司なし)
| 2  | 佐藤花子     | 1          | (山田の部下)
| 3  | 鈴木一郎     | 1          | (山田の部下)
| 4  | 高橋次郎     | 2          | (佐藤の部下)
+----+-------------+------------+
```

このテーブルで、従業員とその上司の名前を表示するには、自己結合を使用します。

```sql
-- 自己結合の例 (従業員と上司の関係)
SELECT 
    e1.name AS 従業員名,
    e2.name AS 上司名
FROM 
    employees e1
LEFT JOIN 
    employees e2 ON e1.manager_id = e2.id;
```

<details>

<summary>実行結果</summary>

```
+-------------+-------------+
| 従業員名     | 上司名       |
+-------------+-------------+
| 山田太郎     | NULL        | (上司なし)
| 佐藤花子     | 山田太郎     |
| 鈴木一郎     | 山田太郎     |
| 高橋次郎     | 佐藤花子     |
+-------------+-------------+
4 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: 自己結合では、同じテーブルを異なる別名 (この例では e1 と e2) で参照します。これにより、同じテーブル内の異なるレコード間の関係を表現できます。階層構造 (上司と部下、親カテゴリーと子カテゴリーなど) を扱う場合に特に有用です。

### 8.6 複数条件での結合

結合条件は単一の条件だけでなく、複数の条件を組み合わせることもできます。

#### 8.6.1 複数条件での結合の例

従業員テーブルに部署IDを追加した例で考えてみましょう。

```
+----+-------------+------------+---------------+
| id | name        | manager_id | department_id |
+----+-------------+------------+---------------+
| 1  | 山田太郎     | NULL       | 1             | (営業部長)
| 2  | 佐藤花子     | 1          | 1             | (営業部員)
| 3  | 鈴木一郎     | 1          | 1             | (営業部員)
| 4  | 高橋次郎     | NULL       | 2             | (開発部長)
| 5  | 田中三郎     | 4          | 2             | (開発部員)
| 6  | 伊藤四郎     | 4          | 2             | (開発部員)
| 7  | 渡辺五郎     | 4          | 1             | (営業部に異動した開発部員、上司は開発部長のまま)
+----+-------------+------------+---------------+
```

同じ部署内での上司と部下の関係のみを取得するには、複数の条件で結合します。

```sql
-- 複数条件での結合
SELECT 
    e1.name AS 従業員名,
    e2.name AS 上司名,
    e1.department_id AS 部署ID
FROM 
    employees e1
JOIN 
    employees e2 ON e1.manager_id = e2.id AND e1.department_id = e2.department_id;
```

<details>

<summary>実行結果</summary>

```
+-------------+-------------+---------+
| 従業員名     | 上司名        | 部署ID  |
+-------------+-------------+---------+
| 佐藤花子     | 山田太郎      | 1       |
| 鈴木一郎     | 山田太郎      | 1       |
| 田中三郎     | 高橋次郎      | 2       |
| 伊藤四郎     | 高橋次郎      | 2       |
+-------------+-------------+---------+
4 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: この例では 「従業員と上司が同じ部署に所属している」 という条件を追加しています。そのため、部署をまたいだ上司-部下関係 (渡辺五郎と高橋次郎の関係) は結果に含まれません。複数条件での結合を使うと、より細かいビジネスルールをクエリーで表現できます。

### 8.7 多テーブル結合

多くの実世界のシナリオでは、3つ以上のテーブルを結合する必要があります。

#### 8.7.1 3つ以上のテーブルの結合

JOIN を連続して使用することで、複数のテーブルを結合できます。

```sql
-- 3つのテーブルを結合
SELECT 
    o.id AS 注文ID,
    c.name AS 顧客名,
    p.name AS 商品名,
    oi.quantity AS 数量,
    oi.price AS 単価,
    oi.price * oi.quantity AS 小計
FROM 
    orders o
JOIN 
    customers c ON o.customer_id = c.id
JOIN 
    order_items oi ON o.id = oi.order_id
JOIN 
    products p ON oi.product_id = p.id;
```

この例では、注文 (orders)、顧客 (customers)、注文明細 (order\_items)、商品 (products) の 4つのテーブルを結合しています。

> 📝 **ポイント**: 複数のテーブルを結合する場合、結合する順序は結果に影響を与えることがあります。一般的には、FROM 句に指定するテーブルを起点として、関連するテーブルを順番に結合していきます。多くのテーブルを結合すると、パフォーマンスに影響する可能性があるため、必要なテーブルのみを結合するようにしましょう。

### 8.8 結合のベストプラクティス

#### 8.8.1 パフォーマンスの考慮

テーブル結合はデータベースに負荷をかける操作です。以下のポイントに注意しましょう。

1. **必要なカラムのみを選択する**: `SELECT *` ではなく、必要なカラムだけを指定する
2. **適切なインデックスを作成する**: 結合に使用するカラムにはインデックスを作成する
3. **結合するテーブル数を最小限に**: 必要なテーブルだけを結合する

```sql
-- 良い例: 必要なカラムのみを選択
SELECT 
    p.name, 
    c.name 
FROM products p 
JOIN categories c ON p.category_id = c.id;

-- 悪い例: すべてのカラムを選択
SELECT * 
FROM products p 
JOIN categories c ON p.category_id = c.id;
```

> 📝 **ポイント**: 実務では、データ量が増えるにつれてクエリーのパフォーマンスが重要になります。特に結合操作は処理が重くなりがちなので、適切な最適化を行うことが大切です。



***

### 演習問題

演習問題を行うために、以下の SQL スクリプトを実行してサンプルデータベースを準備しましょう。

<details>

<summary>サンプルデータベース</summary>

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

-- 従業員テーブルの作成（自己結合の練習用）
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

-- 従業員データ (自己結合の練習用)
INSERT INTO employees (id, name, manager_id, department_id) VALUES
(1, '山田太郎', NULL, 1),     -- 営業部長
(2, '佐藤花子', 1, 1),        -- 営業部員
(3, '鈴木一郎', 1, 1),        -- 営業部員
(4, '高橋次郎', NULL, 2),     -- 開発部長
(5, '田中三郎', 4, 2),        -- 開発部員
(6, '伊藤四郎', 4, 2),        -- 開発部員
(7, '渡辺五郎', 4, 1);        -- 営業部に異動した開発部員
```

</details>

#### 問題 8-1: 基本的な内部結合

顧客テーブル (customers) と注文テーブル (orders) を結合して、各注文の顧客名と注文日、合計金額を表示するクエリを作成してください。

<details>

<summary>解答</summary>

```sql
SELECT 
    c.name AS 顧客名, 
    o.order_date AS 注文日, 
    o.total_amount AS 合計金額
FROM 
    orders o
JOIN 
    customers c ON o.customer_id = c.id;
```

</details>

#### 問題 8-2: 外部結合の活用 ⭐️

すべての顧客と、注文情報 (ある場合) を表示するクエリーを作成してください。注文をしていない顧客も結果に含めてください。

<details>

<summary>解答</summary>

```sql
SELECT 
    c.name AS 顧客名,
    o.id AS 注文ID,
    o.order_date AS 注文日
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id;
```

</details>

#### 問題 8-3: 自己結合の活用 ⭐️

従業員テーブル (employees) があり、以下の構造を持っているとします。

```
id: 主キー
name: 従業員名
manager_id: 上司のID (従業員テーブル自身の外部キー)
```

このテーブルを使って、各従業員の名前とその上司の名前を表示するクエリーを作成してください。上司がいない従業員 (社長など) も結果に含めてください。

<details>

<summary>解答</summary>

```sql
SELECT 
    e1.name AS 従業員名,
    e2.name AS 上司名
FROM 
    employees e1
LEFT JOIN 
    employees e2 ON e1.manager_id = e2.id;
```

</details>

#### 問題 8-4: 多テーブル結合 ⭐️

注文、顧客、注文明細、商品の各テーブルを結合して、顧客ごとの注文商品の詳細を表示するクエリーを作成してください。表示する情報は、顧客名、注文 ID、注文日、商品名、数量、単価、小計 (単価 × 数量) としてください。

<details>

<summary>解答</summary>

```sql
SELECT 
    c.name AS 顧客名,
    o.id AS 注文ID,
    o.order_date AS 注文日,
    p.name AS 商品名,
    oi.quantity AS 数量,
    oi.price AS 単価,
    oi.quantity * oi.price AS 小計
FROM 
    customers c
JOIN 
    orders o ON c.id = o.customer_id
JOIN 
    order_items oi ON o.id = oi.order_id
JOIN 
    products p ON oi.product_id = p.id
ORDER BY 
    c.name, o.id;
```

</details>
