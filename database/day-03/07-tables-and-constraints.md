---
description: データベースの中心となるテーブルの作成方法と、データの整合性を保つための制約について学びます。 適切に設計されたテーブルがデータベースの基礎となります。
---

# 7. テーブルの作成と制約

### 7.1 テーブルの基本構造

テーブルは、データベース内でデータを格納する主要な構造です。テーブルはカラム (列) とレコード (行) から構成されています。

#### 7.1.1 テーブルとカラムの定義

テーブルを作成するには `CREATE TABLE` 文を使用します。

```sql
-- 基本的なテーブル作成
CREATE TABLE products (
    id INT,
    name VARCHAR(100),
    price INT,
    description TEXT
);
```

各カラムには、データ型と必要に応じて制約を指定します。

> 📝 **ポイント**: テーブル名とカラム名は、わかりやすい名前をつけることが重要です。一般的には英小文字とアンダースコア ( `_` ) を使用します (例: `product_categories` )。また、テーブル名は複数形 ( `products`, `users` など) にするのが慣習です。

#### 7.1.2 主なデータ型

MySQL で使用できる主なデータ型は以下の通りです。

<table><thead><tr><th width="185.272705078125">データ型</th><th width="344">説明</th><th>使用例</th></tr></thead><tbody><tr><td>INT</td><td>整数値</td><td>商品ID、数量、年齢</td></tr><tr><td>VARCHAR(n)</td><td>可変長文字列 (最大 n文字)</td><td>名前、メールアドレス</td></tr><tr><td>TEXT</td><td>長いテキスト</td><td>商品説明、記事本文</td></tr><tr><td>DATE</td><td>日付 (YYYY-MM-DD)</td><td>生年月日、予約日</td></tr><tr><td>DATETIME</td><td>日付と時間 (YYYY-MM-DD HH:MM:SS)</td><td>注文日時、更新日時</td></tr><tr><td>DECIMAL(p,s)</td><td>精度の高い小数 (p桁の数値、s桁の小数部)</td><td>金額 (外貨など)</td></tr><tr><td>BOOLEAN / TINYINT</td><td>真偽値 (TRUE または FALSE)</td><td>フラグ、ステータス</td></tr></tbody></table>



> 📝 **ポイント**: 適切なデータ型を選ぶことで、データの正確性を確保し、ストレージを効率的に使用できます。例えば、日本円のように小数がない金額は `INT` 型で十分ですが、ドルやユーロなど小数がある通貨では `DECIMAL` 型が適しています。

### 7.2 主キーと自動採番

#### 7.2.1 主キーの設定

主キー (PRIMARY KEY) は、テーブル内の各行を一意に識別するためのカラムです。

```sql
-- 主キーを持つテーブル
CREATE TABLE customers (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

> 📝 **ポイント**: 主キーには重複する値や NULL 値を格納できません。主キーは通常、テーブルの最初のカラムとして定義します。データの検索や結合操作が高速になり、データの一貫性を確保できます。

#### 7.2.2 自動採番 (AUTO\_INCREMENT)

`AUTO_INCREMENT` 属性を使用すると、新しい行を挿入するたびに自動的に値が増加します。

```sql
-- 自動採番を使用したテーブル
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

このテーブルに新しい商品を追加する際、`id` 値を指定する必要はありません。

```sql
-- id を指定せずにデータを挿入
INSERT INTO products (name, price) VALUES ('スマートフォン', 89000);
INSERT INTO products (name, price) VALUES ('タブレット', 69000);
```

<details>

<summary>実行結果</summary>

```
Query OK, 1 row affected (0.01 sec)
Query OK, 1 row affected (0.00 sec)

SELECT * FROM products;
+----+------------------+-------+---------------------+
| id | name             | price | created_at          |
+----+------------------+-------+---------------------+
|  1 | スマートフォン     | 89000 | 2025-04-30 14:25:10 |
|  2 | タブレット        | 69000 | 2025-04-30 14:25:15 |
+----+------------------+-------+---------------------+
2 rows in set (0.00 sec)
```

</details>

> 📝 **ポイント**: `AUTO_INCREMENT` は主に主キーのカラムで使用され、一意の ID 値を自動的に生成します。データ挿入時に明示的に ID を指定しなくて済むため、プログラムの記述が簡単になります。また、`CURRENT_TIMESTAMP` のようなデフォルト値を使うと、行が挿入された時点の日時を自動的に記録できます。

### 7.3 カラムの制約

制約 (CONSTRAINT) は、テーブルのカラムに設定する規則で、データの正確性と整合性を確保します。

#### 7.3.1 NOT NULL 制約

`NOT NULL` 制約は、カラムに NULL 値を許可しないようにします。

```sql
-- NOT NULL 制約を持つテーブル
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount INT NOT NULL
);
```

> 📝 **ポイント**: 重要なデータを含むカラムには `NOT NULL` 制約を設定すべきです。これにより、必須データの欠落を防止できます。例えば、注文日や注文金額などは必ず値が必要なため、`NOT NULL` とするのが適切です。

#### 7.3.2 一意制約 (UNIQUE)

`UNIQUE` 制約は、カラムに重複する値を許可しないようにします。

```sql
-- UNIQUE 制約を持つテーブル
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL
);
```

> 📝 **ポイント**: ユーザー名やメールアドレスなど、システム内で一意であるべき値には `UNIQUE` 制約を使用します。`UNIQUE` 制約があるカラムには同じ値を持つ複数の行を挿入できなくなります。

#### 7.3.3 デフォルト値 (DEFAULT)

`DEFAULT` 制約を使用すると、値が明示的に指定されない場合にカラムに使用される値を設定できます。

```sql
-- DEFAULT 制約を持つテーブル
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    stock INT DEFAULT 0,
    active BOOLEAN DEFAULT TRUE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

> 📝 **ポイント**: デフォルト値を設定することで、INSERT 文を簡潔にでき、データの一貫性も向上します。例えば、新商品の在庫数のデフォルトを `0` にしたり、ステータスのデフォルトをアクティブ ( `TRUE` ) にしたりできます。日時フィールドには `CURRENT_TIMESTAMP` を使って現在の日時をデフォルト値にするのが一般的です。

### 7.4 外部キー制約

外部キー (FOREIGN KEY) 制約は、テーブル間の関係を定義し、参照整合性を維持します。

#### 7.4.1 外部キーの基本的な設定

```sql
-- 外部キー制約を持つテーブル
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

この例では、`orders` テーブルの `customer_id` カラムが `customers` テーブルの `id` カラムを参照しています。

> 📝 **ポイント**: 外部キー制約により、存在しない顧客 ID を注文テーブルに挿入しようとするとエラーになります。これにより、データの一貫性が保たれます。また、データの関連性が明示的に定義されるため、データベース設計の理解も容易になります。

#### 7.4.2 複数の外部キーの設定

1つのテーブルに複数の外部キーを設定することも可能です。

```sql
-- 複数の外部キー制約を持つテーブル
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price INT NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```

> 📝 **ポイント**: 多くの実際のアプリケーションでは、複数のテーブルが相互に関連しています。外部キー制約を使うことで、これらの関係が正しく維持されます。例えば、注文明細テーブル ( `order_items` ) は注文テーブル ( `orders` ) と商品テーブル ( `products` ) の両方に関連しています。

### 7.5 テーブルの変更と削除

#### 7.5.1 テーブル構造の変更 (ALTER TABLE)

既存のテーブルの構造を変更するには `ALTER TABLE` 文を使用します。

```sql
-- カラムの追加
ALTER TABLE products ADD description TEXT;

-- カラムの変更
ALTER TABLE products MODIFY price DECIMAL(10, 2);

-- カラムの削除
ALTER TABLE products DROP COLUMN description;
```

> 📝 **ポイント**: 本番環境でテーブル構造を変更する場合は注意が必要です。特に大きなテーブルでは変更に時間がかかったり、アプリケーションに影響したりする可能性があります。可能であれば、テスト環境で変更の影響を確認してから本番環境に適用しましょう。

#### 7.5.2 テーブルの削除 (DROP TABLE)

テーブルを完全に削除するには `DROP TABLE` 文を使用します。

```sql
-- テーブルの削除
DROP TABLE order_items;
DROP TABLE orders;
DROP TABLE products;
DROP TABLE customers;
```

> ⚠️ **注意**: `DROP TABLE` コマンドは取り消せません。実行する前に本当に削除してよいか確認してください。特に本番環境では細心の注意が必要です。また、外部キー制約がある場合は、参照されているテーブルよりも参照しているテーブルを先に削除する必要があります。



***

### 演習問題

#### 問題 7-1: 基本的なテーブルの作成

以下の仕様に基づいて、`employees` (従業員) テーブルを作成してください。

* 従業員ID: 整数型、主キー、自動採番
* 氏名: 100文字以内の文字列型、必須
* メールアドレス: 100文字以内の文字列型、必須、一意制約あり
* 部署: 50文字以内の文字列型、必須
* 入社日: 日付型、必須
* 給料: 整数型、必須

<details>

<summary>解答</summary>

```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    department VARCHAR(50) NOT NULL,
    hire_date DATE NOT NULL,
    salary INT NOT NULL
);
```

</details>

#### 問題 7-2: 外部キー制約の実装

以下の2つのテーブルを作成してください。

1. `departments` (部署) テーブル
   * 部署ID: 整数型、主キー、自動採番
   * 部署名: 50文字以内の文字列型、必須、一意制約あり
   * 場所: 100文字以内の文字列型
2. `employees` (従業員) テーブル (問題 7-1をベースに変更)
   * 従業員 ID: 整数型、主キー、自動採番
   * 氏名: 100文字以内の文字列型、必須
   * メールアドレス: 100文字以内の文字列型、必須、一意制約あり
   * 部署ID: 整数型、必須、departments テーブルの部署 ID を参照
   * 入社日: 日付型、必須
   * 給料: 整数型、必須

<details>

<summary>解答</summary>

```sql
-- 部署テーブル
CREATE TABLE departments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    location VARCHAR(100)
);

-- 従業員テーブル
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    department_id INT NOT NULL,
    hire_date DATE NOT NULL,
    salary INT NOT NULL,
    FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

</details>

#### 問題 7-3: デフォルト値と日付の自動設定

以下の仕様に基づいて、`projects` (プロジェクト) テーブルを作成してください。

* プロジェクト ID: 整数型、主キー、自動採番
* 名前: 100文字以内の文字列型、必須
* 説明: テキスト型
* 開始日: 日付型、必須
* 終了予定日: 日付型
* ステータス: 文字列型 ( '計画中', '進行中', '完了', '中止' )、デフォルト値は '計画中'
* 担当部署 ID: 整数型、必須、departments テーブルの部署 ID を参照
* 作成日時: 日時型、自動的に現在の日時を設定
* 更新日時: 日時型、自動的に現在の日時を設定

<details>

<summary>解答</summary>

```sql
CREATE TABLE projects (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    start_date DATE NOT NULL,
    end_date DATE,
    status VARCHAR(20) DEFAULT '計画中',
    department_id INT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

</details>

#### 問題 7-4: テーブル構造の変更 ⭐️

既存の `employees` テーブルに対して以下の変更を行ってください。

1. `phone` (電話番号) カラムを追加する (20文字以内の文字列型、NULL許可)
2. `salary` カラムの型を `DECIMAL(10, 2)` に変更する
3. `notes` (備考) カラムを追加する (テキスト型、NULL許可)

<details>

<summary>解答</summary>

```sql
-- 電話番号カラムの追加
ALTER TABLE employees ADD phone VARCHAR(20);

-- 給料カラムのデータ型変更
ALTER TABLE employees MODIFY salary DECIMAL(10, 2) NOT NULL;

-- 備考カラムの追加
ALTER TABLE employees ADD notes TEXT;
```

</details>
