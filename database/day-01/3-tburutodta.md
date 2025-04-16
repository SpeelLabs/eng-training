---
description: >-
  この章では、データベースの作成からテーブルの設計・作成、そしてデータの追加までの一連の操作を学びます。これらはデータ検索 (SELECT)
  の前に必要な準備段階です。実際に手を動かしながら、データベース操作の基本を身につけましょう。
---

# 3. テーブル作成とデータ操作

### 3.1 データベースの作成と選択

データベースは複数のテーブルをまとめて格納・保管する場所です。実際にデータベースを作成し、操作していきましょう。

#### データベース名の付け方

データベース名は、システムの内容や目的を反映した、わかりやすい名前にしましょう。

<table><thead><tr><th width="264.4000244140625">システム種類</th><th>データベース名の例</th></tr></thead><tbody><tr><td><strong>オンラインショップシステム</strong></td><td><code>online_shop</code>, <code>ecommerce_db</code>, <code>web_store</code></td></tr><tr><td><strong>学校管理システム</strong></td><td><code>school_management</code>, <code>education_db</code>, <code>campus_system</code></td></tr><tr><td><strong>図書館管理システム</strong></td><td><code>library_management</code>, <code>books_db</code>, <code>reading_center</code></td></tr></tbody></table>

> 📝 **ポイント**: データベース名は、単語の区切りにアンダースコア ( \_ ) を使うことが多いです。すべて小文字で統一するのが一般的です。

#### データベースの作成

新しいデータベースを作成するには、`CREATE DATABASE` コマンドを使います。

```sql
-- 新しいデータベースを作成
CREATE DATABASE online_shop;
```

<details>

<summary>実行結果</summary>

```
OK, 1 row affected in 22.679ms
```

</details>

同名のデータベースが既に存在する場合にエラーを避けるには、`IF NOT EXISTS`を追加します。

```sql
-- 既に存在する場合はエラーを出さない
CREATE DATABASE IF NOT EXISTS online_shop;
```

#### データベースの一覧表示

サーバー上のデータベース一覧を表示するには、

```sql
SHOW DATABASES;
```

<details>

<summary>実行結果</summary>

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| online_shop        |
| performance_schema |
| sys                |
+--------------------+
OK, 7 records retrieved in 1.299ms
```

</details>

> 📝 **ポイント**: `information_schema`, `mysql`, `performance_schema`, `sys` はMySQLのシステムデータベースです。通常、これらは直接操作しません。

#### データベースの選択

作成したデータベースを使用するには、`USE` コマンドで選択します。

```sql
-- データベースを選択
USE online_shop;
```

<details>

<summary>実行結果</summary>

```
OK, 0 records retrieved in 0.732ms
```

</details>

現在選択しているデータベースを確認するには、

```sql
SELECT DATABASE();
```

<details>

<summary>実行結果</summary>

```
+-------------+
| DATABASE()  |
+-------------+
| online_shop |
+-------------+
OK, 1 record retrieved in 0.621ms
```

</details>

> 📝 **ポイント**: 作業を始める前に必ず 「どのデータベースを使っているか」 を確認する習慣をつけましょう。

### 3.2 テーブルの作成

データベースを作成したら、次はその中にテーブルを作成します。これがデータを格納する実際の「表」になります。

#### 基本的なデータ型

テーブル作成時に使用する主な基本データ型は以下の通りです。

| データ型       | 説明             | 使用例          |
| ---------- | -------------- | ------------ |
| INT        | 整数値            | ID、年齢、数量など   |
| VARCHAR(n) | 可変長文字列（最大n文字）  | 名前、メールアドレスなど |
| TEXT       | 長いテキスト         | 商品説明、記事本文など  |
| DATE       | 日付（YYYY-MM-DD） | 生年月日、予約日など   |

> 📝 **ポイント**: 実際にはもっと多くのデータ型がありますが、初めは上記の基本的なものだけで十分です。

#### シンプルなテーブルの作成

テーブルを作成するには、`CREATE TABLE` コマンドを使います。基本的な構文は以下の通りです。

```sql
CREATE TABLE テーブル名 (
    カラム名1 データ型 [制約],
    カラム名2 データ型 [制約],
    ...
);
```

実際の例を見てみましょう。

```sql
-- 商品カテゴリのテーブルを作成
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);
```

<details>

<summary>実行結果</summary>

```
OK, 0 records retrieved in 41.247ms
```

</details>

次に、商品テーブルを作成します。

```sql
-- 商品テーブルを作成_
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    stock INT DEFAULT 0,
    category_id INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

<details>

<summary>実行結果</summary>

```
OK, 0 records retrieved in 52.562ms
```

</details>

> 📝 **ポイント**: 上記の例では、`products` テーブルの `category_id` 列が `categories` テーブルの`id` 列を参照しています。これを 「外部キー」 と呼び、テーブル同士の関連を表します。

#### 主な制約

テーブル作成時には以下の基本的な制約が使えます。

<table><thead><tr><th width="181.5999755859375">制約</th><th>説明</th><th>例</th></tr></thead><tbody><tr><td>PRIMARY KEY</td><td>主キー (一意の識別子)</td><td><code>id INT PRIMARY KEY</code></td></tr><tr><td>FOREIGN KEY</td><td>外部キー (他のテーブルを参照)</td><td><code>FOREIGN KEY (category_id) REFERENCES categories(id)</code></td></tr><tr><td>NOT NULL</td><td>NULL値を許可しない</td><td><code>name VARCHAR(100) NOT NULL</code></td></tr><tr><td>DEFAULT</td><td>デフォルト値を設定</td><td><code>stock INT DEFAULT 0</code></td></tr><tr><td>AUTO_INCREMENT</td><td>自動的に増加する値</td><td><code>id INT AUTO_INCREMENT</code></td></tr></tbody></table>

#### テーブル一覧と構造の確認

データベース内のテーブル一覧を表示するには、

```sql
SHOW TABLES;
```

<details>

<summary>実行結果</summary>

```
+----------------------+
| Tables_in_online_shop|
+----------------------+
| categories           |
| products             |
+----------------------+
OK, 2 records retrieved in 1.647ms
```

</details>

テーブルの構造を確認するには、

```sql
DESCRIBE products;
-- または
DESC products;
```

<details>

<summary>実行結果</summary>

```
+-------------+--------------+------+-----+---------+----------------+
| Field       | Type         | Null | Key | Default | Extra          |
+-------------+--------------+------+-----+---------+----------------+
| id          | int          | NO   | PRI | NULL    | auto_increment |
| name        | varchar(100) | NO   |     | NULL    |                |
| price       | int          | NO   |     | NULL    |                |
| stock       | int          | YES  |     | 0       |                |
| category_id | int          | YES  | MUL | NULL    |                |
+-------------+--------------+------+-----+---------+----------------+
OK, 5 records retrieved in 3.565ms
```

</details>

### 3.3 データの追加 (INSERT)

テーブルを作成したら、次はデータを追加します。データを追加するには`INSERT INTO`コマンドを使用します。

#### INSERT 文の基本構文

```sql
INSERT INTO テーブル名 (カラム1, カラム2, ...)
VALUES (値1, 値2, ...);
```

#### 単一レコードの挿入

まずはカテゴリテーブルにデータを入れてみましょう。

```sql
-- カテゴリを追加
INSERT INTO categories (name) 
VALUES ('電子機器');
```

<details>

<summary>実行結果</summary>

```
OK, 1 row affected in 9.489ms
```

</details>

続いて別のカテゴリも追加します。

```sql
INSERT INTO categories (name)
VALUES ('家具');
```

<details>

<summary>実行結果</summary>

```
OK, 1 row affected in 10.239ms
```

</details>

> 📝 **ポイント**: `AUTO_INCREMENT` が設定された `id` カラムには値を指定する必要がありません。MySQL が自動的に値を割り当てます。

次に商品テーブルにデータを追加します。

```sql
-- 商品を追加（category_idは既に存在するカテゴリのIDを指定）
INSERT INTO products (name, price, stock, category_id)
VALUES ('スマートフォン', 89000, 25, 1);
```

<details>

<summary>実行結果</summary>

```
OK, 1 row affected in 16.993ms
```

</details>

#### 複数レコードの一括挿入

複数のレコードを一度に挿入することもできます。

```sql
-- 複数の商品を一括挿入
INSERT INTO products (name, price, stock, category_id)
VALUES 
    ('ノートパソコン', 120000, 15, 1),
    ('木製テーブル', 35000, 5, 2),
    ('オフィスチェア', 12000, 20, 2);
```

<details>

<summary>実行結果</summary>

```
OK, 3 rows affected in 13.01ms
```

</details>

#### 挿入したデータの確認

データを挿入したら、正しく追加されたか確認しましょう。

```sql
-- カテゴリを確認
SELECT * FROM categories;
```

<details>

<summary>実行結果</summary>

```
+----+----------+
| id | name     |
+----+----------+
|  1 | 電子機器  |
|  2 | 家具     |
+----+----------+
OK, 2 records retrieved in 0.799ms
```

</details>

```sql
-- 商品を確認
SELECT * FROM products;
```

<details>

<summary>実行結果</summary>

```
+----+--------------------+--------+-------+-------------+
| id | name               | price  | stock | category_id |
+----+--------------------+--------+-------+-------------+
|  1 | スマートフォン       |  89000 |    25 |           1 |
|  2 | ノートパソコン       | 120000 |    15 |           1 |
|  3 | 木製テーブル         |  35000 |     5 |           2 |
|  4 | オフィスチェア       |  12000 |    20 |           2 |
+----+--------------------+--------+-------+-------------+
OK, 4 records retrieved in 0.747ms
```

</details>

### 3.4 テーブル設計の実践

実際のテーブル設計では、情報を適切に整理することが重要です。

#### テーブルの分割

情報を適切に分割することで、データの管理が容易になります。例えば、以下のような 「 1つの表に全部入れた場合」 は問題があります。

**注文情報 (全部一つのテーブル)**

<table><thead><tr><th width="75.60000610351562">注文ID</th><th width="97.5999755859375">顧客名</th><th width="86.800048828125">顧客住所</th><th width="143.9998779296875">商品名</th><th width="96.4000244140625">商品価格</th><th width="69.99993896484375">数量</th></tr></thead><tbody><tr><td>1</td><td>山田太郎</td><td>東京都...</td><td>スマートフォン</td><td>89000</td><td>1</td></tr><tr><td>2</td><td>山田太郎</td><td>東京都...</td><td>ノートパソコン</td><td>120000</td><td>1</td></tr><tr><td>3</td><td>佐藤花子</td><td>大阪府...</td><td>スマートフォン</td><td>89000</td><td>2</td></tr></tbody></table>

これを複数のテーブルに分割することで、データの重複を減らし、管理が容易になります。

**顧客テーブル**

<table><thead><tr><th width="121">顧客ID</th><th>顧客名</th><th>顧客住所</th></tr></thead><tbody><tr><td>1</td><td>山田太郎</td><td>東京都...</td></tr><tr><td>2</td><td>佐藤花子</td><td>大阪府...</td></tr></tbody></table>

**商品テーブル**

<table><thead><tr><th width="120.20001220703125">商品ID</th><th>商品名</th><th>価格</th></tr></thead><tbody><tr><td>1</td><td>スマートフォン</td><td>89000</td></tr><tr><td>2</td><td>ノートパソコン</td><td>120000</td></tr></tbody></table>

**注文テーブル**

<table><thead><tr><th width="90">注文ID</th><th width="90.4000244140625">顧客ID</th><th width="91.60003662109375">商品ID</th><th>数量</th><th>注文日</th></tr></thead><tbody><tr><td>1</td><td>1</td><td>1</td><td>1</td><td>2023-01-15</td></tr><tr><td>2</td><td>1</td><td>2</td><td>1</td><td>2023-01-20</td></tr><tr><td>3</td><td>2</td><td>1</td><td>2</td><td>2023-01-25</td></tr></tbody></table>

> 📝 **ポイント**: このようにテーブルを分割することで、データの重複が減り、更新や管理が容易になります。また、外部キーを使ってテーブル間の関連性を保つことができます。

#### テーブル設計の練習

実際のアプリケーション開発では、設計段階でテーブル構造を考えることが重要です。例えば、簡単なブログシステムのテーブル設計を考えてみましょう。

**authors テーブル (著者情報)**

```sql
CREATE TABLE authors (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE
);
```

**articles テーブル (記事情報)**

```sql
CREATE TABLE articles (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT,
    author_id INT,
    published_date DATE,
    FOREIGN KEY (author_id) REFERENCES authors(id)
);
```

**comments テーブル (コメント情報)**

```sql
CREATE TABLE comments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    article_id INT,
    name VARCHAR(50),
    content TEXT,
    comment_date DATETIME,
    FOREIGN KEY (article_id) REFERENCES articles(id)
);
```

> 📝 **ポイント**: このような設計を行うことで、「記事には複数のコメントがつけられる」 「一人の著者が複数の記事を書ける」 といった関係性を表現できます。



***

### 演習問題

#### 問題 3-1: シンプルなデータベースとテーブルの作成

1. `mini_shop` という名前のデータベースを作成し、使用するように設定してください。
2. 以下の列を持つ `users` テーブルを作成してください。
   * `id`: 整数型、主キー、自動採番
   * `name`: 文字列型 (50文字まで)、NULL 禁止
   * `email`: 文字列型 (100文字まで)
3. 以下の列を持つ `items` テーブルを作成してください。
   * `id`: 整数型、主キー、自動採番
   * `name`: 文字列型 (100文字まで)、NULL 禁止
   * `price`: 整数型、NULL 禁止
   * `stock`: 整数型、デフォルト値は 0

<details>

<summary>解答例</summary>

```sql
-- 1. データベースの作成
CREATE DATABASE mini_shop;

-- データベースの選択
USE mini_shop;

-- 2. ユーザーテーブルの作成
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100)
);

-- 3. 商品テーブルの作成
CREATE TABLE items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    stock INT DEFAULT 0
);
```



</details>

#### 問題 3-2: データの追加

1. `users` テーブルに少なくとも 2名のユーザーデータを追加してください。
2. `items` テーブルに少なくとも 3つの商品データを追加してください。

<details>

<summary>解答例</summary>

```sql
-- ユーザーデータの追加
INSERT INTO users (name, email)
VALUES 
('山田太郎', 'yamada@example.com'),
('佐藤花子', 'sato@example.com');

-- 商品データの追加
INSERT INTO items (name, price, stock)
VALUES 
('Tシャツ', 2500, 50),
('ジーンズ', 5000, 30),
('スニーカー', 8000, 20);
```



</details>

#### 問題 3-3: データの確認

1. ユーザーテーブルのすべてのデータを表示してください。
2. 商品テーブルのすべてのデータを表示してください。
3. 価格が5000円以上の商品だけを表示してください。

<details>

<summary>解答例</summary>

```sql
-- 1. ユーザーテーブルのすべてのデータを表示
SELECT * FROM users;

-- 2. 商品テーブルのすべてのデータを表示
SELECT * FROM items;

-- 3. 価格が5000円以上の商品だけを表示
SELECT * FROM items WHERE price >= 5000;
```

</details>

#### 問題 3-4: テーブル設計の考察

あなたはブログ投稿システムを設計することになりました。以下の情報を管理するために必要なテーブルと、それぞれの主なカラムを考えてください。

情報:

* 投稿者 (名前、メールアドレスなど)
* ブログ記事 (タイトル、内容、投稿日など)
* カテゴリ (カテゴリ名)
* コメント (コメント内容、投稿者、日時など)

<details>

<summary>解答例</summary>

以下のようなテーブル設計が考えられます：

1. **authorsテーブル (**&#x6295;稿者情報)
   * id (主キー)
   * name (名前、NOT NULL)
   * email (メールアドレス)
   * join\_date (登録日)
2. **categoriesテーブル (**&#x30AB;テゴリ情報)
   * id (主キー)
   * name (カテゴリ名、NOT NULL)
3. **articlesテーブル (**&#x30D6;ログ記事)
   * id (主キー)
   * title (タイトル、NOT NULL)
   * content (記事内容、TEXT型)
   * author\_id (外部キー、投稿者のID)
   * category\_id (外部キー、カテゴリのID)
   * publish\_date (投稿日)
4. **commentsテーブル (**&#x30B3;メント)
   * id (主キー)
   * article\_id (外部キー、記事のID)
   * author\_name (コメント投稿者名)
   * content (コメント内容)
   * comment\_date (コメント日時)

このテーブル設計の特徴：

* 各テーブルは明確な一つの概念を表している
* 記事は投稿者とカテゴリに関連付けられている
* コメントは記事に関連付けられている

</details>
