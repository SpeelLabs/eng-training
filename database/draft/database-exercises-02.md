---
description: >-
  この実践問題では、オンラインストアのデータベースを使用して、データの集計・分析・加工などの応用的なSQL操作を行います。集計関数やグループ化、計算フィールドなどを活用し、ビジネス上の意思決定に役立つ情報を抽出しましょう。
---

# 実践問題 ②

### 準備：データベースの構築

以下のスクリプトを実行して、実践用データベースとサンプルデータを作成してください。

```sql
-- 新しいデータベースを作成
CREATE DATABASE IF NOT EXISTS online_store;

-- データベースを選択
USE online_store;

-- カテゴリテーブルを作成
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    description VARCHAR(200)
);

-- 商品テーブルを作成
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock INT NOT NULL DEFAULT 0,
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

-- 顧客テーブルを作成
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    city VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 注文テーブルを作成
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('pending', 'processing', 'shipped', 'delivered') NOT NULL DEFAULT 'pending',
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- 注文詳細テーブルを作成
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- サンプルデータの挿入：カテゴリ
INSERT INTO categories (name, description) VALUES
('電子機器', 'スマートフォン、パソコン、その他の電子製品'),
('家具', 'テーブル、椅子、ソファなど'),
('衣類', 'シャツ、パンツ、ドレスなど'),
('食品', '加工食品、飲料、調味料'),
('書籍', '本、電子書籍、雑誌');

-- サンプルデータの挿入：商品
INSERT INTO products (name, price, stock, category_id) VALUES
('スマートフォンX', 89000, 25, 1),
('ノートパソコンY', 120000, 15, 1),
('ワイヤレスイヤホン', 15000, 40, 1),
('BluetoothスピーカーZ', 8500, 30, 1),
('ダイニングテーブル', 35000, 5, 2),
('オフィスチェア', 12000, 20, 2),
('本棚', 18000, 10, 2),
('カジュアルシャツ', 4500, 50, 3),
('デニムパンツ', 6500, 35, 3),
('ビジネススーツ', 35000, 15, 3),
('有機コーヒー豆', 1200, 100, 4),
('オリーブオイル', 2200, 45, 4),
('ダークチョコレート', 800, 80, 4),
('プログラミング入門書', 3500, 30, 5),
('小説集', 1500, 50, 5),
('ビジネス戦略本', 2800, 25, 5);

-- サンプルデータの挿入：顧客
INSERT INTO customers (name, email, city) VALUES
('山田太郎', 'yamada@example.com', '東京'),
('佐藤花子', 'sato@example.com', '大阪'),
('鈴木一郎', 'suzuki@example.com', '東京'),
('田中順子', 'tanaka@example.com', '名古屋'),
('伊藤健太', 'ito@example.com', '福岡'),
('渡辺美咲', 'watanabe@example.com', '大阪'),
('高橋誠', 'takahashi@example.com', '東京');

-- サンプルデータの挿入：注文
INSERT INTO orders (customer_id, order_date, status) VALUES
(1, '2025-03-01 10:30:00', 'delivered'),
(2, '2025-03-03 14:45:00', 'shipped'),
(3, '2025-03-05 09:15:00', 'delivered'),
(4, '2025-03-08 16:20:00', 'processing'),
(1, '2025-03-10 11:05:00', 'delivered'),
(3, '2025-03-12 13:40:00', 'shipped'),
(5, '2025-03-15 10:00:00', 'pending'),
(6, '2025-03-18 15:30:00', 'delivered'),
(2, '2025-03-20 12:15:00', 'shipped'),
(7, '2025-04-01 09:45:00', 'processing'),
(3, '2025-04-03 14:20:00', 'pending'),
(1, '2025-04-05 10:30:00', 'processing');

-- サンプルデータの挿入：注文詳細
INSERT INTO order_items (order_id, product_id, quantity, price) VALUES
(1, 1, 1, 89000), -- 山田注文1: スマートフォン
(1, 3, 1, 15000), -- 山田注文1: イヤホン
(2, 10, 1, 35000), -- 佐藤注文1: ビジネススーツ
(2, 8, 2, 4500), -- 佐藤注文1: カジュアルシャツ2枚
(3, 2, 1, 120000), -- 鈴木注文1: ノートパソコン
(3, 4, 1, 8500), -- 鈴木注文1: スピーカー
(4, 14, 1, 3500), -- 田中注文1: プログラミング本
(4, 15, 2, 1500), -- 田中注文1: 小説2冊
(5, 5, 1, 35000), -- 山田注文2: テーブル
(6, 11, 2, 1200), -- 鈴木注文2: コーヒー豆2袋
(6, 13, 3, 800), -- 鈴木注文2: チョコレート3個
(7, 9, 1, 6500), -- 伊藤注文1: デニムパンツ
(7, 12, 1, 2200), -- 伊藤注文1: オリーブオイル
(8, 7, 1, 18000), -- 渡辺注文1: 本棚
(8, 15, 1, 1500), -- 渡辺注文1: 小説
(9, 3, 1, 15000), -- 佐藤注文2: イヤホン
(10, 6, 1, 12000), -- 高橋注文1: オフィスチェア
(11, 16, 1, 2800), -- 鈴木注文3: ビジネス本
(12, 4, 1, 8500); -- 山田注文3: スピーカー
```

### 1. 基本的な集計

#### 問題 1-1: シンプルな集計クエリ

以下の集計クエリを作成してください。

1. すべての商品の総数を取得する
2. 商品の平均価格、最高価格、最低価格を求める
3. 注文の総数と総注文金額（注文明細の総額）を計算する

**ヒント**:

* 集計関数 `COUNT()`, `AVG()`, `MAX()`, `MIN()`, `SUM()` を使います
* 複数テーブルの値を集計する場合は、テーブルを結合する必要があります

\<details> \<summary>解答例\</summary>

```sql
-- 1. すべての商品の総数
SELECT COUNT(*) AS 商品総数 FROM products;

-- 2. 商品の価格統計
SELECT 
    ROUND(AVG(price), 0) AS 平均価格,
    MAX(price) AS 最高価格,
    MIN(price) AS 最低価格
FROM products;

-- 3. 注文の総数と総注文金額
SELECT 
    COUNT(DISTINCT o.id) AS 注文総数,
    SUM(oi.quantity * oi.price) AS 総注文金額
FROM 
    orders o,
    order_items oi
WHERE 
    o.id = oi.order_id;
```

\</details>

#### 問題 1-2: 条件付き集計

以下の条件付き集計クエリを作成してください。

1. カテゴリが「電子機器」の商品の平均価格を求める
2. 在庫が10個以下の商品の数を求める
3. 「delivered」ステータスの注文の総数と総額を計算する

**ヒント**:

* 集計前にデータを絞り込むには `WHERE` 句を使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. 電子機器カテゴリの平均価格
SELECT 
    ROUND(AVG(p.price), 0) AS 電子機器平均価格
FROM 
    products p,
    categories c
WHERE 
    p.category_id = c.id
    AND c.name = '電子機器';

-- 2. 在庫が10個以下の商品数
SELECT 
    COUNT(*) AS 在庫僅少商品数
FROM products
WHERE stock <= 10;

-- 3. 納品済み注文の集計
SELECT 
    COUNT(o.id) AS 納品済み注文数,
    SUM(oi.quantity * oi.price) AS 納品済み注文総額
FROM 
    orders o,
    order_items oi
WHERE 
    o.id = oi.order_id
    AND o.status = 'delivered';
```

\</details>

### 2. グループ別の集計

#### 問題 2-1: カテゴリ別の集計

以下のカテゴリ別の集計クエリを作成してください。

1. カテゴリごとの商品数を求める
2. カテゴリごとの平均価格を求め、平均価格の高い順に表示する
3. カテゴリごとの在庫総数を求める

**ヒント**:

* グループ別に集計するには `GROUP BY` 句を使います
* 結果を並べ替えるには `ORDER BY` 句を使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. カテゴリごとの商品数
SELECT 
    c.name AS カテゴリ名,
    COUNT(p.id) AS 商品数
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
ORDER BY 
    商品数 DESC;

-- 2. カテゴリごとの平均価格（高い順）
SELECT 
    c.name AS カテゴリ名,
    ROUND(AVG(p.price), 0) AS 平均価格
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
ORDER BY 
    平均価格 DESC;

-- 3. カテゴリごとの在庫総数
SELECT 
    c.name AS カテゴリ名,
    SUM(p.stock) AS 在庫総数
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
ORDER BY 
    在庫総数 DESC;
```

\</details>

#### 問題 2-2: 顧客別の注文集計

以下の顧客別の集計クエリを作成してください。

1. 顧客ごとの注文回数を求め、注文回数の多い順に表示する
2. 顧客ごとの総購入金額を求め、購入金額の多い順に表示する
3. 各顧客の平均注文金額（1注文あたりの金額）を計算する

**ヒント**:

* 顧客情報と注文情報、さらに注文詳細情報を結合する必要があります
* 平均注文金額は「総購入金額÷注文回数」で計算できます

\<details> \<summary>解答例\</summary>

```sql
-- 1. 顧客ごとの注文回数（多い順）
SELECT 
    c.name AS 顧客名,
    COUNT(o.id) AS 注文回数
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
GROUP BY 
    c.id
ORDER BY 
    注文回数 DESC;

-- 2. 顧客ごとの総購入金額（多い順）
SELECT 
    c.name AS 顧客名,
    COALESCE(SUM(oi.quantity * oi.price), 0) AS 総購入金額
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.id
ORDER BY 
    総購入金額 DESC;

-- 3. 顧客ごとの平均注文金額
SELECT 
    c.name AS 顧客名,
    COUNT(DISTINCT o.id) AS 注文回数,
    COALESCE(SUM(oi.quantity * oi.price), 0) AS 総購入金額,
    CASE 
        WHEN COUNT(DISTINCT o.id) > 0 THEN ROUND(SUM(oi.quantity * oi.price) / COUNT(DISTINCT o.id), 0)
        ELSE 0 
    END AS 平均注文金額
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.id
ORDER BY 
    平均注文金額 DESC;
```

\</details>

#### 問題 2-3: 地域別の売上分析

以下の地域（都市）別の売上分析クエリを作成してください。

1. 都市ごとの顧客数を表示する
2. 都市ごとの総注文数と総売上金額を計算する
3. 都市ごとの顧客1人あたり平均購入金額を計算する

**ヒント**:

* 都市情報は顧客テーブルの `city` 列にあります
* 都市ごとにグループ化するには `GROUP BY customers.city` を使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. 都市ごとの顧客数
SELECT 
    city AS 都市,
    COUNT(*) AS 顧客数
FROM 
    customers
GROUP BY 
    city
ORDER BY 
    顧客数 DESC;

-- 2. 都市ごとの総注文数と総売上金額
SELECT 
    c.city AS 都市,
    COUNT(DISTINCT o.id) AS 総注文数,
    SUM(oi.quantity * oi.price) AS 総売上金額
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.city
ORDER BY 
    総売上金額 DESC;

-- 3. 都市ごとの顧客1人あたり平均購入金額
SELECT 
    c.city AS 都市,
    COUNT(DISTINCT c.id) AS 顧客数,
    COALESCE(SUM(oi.quantity * oi.price), 0) AS 総売上金額,
    ROUND(COALESCE(SUM(oi.quantity * oi.price), 0) / COUNT(DISTINCT c.id), 0) AS 顧客あたり平均購入金額
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.city
ORDER BY 
    顧客あたり平均購入金額 DESC;
```

\</details>

### 3. HAVINGによる集計結果のフィルタリング

#### 問題 3-1: 条件を満たすグループの抽出

以下のHAVING句を使用した集計クエリを作成してください。

1. 平均価格が10,000円以上のカテゴリを抽出する
2. 合計3回以上注文した顧客を抽出する
3. 総売上が50,000円以上の都市を抽出する

**ヒント**:

* 集計結果に対して条件を適用するには `HAVING` 句を使います
* `WHERE` は集計前の行に対する条件、`HAVING` は集計後のグループに対する条件です

\<details> \<summary>解答例\</summary>

```sql
-- 1. 平均価格が10,000円以上のカテゴリ
SELECT 
    c.name AS カテゴリ名,
    ROUND(AVG(p.price), 0) AS 平均価格
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
HAVING 
    AVG(p.price) >= 10000
ORDER BY 
    平均価格 DESC;

-- 2. 合計3回以上注文した顧客
SELECT 
    c.name AS 顧客名,
    COUNT(o.id) AS 注文回数
FROM 
    customers c,
    orders o
WHERE 
    c.id = o.customer_id
GROUP BY 
    c.id
HAVING 
    COUNT(o.id) >= 3
ORDER BY 
    注文回数 DESC;

-- 3. 総売上が50,000円以上の都市
SELECT 
    c.city AS 都市,
    SUM(oi.quantity * oi.price) AS 総売上金額
FROM 
    customers c,
    orders o,
    order_items oi
WHERE 
    c.id = o.customer_id
    AND o.id = oi.order_id
GROUP BY 
    c.city
HAVING 
    SUM(oi.quantity * oi.price) >= 50000
ORDER BY 
    総売上金額 DESC;
```

\</details>

#### 問題 3-2: 複合条件による集計結果のフィルタリング ⭐️

以下の複合条件を使用した集計クエリを作成してください。

1. 商品数が3つ以上あり、かつ平均価格が5,000円以上のカテゴリを抽出する
2. 合計金額が100,000円以上、または平均注文金額が30,000円以上の顧客を抽出する
3. 顧客数が2人以上で、総売上が100,000円以上の都市を抽出する

**ヒント**:

* `HAVING` 句内でも `AND` や `OR` などの論理演算子が使用できます
* 複数の集計関数を組み合わせることができます

\<details> \<summary>解答例\</summary>

```sql
-- 1. 商品数が3つ以上あり、平均価格が5,000円以上のカテゴリ
SELECT 
    c.name AS カテゴリ名,
    COUNT(p.id) AS 商品数,
    ROUND(AVG(p.price), 0) AS 平均価格
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
HAVING 
    COUNT(p.id) >= 3
    AND AVG(p.price) >= 5000
ORDER BY 
    平均価格 DESC;

-- 2. 合計金額が100,000円以上、または平均注文金額が30,000円以上の顧客
SELECT 
    c.name AS 顧客名,
    COUNT(DISTINCT o.id) AS 注文回数,
    SUM(oi.quantity * oi.price) AS 合計金額,
    ROUND(SUM(oi.quantity * oi.price) / COUNT(DISTINCT o.id), 0) AS 平均注文金額
FROM 
    customers c,
    orders o,
    order_items oi
WHERE 
    c.id = o.customer_id
    AND o.id = oi.order_id
GROUP BY 
    c.id
HAVING 
    SUM(oi.quantity * oi.price) >= 100000
    OR (SUM(oi.quantity * oi.price) / COUNT(DISTINCT o.id)) >= 30000
ORDER BY 
    合計金額 DESC;

-- 3. 顧客数が2人以上で、総売上が100,000円以上の都市
SELECT 
    c.city AS 都市,
    COUNT(DISTINCT c.id) AS 顧客数,
    SUM(oi.quantity * oi.price) AS 総売上金額
FROM 
    customers c,
    orders o,
    order_items oi
WHERE 
    c.id = o.customer_id
    AND o.id = oi.order_id
GROUP BY 
    c.city
HAVING 
    COUNT(DISTINCT c.id) >= 2
    AND SUM(oi.quantity * oi.price) >= 100000
ORDER BY 
    総売上金額 DESC;
```

\</details>

### 4. 高度な計算と条件式

#### 問題 4-1: 計算フィールドの使用

以下の計算フィールドを使用したクエリを作成してください。

1. 各商品の「総在庫価値」（価格×在庫数）を計算し、価値の高い順に表示する
2. 各注文の「総注文金額」を計算し、注文日と顧客名も表示する
3. 各商品の「価格帯」（5,000円未満、5,000円以上30,000円未満、30,000円以上）を決定し、価格帯ごとの商品数を表示する

**ヒント**:

* 計算フィールドは SELECT 句で式として定義できます
* 価格帯のような条件分岐には `CASE` 式を使用します

\<details> \<summary>解答例\</summary>

```sql
-- 1. 商品の総在庫価値
SELECT 
    p.name AS 商品名,
    p.price AS 単価,
    p.stock AS 在庫数,
    p.price * p.stock AS 総在庫価値
FROM 
    products p
ORDER BY 
    総在庫価値 DESC;

-- 2. 各注文の総注文金額
SELECT 
    o.id AS 注文ID,
    o.order_date AS 注文日,
    c.name AS 顧客名,
    SUM(oi.quantity * oi.price) AS 総注文金額
FROM 
    orders o,
    customers c,
    order_items oi
WHERE 
    o.customer_id = c.id
    AND o.id = oi.order_id
GROUP BY 
    o.id
ORDER BY 
    o.order_date DESC;

-- 3. 価格帯ごとの商品数
SELECT 
    CASE 
        WHEN price < 5000 THEN '低価格（5,000円未満）'
        WHEN price < 30000 THEN '中価格（5,000円以上30,000円未満）'
        ELSE '高価格（30,000円以上）'
    END AS 価格帯,
    COUNT(*) AS 商品数,
    ROUND(AVG(price), 0) AS 平均価格
FROM 
    products
GROUP BY 
    価格帯
ORDER BY 
    平均価格;
```

\</details>

#### 問題 4-2: CASE式による条件分岐 ⭐️

以下のCASE式を使用した条件分岐クエリを作成してください。

1. 各商品の「在庫状況」（「在庫なし」、「残りわずか（10個以下）」、「在庫あり」）を表示する
2. 各カテゴリの「価格帯評価」（平均価格が30,000円以上なら「高級」、10,000円以上なら「中級」、それ以外は「一般」）を表示する
3. 各顧客の「購入者ランク」（総購入額が200,000円以上なら「VIP」、100,000円以上なら「上級」、50,000円以上なら「一般」、それ以外は「新規」）を表示する

**ヒント**:

* 複雑な条件分岐には `CASE WHEN 条件 THEN 値 ... ELSE 値 END` の構文を使います
* 集計結果に対する条件分岐も可能です

\<details> \<summary>解答例\</summary>

```sql
-- 1. 商品の在庫状況
SELECT 
    p.name AS 商品名,
    p.stock AS 在庫数,
    CASE 
        WHEN p.stock = 0 THEN '在庫なし'
        WHEN p.stock <= 10 THEN '残りわずか'
        ELSE '在庫あり'
    END AS 在庫状況
FROM 
    products p
ORDER BY 
    p.stock;

-- 2. カテゴリの価格帯評価
SELECT 
    c.name AS カテゴリ名,
    ROUND(AVG(p.price), 0) AS 平均価格,
    CASE 
        WHEN AVG(p.price) >= 30000 THEN '高級'
        WHEN AVG(p.price) >= 10000 THEN '中級'
        ELSE '一般'
    END AS 価格帯評価
FROM 
    categories c,
    products p
WHERE 
    p.category_id = c.id
GROUP BY 
    c.id
ORDER BY 
    平均価格 DESC;

-- 3. 顧客の購入者ランク
SELECT 
    c.name AS 顧客名,
    COALESCE(SUM(oi.quantity * oi.price), 0) AS 総購入額,
    CASE 
        WHEN SUM(oi.quantity * oi.price) >= 200000 THEN 'VIP'
        WHEN SUM(oi.quantity * oi.price) >= 100000 THEN '上級'
        WHEN SUM(oi.quantity * oi.price) >= 50000 THEN '一般'
        ELSE '新規'
    END AS 購入者ランク
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.id
ORDER BY 
    総購入額 DESC;
```

\</details>

### 5. 複雑なデータ分析 ⭐️

#### 問題 5-1: 製品人気度分析

以下の製品人気度分析クエリを作成してください。

1. 販売数量が多い上位5つの商品を、販売数量と売上金額とともに表示する
2. カテゴリ別の商品の売れ行きを表示する（各カテゴリの商品ごとの販売数量）
3. 売上金額と注文数から「人気度スコア」（売上金額×0.7 + 注文数×10000×0.3）を計算し、スコアの高い順に商品を表示する

**ヒント**:

* GROUP BY句を使って製品ごとや、カテゴリごとに集計します
* 計算フィールドを使って人気度スコアを算出します
* ORDER BYとLIMITを組み合わせて上位商品を表示します

\<details> \<summary>解答例\</summary>

```sql
-- 1. 販売数量が多い上位5商品
SELECT 
    p.name AS 商品名,
    SUM(oi.quantity) AS 販売数量,
    SUM(oi.quantity * oi.price) AS 売上金額
FROM 
    products p,
    order_items oi
WHERE 
    p.id = oi.product_id
GROUP BY 
    p.id
ORDER BY 
    販売数量 DESC
LIMIT 5;

-- 2. カテゴリ別の商品の売れ行き
SELECT 
    c.name AS カテゴリ名,
    p.name AS 商品名,
    SUM(oi.quantity) AS 販売数量
FROM 
    categories c,
    products p,
    order_items oi
WHERE 
    c.id = p.category_id
    AND p.id = oi.product_id
GROUP BY 
    c.id, p.id
ORDER BY 
    c.name, 販売数量 DESC;

-- 3. 人気度スコアによるランキング
SELECT 
    p.name AS 商品名,
    c.name AS カテゴリ,
    COUNT(DISTINCT oi.order_id) AS 注文数,
    SUM(oi.quantity) AS 販売数量,
    SUM(oi.quantity * oi.price) AS 売上金額,
    ROUND(SUM(oi.quantity * oi.price) * 0.7 + COUNT(DISTINCT oi.order_id) * 10000 * 0.3, 0) AS 人気度スコア
FROM 
    products p,
    categories c,
    order_items oi
WHERE 
    p.category_id = c.id
    AND p.id = oi.product_id
GROUP BY 
    p.id
ORDER BY 
    人気度スコア DESC
LIMIT 10;
```

\</details>

#### 問題 5-2: 顧客行動分析 ⭐️

以下の顧客行動分析クエリを作成してください。

1. 各顧客が購入したカテゴリ別の商品数を表示する
2. 顧客ごとの最初の注文日と最新の注文日、注文回数を表示する
3. 「最も価値の高い顧客」を特定する（総購入額、購入頻度、平均注文金額を考慮した簡易スコア算出）

**ヒント**:

* カテゴリごとのグループ化や顧客ごとのグループ化を組み合わせる必要があります
* 日付の最小値と最大値は `MIN()` と `MAX()` 関数で取得できます
* 複数の指標を組み合わせたスコアを計算するには、集計結果を計算式に組み込みます

\<details> \<summary>解答例\</summary>

```sql
-- 1. 各顧客が購入したカテゴリ別の商品数
SELECT 
    c.name AS 顧客名,
    cat.name AS カテゴリ名,
    COUNT(DISTINCT p.id) AS 購入商品数,
    SUM(oi.quantity) AS 購入数量
FROM 
    customers c,
    orders o,
    order_items oi,
    products p,
    categories cat
WHERE 
    c.id = o.customer_id
    AND o.id = oi.order_id
    AND oi.product_id = p.id
    AND p.category_id = cat.id
GROUP BY 
    c.id, cat.id
ORDER BY 
    c.name, 購入数量 DESC;

-- 2. 顧客ごとの注文履歴サマリ
SELECT 
    c.name AS 顧客名,
    MIN(o.order_date) AS 最初の注文日,
    MAX(o.order_date) AS 最新の注文日,
    COUNT(DISTINCT o.id) AS 注文回数,
    SUM(oi.quantity * oi.price) AS 総購入額
FROM 
    customers c
LEFT JOIN 
    orders o ON c.id = o.customer_id
LEFT JOIN 
    order_items oi ON o.id = oi.order_id
GROUP BY 
    c.id
ORDER BY 
    総購入額 DESC;

-- 3. 顧客価値スコア
SELECT 
    c.name AS 顧客名,
    COUNT(DISTINCT o.id) AS 注文回数,
    SUM(oi.quantity * oi.price) AS 総購入額,
    ROUND(SUM(oi.quantity * oi.price) / COUNT(DISTINCT o.id), 0) AS 平均注文金額,
    ROUND(
        (SUM(oi.quantity * oi.price) * 0.5) + 
        (COUNT(DISTINCT o.id) * 10000 * 0.3) + 
        (SUM(oi.quantity * oi.price) / COUNT(DISTINCT o.id) * 0.2), 
        0
    ) AS 顧客価値スコア
FROM 
    customers c,
    orders o,
    order_items oi
WHERE 
    c.id = o.customer_id
    AND o.id = oi.order_id
GROUP BY 
    c.id
ORDER BY 
    顧客価値スコア DESC
LIMIT 3;
```

\</details>

### まとめ

この実践問題を通じて、SQL集計機能を使用したデータ分析のスキルを身につけることができました。以下のような重要なSQLスキルを習得しています：

* 集計関数（COUNT, SUM, AVG, MAX, MIN）の使用
* GROUP BY句によるデータのグループ化
* HAVING句によるグループのフィルタリング
* 計算フィールドとCASE式による条件分岐
* 複雑なデータ分析クエリの作成

これらのスキルは、実際のビジネスシーンでのデータ分析や意思決定に不可欠なものです。引き続きSQLを学び、より高度なデータベース操作や分析を行えるようになりましょう。
