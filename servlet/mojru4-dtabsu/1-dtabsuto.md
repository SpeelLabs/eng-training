# 1. データベース設計と環境構築

#### 1.1 ブログアプリケーション用データベースの設計

パート1で作成したブログアプリケーションのデータを永続化するために、適切なデータベース設計を行います。まずは、現在のPostクラスの構造を分析し、それに対応するデータベーステーブルを設計していきます。

**現在のPostクラスの分析**

パート1で作成したPostクラスは以下の属性を持っています：

```java
public class Post {
    private int id;              // 投稿ID
    private String title;        // タイトル
    private String content;      // 内容
    private LocalDateTime createdAt;  // 作成日時
}
```

この構造をデータベーステーブルに対応させる必要があります。

**posts テーブルの設計**

Postクラスに対応する `posts` テーブルを以下のように設計します：

| カラム名         | データ型         | 制約                                                      | 説明           |
| ------------ | ------------ | ------------------------------------------------------- | ------------ |
| `id`         | INT          | PRIMARY KEY, AUTO\_INCREMENT                            | 投稿を一意に識別するID |
| `title`      | VARCHAR(200) | NOT NULL                                                | 投稿のタイトル      |
| `content`    | TEXT         | NOT NULL                                                | 投稿の内容        |
| `created_at` | TIMESTAMP    | DEFAULT CURRENT\_TIMESTAMP                              | 投稿作成日時       |
| `updated_at` | TIMESTAMP    | DEFAULT CURRENT\_TIMESTAMP ON UPDATE CURRENT\_TIMESTAMP | 投稿更新日時       |

> 📝 **ポイント**: `AUTO_INCREMENT` を使用することで、新しい投稿が作成されるたびに自動的にIDが割り当てられます。また、`updated_at` カラムを追加することで、将来の編集機能に対応できます。

#### 1.2 データベースとテーブルの作成

**データベースの作成**

MySQL Shell for VS Code または MySQL Workbench を使用して、ブログアプリケーション用のデータベースを作成します。

1. **VS Code でMySQLに接続**
   * MySQL Shell for VS Code 拡張機能を使用
   * 左側の MySQL パネルから接続を作成
   * `localhost:3306` に `root` ユーザーで接続
2. **新しいデータベースを作成**

```sql
-- ブログアプリケーション用のデータベースを作成
CREATE DATABASE IF NOT EXISTS blog_app;

-- 作成したデータベースを選択
USE blog_app;

-- データベースが作成されたことを確認
SHOW DATABASES;
```

> 📝 **ポイント**: `IF NOT EXISTS` を使用することで、同じ名前のデータベースが既に存在していてもエラーになりません。また、データベース名は小文字とアンダースコアを使用する慣習に従っています。

**postsテーブルの作成**

次に、投稿データを格納するためのpostsテーブルを作成します。

```sql
-- posts テーブルの作成
CREATE TABLE posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

**テーブル構造の確認**

作成したテーブルの構造を確認します。

```sql
-- テーブル構造の確認
DESCRIBE posts;

-- または
SHOW CREATE TABLE posts;
```

**期待される出力例**:

```
+------------+--------------+------+-----+-------------------+-------------------+
| Field      | Type         | Null | Key | Default           | Extra             |
+------------+--------------+------+-----+-------------------+-------------------+
| id         | int          | NO   | PRI | NULL              | auto_increment    |
| title      | varchar(200) | NO   |     | NULL              |                   |
| content    | text         | NO   |     | NULL              |                   |
| created_at | timestamp    | YES  |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
| updated_at | timestamp    | YES  |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
+------------+--------------+------+-----+-------------------+-------------------+
```

> 📝 **ポイント**: `DESCRIBE` コマンドでテーブルの構造を確認できます。各カラムのデータ型、NULL許可、キー設定、デフォルト値が正しく設定されていることを確認しましょう。

#### 1.3 サンプルデータの投入

テーブルが正しく作成されたことを確認するため、いくつかのサンプルデータを投入します。

```sql
-- サンプル投稿データの投入
INSERT INTO posts (title, content) VALUES
('データベース連携の開始', 'パート1で作成したブログアプリケーションをデータベース対応に変更します。これまでメモリ内で管理していた投稿データを、MySQLデータベースに永続化することで、より実用的なアプリケーションに発展させていきます。'),
('JDBCの基礎', 'Java Database Connectivity (JDBC) は、Javaアプリケーションからデータベースにアクセスするための標準APIです。PreparedStatementを使用することで、SQLインジェクション攻撃を防ぎながら安全にデータベース操作を行うことができます。'),
('データベース設計の重要性', '適切なデータベース設計は、アプリケーションのパフォーマンスと保守性に大きく影響します。正規化、インデックス、制約の適切な設定により、データの整合性を保ちながら効率的な操作を実現できます。');
```

**データの確認**

投入したサンプルデータを確認します。

```sql
-- 投入したデータの確認
SELECT * FROM posts ORDER BY created_at DESC;
```

**期待される出力例**:

```
+----+---------------------------+--------------------------------------------------------+---------------------+---------------------+
| id | title                     | content                                                | created_at          | updated_at          |
+----+---------------------------+--------------------------------------------------------+---------------------+---------------------+
|  3 | データベース設計の重要性  | 適切なデータベース設計は、アプリケーションの...        | 2024-01-15 10:30:15 | 2024-01-15 10:30:15 |
|  2 | JDBCの基礎               | Java Database Connectivity (JDBC) は、Java...         | 2024-01-15 10:30:15 | 2024-01-15 10:30:15 |
|  1 | データベース連携の開始    | パート1で作成したブログアプリケーションを...          | 2024-01-15 10:30:15 | 2024-01-15 10:30:15 |
+----+---------------------------+--------------------------------------------------------+---------------------+---------------------+
```

> 📝 **ポイント**: `ORDER BY created_at DESC` により、新しい投稿が上に表示されます。これは後でアプリケーションで実装する一覧表示の順序と同じです。また、IDが自動的に採番され、日時も自動的に設定されていることが確認できます。

#### 1.4 Mavenプロジェクトの依存関係追加

既存のブログアプリケーションプロジェクトに、MySQL接続のための依存関係を追加します。

**pom.xmlへのMySQL Connector/J追加**

`pom.xml` ファイルを開き、dependencies セクションに MySQL Connector/J を追加します。

```xml
<dependencies>
    <!-- 既存の Jakarta Servlet API -->
    <dependency>
        <groupId>jakarta.servlet</groupId>
        <artifactId>jakarta.servlet-api</artifactId>
        <version>5.0.0</version>
        <scope>provided</scope>
    </dependency>
    
    <!-- MySQL Connector/J を追加 -->
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <version>8.4.0</version>
    </dependency>
</dependencies>
```

> 📝 **ポイント**: Jakarta Servlet API には `<scope>provided</scope>` を指定していますが、MySQL Connector/J には指定していません。これは、Servlet API はTomcatサーバーが提供するためWARファイルに含める必要がないのに対し、MySQL Connector/J はアプリケーション独自の依存関係なので、WARファイルに含めてサーバーに配布する必要があるためです。

**依存関係の更新**

pom.xml を保存した後、依存関係を更新します。

**方法1**: VS Code の機能を使用

* VS Code 下部の「Java Projects」パネルを開く
* プロジェクトを展開し、「Dependencies」で右クリック
* 「Refresh Dependencies」を選択

**方法2**: ターミナルコマンドを使用

```bash
# プロジェクトディレクトリで実行
mvn clean compile
```

> 📝 **ポイント**: pom.xml を変更した後は、必ず依存関係を更新してください。更新が完了すると、MySQL Connector/J がプロジェクトで使用できるようになります。

#### 1.5 データベース接続テスト

データベースへの接続が正常に行えるかテストするプログラムを作成します。

**接続テストクラスの作成**

`src/main/java/com/example/blog/util` パッケージを作成し、その中に `DatabaseConnectionTest.java` ファイルを作成します。

```java
package com.example.blog.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConnectionTest {
    
    // データベース接続情報
    private static final String URL = "jdbc:mysql://localhost:3306/blog_app";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password"; // 実際のパスワードに変更
    
    public static void main(String[] args) {
        System.out.println("=== データベース接続テスト ===");
        
        try {
            // JDBCドライバの読み込み
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // データベースに接続
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
                
                System.out.println("✓ データベースに正常に接続しました！");
                System.out.println("接続先: " + URL);
                
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("✗ JDBCドライバが見つかりません");
            System.err.println("エラー: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("✗ 接続に失敗しました");
            System.err.println("エラー: " + e.getMessage());
        }
        
        System.out.println("=== テスト終了 ===");
    }
}
```

> 📝 **ポイント**: このテストクラスでは、接続テストとデータ取得テストの両方を実行します。`try-with-resources` 構文を使用することで、リソースが確実に解放されます。また、JDBCドライバーの明示的な読み込みを行っています。

**テストの実行**

作成したテストクラスを実行します。

**実行方法**:

1. `DatabaseConnectionTest.java` ファイルを開く
2. `main` メソッドの上にある「Run」ボタンをクリック
3. または、`Ctrl + F5` で実行

**成功時の実行結果例**:

```
=== データベース接続テスト ===
✓ データベースに正常に接続しました！
接続先: jdbc:mysql://localhost:3306/blog_app
=== テスト終了 ===
```

#### 1.6 トラブルシューティング

データベース接続でよくあるエラーとその対処法を説明します。

**エラー1: Access denied**

```
java.sql.SQLException: Access denied for user 'root'@'localhost'
```

**対処法**:

* パスワードが正しいか確認
* MySQLサーバーが起動しているか確認
* ユーザー名とパスワードを再確認

**エラー2: Unknown database**

```
java.sql.SQLException: Unknown database 'blog_app'
```

**対処法**:

* データベース名が正しいか確認
* `CREATE DATABASE blog_app;` でデータベースが作成されているか確認

**エラー3: Table doesn't exist**

```
java.sql.SQLException: Table 'blog_app.posts' doesn't exist
```

**対処法**:

* `USE blog_app;` でデータベースを選択しているか確認
* `CREATE TABLE posts ...` でテーブルが作成されているか確認

**エラー4: Driver not found**

```
java.lang.ClassNotFoundException: com.mysql.cj.jdbc.Driver
```

**対処法**:

* MySQL Connector/J がpom.xmlに正しく記述されているか確認
* Maven の依存関係が更新されているか確認

> 📝 **ポイント**: エラーが発生した場合は、エラーメッセージを注意深く読み、上記のチェックリストで順番に確認してください。多くの場合、設定の誤りが原因です。

#### 1.7 接続情報の設定ファイル化

ハードコーディングされた接続情報を設定ファイルに分離し、より保守しやすい構造にします。

**database.properties ファイルの作成**

`src/main/resources` ディレクトリを作成し、その中に `database.properties` ファイルを作成します。

```properties
# データベース接続設定
db.url=jdbc:mysql://localhost:3306/blog_app
db.user=root
db.password=your_password
db.driver=com.mysql.cj.jdbc.Driver

# 接続プール設定（将来の拡張用）
db.maxConnections=20
db.timeout=30000
```

**DatabaseUtilクラスの作成**

設定ファイルを読み込むためのユーティリティクラスを作成します。

```java
package com.example.blog.util;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class DatabaseUtil {
    
    private static Properties properties = new Properties();
    
    // 静的初期化ブロックで設定ファイルを読み込み
    static {
        try (InputStream input = DatabaseUtil.class.getClassLoader()
                .getResourceAsStream("database.properties")) {
            
            if (input != null) {
                properties.load(input);
                // JDBCドライバーを読み込み
                Class.forName(properties.getProperty("db.driver"));
            } else {
                throw new RuntimeException("database.properties ファイルが見つかりません");
            }
            
        } catch (IOException | ClassNotFoundException e) {
            throw new RuntimeException("データベース設定の初期化に失敗しました", e);
        }
    }
    
    /**
     * データベース接続を取得します
     * 
     * @return データベース接続
     * @throws SQLException データベース接続エラー
     */
    public static Connection getConnection() throws SQLException {
        String url = properties.getProperty("db.url");
        String user = properties.getProperty("db.user");
        String password = properties.getProperty("db.password");
        
        return DriverManager.getConnection(url, user, password);
    }
    
    /**
     * 接続テスト用メソッド
     * 
     * @return 接続が成功した場合true
     */
    public static boolean testConnection() {
        try (Connection connection = getConnection()) {
            return connection != null && !connection.isClosed();
        } catch (SQLException e) {
            System.err.println("接続テストに失敗しました: " + e.getMessage());
            return false;
        }
    }
}
```

> 📝 **ポイント**: このユーティリティクラスにより、データベース接続の設定が一元化され、将来的な変更が容易になります。また、設定ファイルを使用することで、本番環境と開発環境で異なる設定を使い分けることも可能になります。

**DatabaseUtilの動作確認**

作成したDatabaseUtilクラスが正しく動作することを確認します。

```java
// DatabaseConnectionTest.java のmainメソッドに追加で確認
public static void testWithDatabaseUtil() {
    System.out.println("\n=== DatabaseUtilテスト ===");
    
    try (Connection connection = DatabaseUtil.getConnection()) {
        System.out.println("✓ DatabaseUtilで接続できました");
    } catch (SQLException e) {
        System.err.println("✗ DatabaseUtilでエラーが発生しました: " + e.getMessage());
    }
}

これで、データベース設計と環境構築の基礎が完了しました。次のセクションでは、この環境を使用して基本的なデータベース操作を実装していきます。

> 📝 **まとめ**: このセクションでは、データベース設計からテーブル作成、接続テスト、設定ファイル化まで段階的に実装しました。各ステップで動作確認を行うことで、確実にデータベース連携の基盤を構築できました。次は、この基盤の上に実際のCRUD操作を実装していきます。
```
