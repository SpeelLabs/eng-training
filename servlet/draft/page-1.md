# Page 1

## 1. データベース設計とテーブル作成

#### 1.1 データベースの設計

パート 1 で作成したブログアプリケーションのデータを永続化するために、MySQL データベースのスキーマを設計します。現在のアプリケーションで使用している Post クラスの構造を基に、適切なテーブル設計を行います。

**Post クラスの分析**

パート 1 で作成した Post クラスの構造を再確認します。

```java
public class Post {
    private int id;                    // 投稿の一意識別子
    private String title;              // 投稿のタイトル
    private String content;            // 投稿の内容
    private LocalDateTime createdAt;   // 投稿の作成日時
}
```

**データベーステーブルの設計**

この Post クラスに対応するテーブル構造を設計します。

**posts テーブル**:

<table><thead><tr><th width="146.27276611328125">カラム名</th><th width="165.0909423828125">データ型</th><th>制約</th><th>説明</th></tr></thead><tbody><tr><td>id</td><td>INT</td><td>PRIMARY KEY, AUTO_INCREMENT</td><td>投稿の一意識別子</td></tr><tr><td>title</td><td>VARCHAR(200)</td><td>NOT NULL</td><td>投稿のタイトル</td></tr><tr><td>content</td><td>TEXT</td><td>NOT NULL</td><td>投稿の内容</td></tr><tr><td>created_at</td><td>TIMESTAMP</td><td>DEFAULT CURRENT_TIMESTAMP</td><td>投稿の作成日時</td></tr></tbody></table>

> 📝 **ポイント**:
>
> * `AUTO_INCREMENT` により ID が自動採番されます
> * `VARCHAR(200)` でタイトルの長さを制限します
> * `TEXT` 型で長い内容にも対応します
> * `TIMESTAMP` で作成日時を自動設定します

#### 1.2 データベースとテーブルの作成

**データベースの作成**

MySQL にログインして、ブログアプリケーション用のデータベースを作成します。

```sql
-- ブログアプリケーション用のデータベースを作成
CREATE DATABASE IF NOT EXISTS blog_app;

-- 作成したデータベースを使用
USE blog_app;

-- データベースが作成されたことを確認
SHOW DATABASES;
```

**posts テーブルの作成**

投稿データを格納するテーブルを作成します。

```sql
-- 投稿テーブルの作成
CREATE TABLE posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- インデックスの追加（検索性能向上のため）
    INDEX idx_created_at (created_at),
    INDEX idx_title (title)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

> 📝 **ポイント**:
>
> * `ENGINE=InnoDB` でトランザクション対応
> * `DEFAULT CHARSET=utf8mb4` で日本語対応
> * インデックスで検索性能を向上

**テーブル構造の確認**

作成したテーブルの構造を確認します。

```sql
-- テーブル構造の確認
DESCRIBE posts;

-- テーブル一覧の確認
SHOW TABLES;
```

#### 1.3 サンプルデータの投入

開発とテストのために、サンプルデータを投入します。

**基本的なサンプルデータ**

```sql
-- サンプル投稿データの投入
INSERT INTO posts (title, content) VALUES
('データベース連携の開始', 
 'パート1のインメモリーアプリケーションから、MySQLデータベースを使用したアプリケーションに移行します。データの永続化により、サーバー再起動後もデータが保持されるようになります。'),

('JDBCとPreparedStatementの活用', 
 'Java Database Connectivity (JDBC) を使用してデータベースに接続し、PreparedStatement で安全なSQL実行を行います。SQLインジェクション対策も含めて実装していきます。'),

('LocalDateTimeとTIMESTAMPの連携', 
 'JavaのLocalDateTimeとMySQLのTIMESTAMP型を連携させて、現代的な日時処理を実現します。タイムゾーンの考慮も含めて適切にデータを管理します。'),

('DAOパターンの準備', 
 'モジュール5では、Data Access Object (DAO) パターンを実装して、データアクセス層を分離します。この準備として、基本的なCRUD操作を実装していきます。'),

('エラーハンドリングの実装', 
 'データベース操作では様々なエラーが発生する可能性があります。SQLException の適切な処理と、ユーザーへの分かりやすいエラーメッセージ表示を実装します。');
```

**データの確認**

投入したデータを確認します。

```sql
-- 投入したデータの確認
SELECT * FROM posts ORDER BY created_at DESC;

-- 投稿数の確認
SELECT COUNT(*) AS total_posts FROM posts;

-- 最新の投稿の確認
SELECT id, title, LEFT(content, 50) AS content_preview, created_at 
FROM posts 
ORDER BY created_at DESC 
LIMIT 3;
```

#### 1.4 テーブル設計の説明

**主キーとAUTO\_INCREMENT**

```sql
id INT AUTO_INCREMENT PRIMARY KEY
```

* **AUTO\_INCREMENT**: 新しいレコード挿入時に自動的に ID が採番される
* **PRIMARY KEY**: テーブル内でレコードを一意に識別する
* **INT 型**: 十分な範囲（約21億件）をカバー

**文字列データの設計**

```sql
title VARCHAR(200) NOT NULL,
content TEXT NOT NULL
```

* **VARCHAR(200)**: 可変長文字列、タイトル用に適切なサイズ
* **TEXT**: 大きなテキスト用、ブログ内容に適している
* **NOT NULL**: 必須フィールドとして設定

**日時データの設計**

```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

* **TIMESTAMP**: 日時データの標準的な型
* **DEFAULT CURRENT\_TIMESTAMP**: 挿入時に自動的に現在時刻が設定
* Java の LocalDateTime と相互変換可能

**インデックスの設計**

```sql
INDEX idx_created_at (created_at),
INDEX idx_title (title)
```

* **created\_at インデックス**: 日時順のソートで使用
* **title インデックス**: タイトル検索で使用
* 検索性能の向上が期待できる

#### 1.5 データベース設計のベストプラクティス

**命名規則**

* **テーブル名**: 複数形（posts, comments など）
* **カラム名**: スネークケース（created\_at, updated\_at など）
* **インデックス名**: 意味のある接頭辞（idx\_, unique\_ など）

**データ型の選択**

```sql
-- 適切なデータ型の選択例
id          INT                    -- 整数の主キー
title       VARCHAR(200)           -- 短いテキスト
content     TEXT                   -- 長いテキスト  
created_at  TIMESTAMP              -- 日時
is_published BOOLEAN DEFAULT FALSE -- 真偽値
view_count  INT DEFAULT 0          -- カウンター
```

**制約の設定**

```sql
-- 制約の例
NOT NULL                    -- 必須フィールド
DEFAULT CURRENT_TIMESTAMP   -- デフォルト値
CHECK (LENGTH(title) >= 1)  -- チェック制約
UNIQUE (slug)              -- ユニーク制約
```

#### 1.6 次のステップの準備

このセクションで作成したデータベース構造は、次のセクションで実装する JDBC 接続の基盤となります。

**確認事項**

作成したデータベース環境を確認してください：

1. **データベースの存在**: `blog_app` データベースが作成されている
2. **テーブルの構造**: `posts` テーブルが適切に作成されている
3. **サンプルデータ**: 5件の投稿データが投入されている
4. **インデックス**: 必要なインデックスが設定されている

**接続情報の確認**

次のセクションで使用する接続情報を確認してください：

* **ホスト**: localhost
* **ポート**: 3306
* **データベース名**: blog\_app
* **ユーザー名**: root（または適切なユーザー）
* **パスワード**: 設定したパスワード

> 📝 **ポイント**: データベース設計は、アプリケーションの性能と保守性に大きく影響します。このモジュールでは基本的な設計を学び、後のモジュールでより高度な設計パターンを学習していきます。適切な設計により、データの整合性確保と効率的なデータアクセスが可能になります。
