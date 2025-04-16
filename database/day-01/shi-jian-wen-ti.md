---
description: この実践問題では、データベースの作成からデータの追加、基本的な検索までの一連の流れを実践します。架空のオンライン書店のデータベースを構築していきましょう。
---

# 実践問題 ①

### 1. データベースとテーブルの作成

#### 問題 1-1: データベースの作成

`book_store` という名前のデータベースを作成し、使用するように設定してください。

**ヒント**:

* データベースを作成するには `CREATE DATABASE` コマンドを使います
* データベースを使用するには `USE` コマンドを使います

\<details> \<summary>解答例\</summary>

```sql
-- データベースの作成
CREATE DATABASE book_store;

-- データベースの選択
USE book_store;
```

\</details>

#### 問題 1-2: 著者テーブルの作成

以下の列を持つ `authors` テーブルを作成してください。

* `id`: 整数型、主キー、自動採番
* `name`: 文字列型(50文字まで)、NOT NULL制約あり
* `email`: 文字列型(100文字まで)、UNIQUE制約あり
* `birth_date`: 日付型
* `biography`: 長いテキスト型

**ヒント**:

* テーブルを作成するには `CREATE TABLE` コマンドを使います
* 主キーには `PRIMARY KEY` 制約を指定します
* 自動採番には `AUTO_INCREMENT` を使います

\<details> \<summary>解答例\</summary>

```sql
CREATE TABLE authors (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    birth_date DATE,
    biography TEXT
);
```

\</details>

#### 問題 1-3: 書籍テーブルの作成

以下の列を持つ `books` テーブルを作成してください。

* `id`: 整数型、主キー、自動採番
* `title`: 文字列型(100文字まで)、NOT NULL制約あり
* `author_id`: 整数型、`authors` テーブルの `id` を参照する外部キー
* `price`: 小数型(10桁、2桁小数点)、NOT NULL制約あり
* `publication_date`: 日付型
* `category`: 文字列型(50文字まで)
* `stock`: 整数型、デフォルト値は0

**ヒント**:

* 外部キーには `FOREIGN KEY` 制約を指定します
* デフォルト値は `DEFAULT` キーワードで指定します

\<details> \<summary>解答例\</summary>

```sql
CREATE TABLE books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    author_id INT,
    price DECIMAL(10, 2) NOT NULL,
    publication_date DATE,
    category VARCHAR(50),
    stock INT DEFAULT 0,
    FOREIGN KEY (author_id) REFERENCES authors(id)
);
```

\</details>

### 2. データの追加

#### 問題 2-1: 著者データの追加

`authors` テーブルに以下の著者データを追加してください。

| 名前   | メールアドレス            | 生年月日       | 経歴                       |
| ---- | ------------------ | ---------- | ------------------------ |
| 山田太郎 | yamada@example.com | 1975-05-15 | 日本のベストセラー作家。20冊以上の小説を執筆。 |
| 佐藤花子 | sato@example.com   | 1980-10-20 | ミステリー小説の第一人者。推理作家賞を受賞。   |
| 鈴木一郎 | suzuki@example.com | 1965-03-08 | プログラミング関連の技術書を多数出版。      |

**ヒント**:

* データを追加するには `INSERT INTO` コマンドを使います
* 複数行を一度に挿入することもできます

\<details> \<summary>解答例\</summary>

```sql
INSERT INTO authors (name, email, birth_date, biography) VALUES
('山田太郎', 'yamada@example.com', '1975-05-15', '日本のベストセラー作家。20冊以上の小説を執筆。'),
('佐藤花子', 'sato@example.com', '1980-10-20', 'ミステリー小説の第一人者。推理作家賞を受賞。'),
('鈴木一郎', 'suzuki@example.com', '1965-03-08', 'プログラミング関連の技術書を多数出版。');
```

\</details>

#### 問題 2-2: 書籍データの追加

`books` テーブルに以下の書籍データを追加してください。

| タイトル      | 著者ID | 価格   | 出版日        | カテゴリ  | 在庫数 |
| --------- | ---- | ---- | ---------- | ----- | --- |
| 夏の記憶      | 1    | 1500 | 2023-06-15 | 小説    | 25  |
| 真夜中の密室    | 2    | 1800 | 2022-11-03 | ミステリー | 18  |
| Python入門  | 3    | 2800 | 2024-01-10 | 技術書   | 35  |
| 朝の光       | 1    | 1600 | 2024-03-20 | 小説    | 12  |
| Java完全ガイド | 3    | 3200 | 2023-08-05 | 技術書   | 20  |

**ヒント**:

* 日付は 'YYYY-MM-DD' 形式で指定します
* 外部キーの値は参照先のテーブルに存在する値である必要があります

\<details> \<summary>解答例\</summary>

```sql
INSERT INTO books (title, author_id, price, publication_date, category, stock) VALUES
('夏の記憶', 1, 1500, '2023-06-15', '小説', 25),
('真夜中の密室', 2, 1800, '2022-11-03', 'ミステリー', 18),
('Python入門', 3, 2800, '2024-01-10', '技術書', 35),
('朝の光', 1, 1600, '2024-03-20', '小説', 12),
('Java完全ガイド', 3, 3200, '2023-08-05', '技術書', 20);
```

\</details>

### 3. 基本的なデータ検索

#### 問題 3-1: シンプルな検索

以下の検索を実行してください。

1. すべての著者の名前と生年月日を取得
2. すべての書籍のタイトルと価格を取得
3. 在庫が20以上の書籍のみを取得

**ヒント**:

* 特定の列のみを取得するには、`SELECT` の後に列名をカンマ区切りで指定します
* 条件で絞り込むには `WHERE` 句を使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. すべての著者の名前と生年月日
SELECT name, birth_date FROM authors;

-- 2. すべての書籍のタイトルと価格
SELECT title, price FROM books;

-- 3. 在庫が20以上の書籍
SELECT * FROM books WHERE stock >= 20;
```

\</details>

#### 問題 3-2: 条件検索

以下の条件に一致するデータを検索するクエリを作成してください。

1. 価格が2000円以上の書籍
2. 小説カテゴリの書籍
3. 2023年以降に出版された書籍

**ヒント**:

* 日付の一部（年など）を比較するには、`YEAR()` 関数を使うことができます

\<details> \<summary>解答例\</summary>

```sql
-- 1. 価格が2000円以上の書籍
SELECT * FROM books WHERE price >= 2000;

-- 2. 小説カテゴリの書籍
SELECT * FROM books WHERE category = '小説';

-- 3. 2023年以降に出版された書籍
SELECT * FROM books WHERE publication_date >= '2023-01-01';
-- または
SELECT * FROM books WHERE YEAR(publication_date) >= 2023;
```

\</details>

#### 問題 3-3: 複合条件検索

以下の複合条件に一致するデータを検索するクエリを作成してください。

1. 小説カテゴリで、かつ価格が1500円以上の書籍
2. 技術書カテゴリ、または在庫が30以上の書籍
3. 2023年に出版された書籍（2023年1月1日～2023年12月31日）

**ヒント**:

* 複数の条件を組み合わせるには、`AND` や `OR` 演算子を使います
* 範囲指定には `BETWEEN` 演算子も使えます

\<details> \<summary>解答例\</summary>

```sql
-- 1. 小説カテゴリで、価格が1500円以上の書籍
SELECT * FROM books WHERE category = '小説' AND price >= 1500;

-- 2. 技術書カテゴリ、または在庫が30以上の書籍
SELECT * FROM books WHERE category = '技術書' OR stock >= 30;

-- 3. 2023年に出版された書籍
SELECT * FROM books WHERE publication_date BETWEEN '2023-01-01' AND '2023-12-31';
-- または
SELECT * FROM books WHERE YEAR(publication_date) = 2023;
```

\</details>

### 4. 結果の整形と制限

#### 問題 4-1: 結果の並べ替え

以下の並べ替えを行うクエリを作成してください。

1. 書籍を価格の安い順に並べ替え
2. 書籍を出版日の新しい順に並べ替え
3. 書籍をカテゴリ別、同じカテゴリ内では価格の高い順に並べ替え

**ヒント**:

* 並べ替えには `ORDER BY` 句を使います
* 複数の列で並べ替える場合はカンマで区切ります
* 降順に並べ替えるには `DESC` キーワードを使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. 価格の安い順
SELECT * FROM books ORDER BY price;
-- または
SELECT * FROM books ORDER BY price ASC;

-- 2. 出版日の新しい順
SELECT * FROM books ORDER BY publication_date DESC;

-- 3. カテゴリ別、同じカテゴリ内では価格の高い順
SELECT * FROM books ORDER BY category, price DESC;
```

\</details>

#### 問題 4-2: 結果の制限

以下の制限を行うクエリを作成してください。

1. 価格の高い上位3冊の書籍を取得
2. 最も新しく出版された2冊の書籍を取得
3. 4番目から6番目に価格の高い書籍を取得

**ヒント**:

* 結果の行数を制限するには `LIMIT` 句を使います
* 指定した位置から始めるには `OFFSET` または `LIMIT 開始位置, 行数` を使います

\<details> \<summary>解答例\</summary>

```sql
-- 1. 価格の高い上位3冊
SELECT * FROM books ORDER BY price DESC LIMIT 3;

-- 2. 最も新しく出版された2冊
SELECT * FROM books ORDER BY publication_date DESC LIMIT 2;

-- 3. 4番目から6番目に価格の高い書籍
SELECT * FROM books ORDER BY price DESC LIMIT 3 OFFSET 3;
-- または
SELECT * FROM books ORDER BY price DESC LIMIT 3, 3;
```

\</details>

### 5. 著者と書籍の関連付け

#### 問題 5-1: 著者情報を含む書籍一覧

各書籍とその著者の情報（書籍タイトル、価格、著者名）を表示するクエリを作成してください。

**ヒント**:

* 著者情報を取得するには、`books` テーブルと `authors` テーブルを関連付ける必要があります
* 複数のテーブルを結合するには、`FROM` 句に両方のテーブルを指定し、`WHERE` 句で関連付ける条件を指定します

\<details> \<summary>解答例\</summary>

```sql
SELECT
    b.title AS 書籍タイトル,
    b.price AS 価格,
    a.name AS 著者名
FROM
    books b,
    authors a
WHERE
    b.author_id = a.id
ORDER BY
    b.title;
```

\</details>

#### 問題 5-2: 著者別の書籍一覧

各著者が書いた書籍の一覧（著者名、書籍数、書籍タイトルのカンマ区切りリスト）を表示するクエリを作成してください。

**ヒント**:

* これは少し高度な問題です。`GROUP_CONCAT` 関数を使用して、グループ化したデータを連結できます

\<details> \<summary>解答例\</summary>

```sql
SELECT
    a.name AS 著者名,
    COUNT(b.id) AS 書籍数,
    GROUP_CONCAT(b.title ORDER BY b.title SEPARATOR ', ') AS 書籍リスト
FROM
    authors a
LEFT JOIN
    books b ON a.id = b.author_id
GROUP BY
    a.id
ORDER BY
    書籍数 DESC;
```

\</details>

### まとめ

この実践問題を通じて、以下のスキルを身につけることができました：

* データベースとテーブルの作成
* データの追加
* 基本的な検索と条件の指定
* 結果の並べ替えと制限
* 複数テーブルの関連付け

これらは実際のデータベース操作で頻繁に使用される基本的な操作です。次の実践問題では、より高度なデータ集計やグループ化について学びましょう。
