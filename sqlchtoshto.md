---
description: このチートシートは、よく使うSQL構文を簡潔にまとめたリファレンスです。SQL学習の復習や実務での参照にご活用ください。
---

# SQLチートシート

### データベース操作

#### データベースの作成と削除

```sql
-- データベースの作成
CREATE DATABASE database_name;

-- 既存の場合はエラーを出さずに作成
CREATE DATABASE IF NOT EXISTS database_name;

-- データベースの削除（注意: 取り消せません）
DROP DATABASE database_name;
```

#### データベースの使用と情報表示

```sql
-- データベース一覧の表示
SHOW DATABASES;

-- 使用するデータベースの選択
USE database_name;

-- 現在使用中のデータベースを確認
SELECT DATABASE();
```

### テーブル操作

#### テーブルの作成

```sql
-- 基本的なテーブル作成
CREATE TABLE table_name (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 外部キーを持つテーブル
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

#### テーブル情報の表示

```sql
-- テーブル一覧の表示
SHOW TABLES;

-- テーブル構造の表示
DESCRIBE table_name;
-- または
DESC table_name;

-- テーブル作成文の表示
SHOW CREATE TABLE table_name;
```

#### テーブルの変更と削除

```sql
-- カラム追加
ALTER TABLE table_name ADD column_name datatype;

-- カラム変更
ALTER TABLE table_name MODIFY column_name new_datatype;

-- カラム削除
ALTER TABLE table_name DROP COLUMN column_name;

-- テーブル名変更
RENAME TABLE old_name TO new_name;

-- テーブル削除（注意: 取り消せません）
DROP TABLE table_name;
```

### データ操作

#### データの追加

```sql
-- 単一行の追加
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);

-- 複数行の一括追加
INSERT INTO table_name (column1, column2)
VALUES 
    (value1, value2),
    (value3, value4),
    (value5, value6);

-- 別のテーブルからデータをコピーして追加
INSERT INTO target_table (column1, column2)
SELECT column1, column2 FROM source_table WHERE condition;
```

#### データの検索（基本）

```sql
-- すべての列を選択
SELECT * FROM table_name;

-- 特定の列を選択
SELECT column1, column2 FROM table_name;

-- 条件付き検索
SELECT * FROM table_name WHERE condition;

-- 並べ替え
SELECT * FROM table_name ORDER BY column1 [ASC|DESC];

-- 重複排除
SELECT DISTINCT column1 FROM table_name;

-- 結果の制限
SELECT * FROM table_name LIMIT number;
-- または
SELECT * FROM table_name LIMIT offset, count;
```

#### WHERE句の条件式

```sql
-- 等価/不等価
WHERE column = value
WHERE column <> value  -- または !=

-- 比較
WHERE column > value
WHERE column >= value
WHERE column < value
WHERE column <= value

-- 範囲
WHERE column BETWEEN value1 AND value2

-- リスト
WHERE column IN (value1, value2, value3)

-- パターンマッチ
WHERE column LIKE 'pattern%'  -- 前方一致
WHERE column LIKE '%pattern'  -- 後方一致
WHERE column LIKE '%pattern%'  -- 部分一致

-- NULL値の検索
WHERE column IS NULL
WHERE column IS NOT NULL
```

#### 論理演算子

```sql
-- AND（両方の条件を満たす）
WHERE condition1 AND condition2

-- OR（どちらかの条件を満たす）
WHERE condition1 OR condition2

-- NOT（条件を否定する）
WHERE NOT condition

-- 複合条件（括弧で優先順位を明確に）
WHERE (condition1 OR condition2) AND condition3
```

#### データの更新と削除

```sql
-- データの更新
UPDATE table_name
SET column1 = value1, column2 = value2
WHERE condition;

-- データの削除
DELETE FROM table_name WHERE condition;

-- テーブル内の全データ削除（注意: 取り消せません）
TRUNCATE TABLE table_name;
```

### 集計・分析

#### 集計関数

```sql
-- 行数のカウント
SELECT COUNT(*) FROM table_name;
SELECT COUNT(column) FROM table_name;  -- NULL値は除外

-- 合計
SELECT SUM(column) FROM table_name;

-- 平均
SELECT AVG(column) FROM table_name;

-- 最大値、最小値
SELECT MAX(column), MIN(column) FROM table_name;
```

#### グループ化

```sql
-- 基本的なグループ化
SELECT column1, COUNT(*) 
FROM table_name 
GROUP BY column1;

-- 複数列でのグループ化
SELECT column1, column2, SUM(column3) 
FROM table_name 
GROUP BY column1, column2;

-- グループ化後のフィルタリング
SELECT column1, COUNT(*) 
FROM table_name 
GROUP BY column1
HAVING COUNT(*) > 5;

-- WHERE と HAVING の併用
SELECT column1, AVG(column2) 
FROM table_name 
WHERE condition
GROUP BY column1
HAVING AVG(column2) > value;
```

### 計算と関数

#### 算術演算

```sql
-- 基本的な算術演算
SELECT column1 + column2 AS sum
SELECT column1 - column2 AS difference
SELECT column1 * column2 AS product
SELECT column1 / column2 AS quotient
```

#### 文字列関数

```sql
-- 連結
SELECT CONCAT(column1, ' ', column2) AS full_name;

-- 長さ
SELECT LENGTH(column) AS string_length;

-- 大文字/小文字変換
SELECT UPPER(column) AS uppercase;
SELECT LOWER(column) AS lowercase;

-- 部分文字列
SELECT SUBSTRING(column, start, length);
SELECT LEFT(column, length);
SELECT RIGHT(column, length);

-- 空白除去
SELECT TRIM(column);
```

#### 数値関数

```sql
-- 四捨五入
SELECT ROUND(column, decimal_places);

-- 切り上げ/切り捨て
SELECT CEILING(column);  -- 切り上げ
SELECT FLOOR(column);    -- 切り捨て

-- 絶対値
SELECT ABS(column);
```

#### 日付・時間関数

```sql
-- 現在の日付/時刻
SELECT NOW();
SELECT CURDATE();
SELECT CURTIME();

-- 日付の各部分を取得
SELECT YEAR(date_column);
SELECT MONTH(date_column);
SELECT DAY(date_column);

-- 日付の計算
SELECT DATE_ADD(date_column, INTERVAL value unit);
SELECT DATE_SUB(date_column, INTERVAL value unit);
SELECT DATEDIFF(date1, date2);  -- 日数の差を返す
```

#### 条件式

```sql
-- CASE式（条件分岐）
SELECT 
    column,
    CASE 
        WHEN condition1 THEN result1
        WHEN condition2 THEN result2
        ELSE default_result
    END AS new_column
FROM table_name;

-- IF関数
SELECT IF(condition, true_value, false_value);

-- NULL値の処理
SELECT IFNULL(column, alternative_value);
SELECT COALESCE(value1, value2, ..., default_value);
```

### テーブル結合

#### 内部結合（INNER JOIN）

```sql
-- INNER JOIN（両方のテーブルに一致するレコードを返す）
SELECT a.column1, a.column2, b.column1
FROM table_a a
INNER JOIN table_b b ON a.id = b.a_id;

-- 複数テーブルの結合
SELECT a.column1, b.column1, c.column1
FROM table_a a
INNER JOIN table_b b ON a.id = b.a_id
INNER JOIN table_c c ON b.id = c.b_id;
```

#### 外部結合（OUTER JOIN）

```sql
-- LEFT JOIN（左テーブルのすべてのレコードと、右テーブルの一致するレコードを返す）
SELECT a.column1, b.column1
FROM table_a a
LEFT JOIN table_b b ON a.id = b.a_id;

-- RIGHT JOIN（右テーブルのすべてのレコードと、左テーブルの一致するレコードを返す）
SELECT a.column1, b.column1
FROM table_a a
RIGHT JOIN table_b b ON a.id = b.a_id;
```

### データ型

#### 数値型

```
INT / INTEGER       - 整数 (-2,147,483,648 to 2,147,483,647)
TINYINT             - 小さな整数 (-128 to 127)
SMALLINT            - 小さな整数 (-32,768 to 32,767)
MEDIUMINT           - 中サイズの整数
BIGINT              - 大きな整数
DECIMAL(m,d)        - 固定小数点数（m桁、小数点以下d桁）
FLOAT, DOUBLE       - 浮動小数点数
```

#### 文字列型

```
CHAR(n)             - 固定長文字列（最大255文字）
VARCHAR(n)          - 可変長文字列（最大65,535文字）
TEXT                - 長いテキスト（最大65,535文字）
MEDIUMTEXT          - より長いテキスト
LONGTEXT            - 非常に長いテキスト
```

#### 日付と時間型

```
DATE                - 日付（YYYY-MM-DD）
TIME                - 時間（HH:MM:SS）
DATETIME            - 日付と時間（YYYY-MM-DD HH:MM:SS）
TIMESTAMP           - タイムスタンプ
YEAR                - 年のみ
```

#### その他のデータ型

```
BOOLEAN / BOOL      - 真偽値（TINYINTの別名）
ENUM                - 列挙型（事前定義された値のリスト）
JSON                - JSON形式のデータ（MySQL 5.7.8以降）
BLOB                - バイナリデータ
```

### インデックスと制約

#### インデックス

```sql
-- テーブル作成時に主キーインデックスを作成
CREATE TABLE table_name (
    id INT PRIMARY KEY,
    ...
);

-- テーブル作成時に一般的なインデックスを作成
CREATE TABLE table_name (
    id INT,
    name VARCHAR(100),
    INDEX name_index (name)
);

-- 既存テーブルにインデックスを追加
CREATE INDEX index_name ON table_name (column);

-- ユニークインデックスの作成
CREATE UNIQUE INDEX index_name ON table_name (column);
```

#### 制約

```sql
-- NOT NULL制約（NULLを許可しない）
column_name data_type NOT NULL

-- UNIQUE制約（値の重複を許可しない）
column_name data_type UNIQUE

-- PRIMARY KEY制約（一意の識別子）
column_name data_type PRIMARY KEY

-- FOREIGN KEY制約（他のテーブルへの参照）
FOREIGN KEY (column_name) REFERENCES other_table(other_column)

-- CHECK制約（条件を満たす値のみ許可）
CHECK (column_name condition)

-- DEFAULT値（挿入時に値が指定されない場合のデフォルト）
column_name data_type DEFAULT default_value
```
