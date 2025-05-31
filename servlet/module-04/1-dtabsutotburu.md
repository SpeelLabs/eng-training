# 1. データベース設計とテーブル作成

### 1.1 データベースの設計方針

パート1 では、投稿データを以下のような Post クラスで管理していました：

```java
// パート1　の Post クラス (復習)
public class Post {
    private int id;
    private String title;
    private String content;
    private LocalDateTime createdAt;
    // ...
}
```

この Post クラスの構造を基に、データベースのテーブル設計を行います。Java のデータ型と MySQL のデータ型の対応関係を理解することが重要です。

#### Java と MySQL のデータ型対応表

| Java の型         | MySQL の型     | 説明             |
| --------------- | ------------ | -------------- |
| `int`           | `INT`        | 整数値 (ID など)    |
| `String`        | `VARCHAR(n)` | 可変長文字列         |
| `String` (長文)   | `TEXT`       | 長い文字列 (投稿内容など) |
| `LocalDateTime` | `DATETIME`   | 日時データ          |

> 📝 **ポイント**: パート1 の Post クラスの属性をそのままデータベースのカラムとして設計します。これにより、Java オブジェクトとデータベースレコードの対応関係が明確になります。

### 1.2 posts テーブルの設計

投稿データを格納するための `posts` テーブルを設計します。まず、必要なカラムを整理しましょう。

#### 基本的なカラム設計

```sql
-- posts テーブルの基本構造
CREATE TABLE posts (
    id INT,           -- 投稿 ID
    title VARCHAR(255), -- 投稿タイトル
    content TEXT,     -- 投稿内容
    created_at DATETIME -- 投稿日時
);
```

この基本構造に、データベースとして必要な制約を追加していきます。

#### 主キーの設定

まず、`id` カラムを主キーとして設定し、自動採番機能を追加します。

```sql
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    content TEXT,
    created_at DATETIME
);
```

> 📝 **ポイント**: `AUTO_INCREMENT` を設定することで、新しいレコードが追加されるたびに自動的にIDが採番されます。パート1では手動でIDを管理していましたが、データベースが自動的に管理してくれるようになります。

#### NOT NULL 制約の追加

必須項目に `NOT NULL` 制約を追加します。

```sql
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at DATETIME NOT NULL
);
```

#### デフォルト値の設定

作成日時には、デフォルト値として現在時刻を設定します。

```sql
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

> 📝 **ポイント**: `DEFAULT CURRENT_TIMESTAMP` により、レコード挿入時に自動的に現在時刻が設定されます。パート1の LocalDateTime.now() と同様の機能をデータベース側で実現できます。

### 1.3 データベースとテーブルの作成

#### schema.sql ファイルの作成

プロジェクトにデータベース定義ファイルを作成します。プロジェクトのルートディレクトリに `database` フォルダを作成し、その中に `schema.sql` ファイルを作成します。

**フォルダ構造:**

```
blog-app/
├── database/
│   └── schema.sql
├── src/
│   └── main/
│       └── java/
└── pom.xml
```

`database/schema.sql` ファイルに以下の内容を記述します：

```sql
-- ブログアプリケーション用データベーススキーマ
-- 作成日：[現在の日付]

-- データベースの作成と選択
CREATE DATABASE IF NOT EXISTS blog_app;
USE blog_app;

-- 既存のテーブルを削除 (開発時の初期化用)
DROP TABLE IF EXISTS posts;

-- posts テーブルの作成
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL COMMENT '投稿タイトル',
    content TEXT NOT NULL COMMENT '投稿内容',
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '投稿日時'
);

-- インデックスの作成 (検索性能向上のため)
CREATE INDEX idx_posts_created_at ON posts(created_at);

-- テーブル構造の確認
DESCRIBE posts;
```

> 📝 **ポイント**: `COMMENT` を追加することで、各カラムの用途が明確になります。また、`created_at` にインデックスを作成することで、日付順での並び替え性能が向上します。

#### テーブルの作成実行

MySQL Shell for VSCode  を使用して、作成した `schema.sql` ファイルを実行します。

**実行後の確認:**

```sql
-- テーブルが正しく作成されたかを確認
USE blog_app;
SHOW TABLES;
DESCRIBE posts;
```

### 1.4 サンプルデータの挿入と確認

#### 手動でのテストデータ挿入

テーブルが正しく作成されたことを確認するため、手動でサンプルデータを挿入してみます。

```sql
-- サンプルデータの挿入
INSERT INTO posts (title, content) VALUES 
('最初の投稿', 'データベースに保存された最初の投稿です。パート1とは異なり、サーバーを再起動してもデータが保持されます。'),
('JDBCの学習', 'Java Database Connectivity (JDBC) を使用してJavaからデータベースに接続する方法を学習しています。PreparedStatementによる安全なSQL実行が重要です。'),
('データ永続化の実現', 'インメモリーからデータベースへの移行により、真の意味でのデータ永続化が実現されました。これで本格的なWebアプリケーションと言えるでしょう。');
```

#### データ確認

挿入されたデータを確認します。

```sql
-- 全ての投稿を新しい順で取得
SELECT id, title, content, created_at 
FROM posts 
ORDER BY created_at DESC;

-- 投稿数の確認
SELECT COUNT(*) AS total_posts FROM posts;

-- 特定の投稿の取得（ID指定）
SELECT * FROM posts WHERE id = 1;
```

実行結果例：

```
+----+------------------+----------------------+---------------------+
| id | title            | content              | created_at          |
+----+------------------+----------------------+---------------------+
|  3 | データ永続化の実現  | インメモリーから...     | 2024-01-15 14:30:25 |
|  2 | JDBCの学習        | Java Database...     | 2024-01-15 14:30:20 |
|  1 | 最初の投稿         | データベースに...      | 2024-01-15 14:30:15 |
+----+------------------+----------------------+---------------------+
```

> 📝 **ポイント**: `AUTO_INCREMENT` により自動的にIDが採番され、`DEFAULT CURRENT_TIMESTAMP` により現在時刻が自動設定されていることが確認できます。

### 1.5 データベース設計の確認

#### 作成したテーブル構造の確認

```sql
-- テーブル構造の詳細確認
SHOW CREATE TABLE posts;
```

この結果から、以下の点が確認できるはずです：

1. **主キー**: `id` カラムが主キーとして設定されている
2. **自動採番**: `AUTO_INCREMENT` が設定されている
3. **制約**: `NOT NULL` 制約が適切に設定されている
4. **デフォルト値**: `created_at` にデフォルト値が設定されている
5. **インデックス**: `created_at` にインデックスが作成されている

#### パート1 との比較

<table><thead><tr><th width="160.272705078125">項目</th><th>パート1</th><th>パート2</th></tr></thead><tbody><tr><td><strong>データ保存場所</strong></td><td>アプリケーションスコープ (メモリ)</td><td>MySQL データベース</td></tr><tr><td><strong>データ永続性</strong></td><td>サーバー再起動で消失</td><td>永続的に保存</td></tr><tr><td><strong>ID管理</strong></td><td>手動採番</td><td>AUTO_INCREMENT による自動採番</td></tr><tr><td><strong>日時設定</strong></td><td>Java の LocalDateTime.now()</td><td>MySQL の CURRENT_TIMESTAMP</td></tr><tr><td><strong>データ検索</strong></td><td>List の線形探索</td><td>SQL の効率的な検索</td></tr></tbody></table>

### 1.6 次のステップの準備

これでデータベース設計とテーブル作成が完了しました。次のセクションでは、Java からこのデータベースに接続する仕組みを構築していきます。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. `blog_app` データベースが作成されている
2. `posts` テーブルが適切な構造で作成されている
3. サンプルデータが正しく挿入・取得できる
4. `schema.sql` ファイルがプロジェクトに追加されている

> 📝 **ポイント**: データベース設計は、アプリケーション全体の基盤となる重要な部分です。この段階で適切な設計を行うことで、後のJava実装がスムーズに進みます。次のセクションでは、このデータベースにJavaから接続する方法を学びます。
