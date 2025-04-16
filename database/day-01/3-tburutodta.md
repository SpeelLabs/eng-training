---
description: >-
  この章では、データベースの作成からテーブルの設計・作成、そしてデータの追加までの一連の操作を学びます。これらはデータ検索 (SELECT)
  の前に必要な準備段階です。実際に手を動かしながら、データベース操作の基本を身につけましょう。
---

# 3. テーブル作成とデータ操作

## 3. テーブル作成とデータ操作

この章では、データベースの作成からテーブルの設計・作成、そしてデータの追加までの一連の操作を学びます。これらはデータ検索（SELECT）の前に必要な準備段階です。実際に手を動かしながら、データベース操作の基本を身につけましょう。

### 3.1 データベースの作成と選択

すでに学習したように、データベースは複数のテーブルをまとめて管理するための「コンテナ」です。実際にデータベースを作成し、操作していきましょう。

#### 3.1.1 データベース名の付け方

データベース名は、システムの内容や目的を反映した、わかりやすい名前にしましょう。

| システム種類            | データベース名の例                                            |
| ----------------- | ---------------------------------------------------- |
| **オンラインショップシステム** | `online_shop`, `ecommerce_db`, `web_store`           |
| **学校管理システム**      | `school_management`, `education_db`, `campus_system` |
| **図書館管理システム**     | `library_management`, `books_db`, `reading_center`   |

> 📝 **ポイント**: データベース名は、単語の区切りにアンダースコア（\_）を使うことが多いです。すべて小文字で統一するのが一般的です。

#### 3.1.2 データベースの作成

新しいデータベースを作成するには、`CREATE DATABASE` コマンドを使います。

```sql
-- 新しいデータベースを作成
CREATE DATABASE online_shop;
```

実行結果:

```
Query OK, 1 row affected (0.02 sec)
```

同名のデータベースが既に存在する場合にエラーを避けるには、`IF NOT EXISTS`を追加します：

```sql
-- 既に存在する場合はエラーを出さない
CREATE DATABASE IF NOT EXISTS online_shop;
```

#### 3.1.3 データベースの一覧表示

サーバー上のデータベース一覧を表示するには：

```sql
SHOW DATABASES;
```

実行結果:

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
5 rows in set (0.00 sec)
```

> 📝 **ポイント**: `information_schema`, `mysql`, `performance_schema`, `sys` はMySQLのシステムデータベースです。通常、これらは直接操作しません。

#### 3.1.4 データベースの選択

作成したデータベースを使用するには、`USE` コマンドで選択します：

```sql
-- データベースを選択
USE online_shop;
```

実行結果:

```
Database changed
```

現在選択しているデータベースを確認するには：

```sql
SELECT DATABASE();
```

実行結果:

```
+-------------+
| DATABASE()  |
+-------------+
| online_shop |
+-------------+
1 row in set (0.00 sec)
```

> 📝 **ポイント**: 作業を始める前に必ず「どのデータベースを使っているか」を確認する習慣をつけましょう。

### 3.2 テーブルの作成

データベースを作成したら、次はその中にテーブルを作成します。これがデータを格納する実際の「表」になります。

#### 3.2.1 基本的なデータ型

テーブル作成時に使用する主な基本データ型は以下の通りです：

| データ型       | 説明             | 使用例          |
| ---------- | -------------- | ------------ |
| INT        | 整数値            | ID、年齢、数量など   |
| VARCHAR(n) | 可変長文字列（最大n文字）  | 名前、メールアドレスなど |
| TEXT       | 長いテキスト         | 商品説明、記事本文など  |
| DATE       | 日付（YYYY-MM-DD） | 生年月日、予約日など   |

> 📝 **ポイント**: 実際にはもっと多くのデータ型がありますが、初めは上記の基本的なものだけで十分です。

#### 3.2.2 シンプルなテーブルの作成

テーブルを作成するには、`CREATE TABLE` コマンドを使います。基本的な構文は以下の通りです：

```sql
CREATE TABLE テーブル名 (
    カラム名1 データ型 [制約],
    カラム名2 データ型 [制約],
    ...
);
```

実際の例を見てみましょう：

```sql
-- 商品カテゴリのテーブルを作成
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);
```

実行結果:

```
Query OK, 0 rows affected (0.03 sec)
```

次に、商品テーブルを作成します：

```sql
-- 商品テーブルを作成
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    stock INT DEFAULT 0,
    category_id INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

実行結果:

```
Query OK, 0 rows affected (0.04 sec)
```

> 📝 **ポイント**: 上記の例では、`products`テーブルの`category_id`列が`categories`テーブルの`id`列を参照しています。これを「外部キー」と呼び、テーブル同士の関連を表します。

#### 3.2.3 主な制約

テーブル作成時には以下の基本的な制約が使えます：

| 制約              | 説明              | 例                                                     |
| --------------- | --------------- | ----------------------------------------------------- |
| PRIMARY KEY     | 主キー（一意の識別子）     | `id INT PRIMARY KEY`                                  |
| FOREIGN KEY     | 外部キー（他のテーブルを参照） | `FOREIGN KEY (category_id) REFERENCES categories(id)` |
| NOT NULL        | NULL値を許可しない     | `name VARCHAR(100) NOT NULL`                          |
| DEFAULT         | デフォルト値を設定       | `stock INT DEFAULT 0`                                 |
| AUTO\_INCREMENT | 自動的に増加する値       | `id INT AUTO_INCREMENT`                               |

#### 3.2.4 テーブル一覧と構造の確認

データベース内のテーブル一覧を表示するには：

```sql
SHOW TABLES;
```

実行結果:

```
+----------------------+
| Tables_in_online_shop|
+----------------------+
| categories           |
| products             |
+----------------------+
2 rows in set (0.00 sec)
```

テーブルの構造を確認するには：

```sql
DESCRIBE products;
-- または
DESC products;
```

実行結果:

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
5 rows in set (0.01 sec)
```

### 3.3 データの追加（INSERT）

テーブルを作成したら、次はデータを追加します。データを追加するには`INSERT INTO`コマンドを使用します。

#### 3.3.1 INSERT文の基本構文

```sql
INSERT INTO テーブル名 (カラム1, カラム2, ...)
VALUES (値1, 値2, ...);
```

#### 3.3.2 単一レコードの挿入

まずはカテゴリテーブルにデータを入れてみましょう：

```sql
-- カテゴリを追加
INSERT INTO categories (name) 
VALUES ('電子機器');
```

実行結果:

```
Query OK, 1 row affected (0.01 sec)
```

続いて別のカテゴリも追加します：

```sql
INSERT INTO categories (name)
VALUES ('家具');
```

実行結果:

```
Query OK, 1 row affected (0.01 sec)
```

> 📝 **ポイント**: `AUTO_INCREMENT`が設定された`id`カラムには値を指定する必要がありません。MySQLが自動的に値を割り当てます。

次に商品テーブルにデータを追加します：

```sql
-- 商品を追加（category_idは既に存在するカテゴリのIDを指定）
INSERT INTO products (name, price, stock, category_id)
VALUES ('スマートフォン', 89000, 25, 1);
```

実行結果:

```
Query OK, 1 row affected (0.01 sec)
```

#### 3.3.3 複数レコードの一括挿入

複数のレコードを一度に挿入することもできます：

```sql
-- 複数の商品を一括挿入
INSERT INTO products (name, price, stock, category_id)
VALUES 
('ノートパソコン', 120000, 15, 1),
('木製テーブル', 35000, 5, 2),
('オフィスチェア', 12000, 20, 2);
```

実行結果:

```
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

#### 3.3.4 挿入したデータの確認

データを挿入したら、正しく追加されたか確認しましょう：

```sql
-- カテゴリを確認
SELECT * FROM categories;
```

実行結果:

```
+----+----------+
| id | name     |
+----+----------+
|  1 | 電子機器 |
|  2 | 家具     |
+----+----------+
2 rows in set (0.00 sec)
```

```sql
-- 商品を確認
SELECT * FROM products;
```

実行結果:

```
+----+--------------------+--------+-------+-------------+
| id | name               | price  | stock | category_id |
+----+--------------------+--------+-------+-------------+
|  1 | スマートフォン     |  89000 |    25 |           1 |
|  2 | ノートパソコン     | 120000 |    15 |           1 |
|  3 | 木製テーブル       |  35000 |     5 |           2 |
|  4 | オフィスチェア     |  12000 |    20 |           2 |
+----+--------------------+--------+-------+-------------+
4 rows in set (0.00 sec)
```

### 3.4 テーブル設計の実践

実際のテーブル設計では、情報を適切に整理することが重要です。

#### 3.4.1 テーブルの分割

情報を適切に分割することで、データの管理が容易になります。例えば、以下のような「1つの表に全部入れた場合」は問題があります：

**注文情報（全部一つのテーブル）**

| 注文ID | 顧客名  | 顧客住所   | 商品名     | 商品価格   | 数量 |
| ---- | ---- | ------ | ------- | ------ | -- |
| 1    | 山田太郎 | 東京都... | スマートフォン | 89000  | 1  |
| 2    | 山田太郎 | 東京都... | ノートパソコン | 120000 | 1  |
| 3    | 佐藤花子 | 大阪府... | スマートフォン | 89000  | 2  |

これを複数のテーブルに分割することで、データの重複を減らし、管理が容易になります：

**顧客テーブル**

| 顧客ID | 顧客名  | 顧客住所   |
| ---- | ---- | ------ |
| 1    | 山田太郎 | 東京都... |
| 2    | 佐藤花子 | 大阪府... |

**商品テーブル**

| 商品ID | 商品名     | 価格     |
| ---- | ------- | ------ |
| 1    | スマートフォン | 89000  |
| 2    | ノートパソコン | 120000 |

**注文テーブル**

| 注文ID | 顧客ID | 商品ID | 数量 | 注文日        |
| ---- | ---- | ---- | -- | ---------- |
| 1    | 1    | 1    | 1  | 2023-01-15 |
| 2    | 1    | 2    | 1  | 2023-01-20 |
| 3    | 2    | 1    | 2  | 2023-01-25 |

> 📝 **ポイント**: このようにテーブルを分割することで、データの重複が減り、更新や管理が容易になります。また、外部キーを使ってテーブル間の関連性を保つことができます。

#### 3.4.2 テーブル設計の練習

実際のアプリケーション開発では、設計段階でテーブル構造を考えることが重要です。例えば、簡単なブログシステムのテーブル設計を考えてみましょう：

**authorsテーブル（著者情報）**

```sql
CREATE TABLE authors (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE
);
```

**articlesテーブル（記事情報）**

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

**commentsテーブル（コメント情報）**

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

> 📝 **ポイント**: このような設計を行うことで、「記事には複数のコメントがつけられる」「一人の著者が複数の記事を書ける」といった関係性を表現できます。

***

### 演習問題

#### 問題 3-1: シンプルなデータベースとテーブルの作成

1. `mini_shop` という名前のデータベースを作成し、使用するように設定してください。
2. 以下の列を持つ `users` テーブルを作成してください。
   * `id`: 整数型、主キー、自動採番
   * `name`: 文字列型(50文字まで)、NULL禁止
   * `email`: 文字列型(100文字まで)
3. 以下の列を持つ `items` テーブルを作成してください。
   * `id`: 整数型、主キー、自動採番
   * `name`: 文字列型(100文字まで)、NULL禁止
   * `price`: 整数型、NULL禁止
   * `stock`: 整数型、デフォルト値は0

\<details> \<summary>解答例\</summary>

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

\</details>

#### 問題 3-2: データの追加

1. `users` テーブルに少なくとも2名のユーザーデータを追加してください。
2. `items` テーブルに少なくとも3つの商品データを追加してください。

\<details> \<summary>解答例\</summary>

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

\</details>

#### 問題 3-3: データの確認

1. ユーザーテーブルのすべてのデータを表示してください。
2. 商品テーブルのすべてのデータを表示してください。
3. 価格が5000円以上の商品だけを表示してください。

\<details> \<summary>解答例\</summary>

```sql
-- 1. ユーザーテーブルのすべてのデータを表示
SELECT * FROM users;

-- 2. 商品テーブルのすべてのデータを表示
SELECT * FROM items;

-- 3. 価格が5000円以上の商品だけを表示
SELECT * FROM items WHERE price >= 5000;
```

\</details>

#### 問題 3-4: テーブル設計の考察

あなたはブログ投稿システムを設計することになりました。以下の情報を管理するために必要なテーブルと、それぞれの主なカラムを考えてください。

情報:

* 投稿者（名前、メールアドレスなど）
* ブログ記事（タイトル、内容、投稿日など）
* カテゴリ（カテゴリ名）
* コメント（コメント内容、投稿者、日時など）

\<details> \<summary>解答例\</summary>

以下のようなテーブル設計が考えられます：

1. **authorsテーブル**（投稿者情報）
   * id (主キー)
   * name (名前、NOT NULL)
   * email (メールアドレス)
   * join\_date (登録日)
2. **categoriesテーブル**（カテゴリ情報）
   * id (主キー)
   * name (カテゴリ名、NOT NULL)
3. **articlesテーブル**（ブログ記事）
   * id (主キー)
   * title (タイトル、NOT NULL)
   * content (記事内容、TEXT型)
   * author\_id (外部キー、投稿者のID)
   * category\_id (外部キー、カテゴリのID)
   * publish\_date (投稿日)
4. **commentsテーブル**（コメント）
   * id (主キー)
   * article\_id (外部キー、記事のID)
   * author\_name (コメント投稿者名)
   * content (コメント内容)
   * comment\_date (コメント日時)

このテーブル設計の特徴：

* 各テーブルは明確な一つの概念を表している
* 記事は投稿者とカテゴリに関連付けられている
* コメントは記事に関連付けられている

\</details>
