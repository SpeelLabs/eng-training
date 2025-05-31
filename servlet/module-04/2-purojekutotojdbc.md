# 2. プロジェクト設定とJDBC接続

### 2.1 Maven依存関係の設定

前のセクションでデータベースとテーブルの準備ができました。このセクションでは、Java からそのデータベースに接続するための設定を行います。まず、MySQL に接続するための JDBC ドライバーをプロジェクトに追加する必要があります。

#### MySQL Connector/J の追加

パート1 で作成した `pom.xml` ファイルを開き、MySQL Connector/J の依存関係を追加します。

既存の `pom.xml` の `<dependencies>` セクションに以下を追加します：

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

> 📝 **ポイント**: MySQL Connector/J は実行時に必要なため、`scope` は指定しません (デフォルトの`compile`スコープ)。これにより、WAR ファイルにライブラリが含まれ、実行時に使用できるようになります。

#### 依存関係の更新

`pom.xml` を保存した後、依存関係を更新します。

~~**VS Code での更新方法**:~~

1. ~~VS Code下部の「Java Projects」パネルを開く~~
2. ~~プロジェクトを展開し、「Dependencies」で右クリック~~
3. ~~「Refresh Dependencies」を選択~~

**コマンドラインでの更新方法**:

```bash
mvn clean compile
```

依存関係が正しく追加されたかを確認するため、「Dependencies」→「Maven Dependencies」に `mysql-connector-j` が表示されることを確認します。

### 2.2 データベース接続設定の実装

#### DatabaseUtil クラスの基本構造

データベース接続を管理するユーティリティクラスを作成します。このクラスは、アプリケーション全体でデータベース接続を一元管理する役割を果たします。

`src/main/java/com/example/blog/util` パッケージを作成し、その中に `DatabaseUtil.java` ファイルを作成します。

**フォルダ構造の確認**:

```
src/main/java/com/example/blog/
├── model/
│   └── Post.java (パート1から継続)
├── servlet/
│   └── ... (既存のサーブレット)
└── util/
    └── DatabaseUtil.java (新規作成)
```

まず、基本的なクラス構造を作成します：

```java
package com.example.blog.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

/**
 * データベース接続を管理するユーティリティクラス
 */
public class DatabaseUtil {
    
    // データベース接続情報
    private static final String URL = "jdbc:mysql://localhost:3306/blog_app";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password"; // 実際のパスワードに変更
    
    // ここに接続メソッドを追加していきます
}
```

> 📝 **ポイント**: パスワード部分は実際のMySQLのrootパスワードに変更してください。本番環境では、パスワードをソースコードに直接記述するのではなく、設定ファイルや環境変数を使用するのが一般的です。

#### データベース接続メソッドの実装

次に、データベースに接続するためのメソッドを段階的に実装していきます。

**基本的な接続メソッド**

```java
/**
 * データベース接続を取得する
 * 
 * @return データベース接続
 * @throws SQLException データベース接続エラー
 */
public static Connection getConnection() throws SQLException {
    try {
        // JDBCドライバの明示的な読み込み
        Class.forName("com.mysql.cj.jdbc.Driver");
        
        // データベースに接続
        return DriverManager.getConnection(URL, USER, PASSWORD);
        
    } catch (ClassNotFoundException e) {
        throw new SQLException("JDBCドライバーが見つかりません: " + e.getMessage());
    }
}
```

> 📝 **ポイント**: `Class.forName()` は、JDBCドライバーを明示的に読み込むためのコードです。最新のJDBCドライバーでは必須ではありませんが、確実な動作のために記述しています。

**接続テストメソッドの追加**

データベース接続が正しく動作するかをテストするためのメソッドを追加します：

```java
/**
 * データベース接続をテストする
 * 
 * @return 接続成功の場合 true
 */
public static boolean testConnection() {
    try (Connection connection = getConnection()) {
        System.out.println("✓ データベースに正常に接続しました");
        System.out.println("接続URL: " + URL);
        return true;
    } catch (SQLException e) {
        System.err.println("✗ データベース接続に失敗しました");
        System.err.println("エラー: " + e.getMessage());
        return false;
    }
}
```

#### 完成した DatabaseUtil クラス

上記の実装を組み合わせた完成版の `DatabaseUtil.java` は以下のようになります：

```java
package com.example.blog.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

/**
 * データベース接続を管理するユーティリティクラス
 */
public class DatabaseUtil {
    
    // データベース接続情報
    private static final String URL = "jdbc:mysql://localhost:3306/blog_app";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password"; // 実際のパスワードに変更
    
    /**
     * データベース接続を取得する
     * 
     * @return データベース接続
     * @throws SQLException データベース接続エラー
     */
    public static Connection getConnection() throws SQLException {
        try {
            // JDBCドライバの明示的な読み込み
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // データベースに接続
            return DriverManager.getConnection(URL, USER, PASSWORD);
            
        } catch (ClassNotFoundException e) {
            throw new SQLException("JDBCドライバが見つかりません: " + e.getMessage());
        }
    }
    
    /**
     * データベース接続をテストする
     * 
     * @return 接続成功の場合 true
     */
    public static boolean testConnection() {
        try (Connection connection = getConnection()) {
            System.out.println("✓ データベースに正常に接続しました");
            System.out.println("接続URL: " + URL);
            return true;
        } catch (SQLException e) {
            System.err.println("✗ データベース接続に失敗しました");
            System.err.println("エラー: " + e.getMessage());
            return false;
        }
    }
    
    // テスト用メインメソッド（動作確認後に削除可能）
    public static void main(String[] args) {
        System.out.println("=== データベース接続テスト ===");
        testConnection();
        System.out.println("=== テスト終了 ===");
    }
}
```

### 2.3 接続テストの実行

#### テストの実行

作成した `DatabaseUtil` クラスが正しく動作するかをテストします。

**実行方法**:

1. VS Code で `DatabaseUtil.java` ファイルを開く
2. `main` メソッドの上にある「Run」ボタンをクリック (または、`Ctrl + F5` で実行)

**成功時の実行結果**:

```
=== データベース接続テスト ===
✓ データベースに正常に接続しました
接続URL: jdbc:mysql://localhost:3306/blog_app
=== テスト終了 ===
```

#### トラブルシューティング

接続に失敗した場合は、以下の点を確認してください：

**よくあるエラーと対処法**

**エラー1: 「Access denied for user」**

```
java.sql.SQLException: Access denied for user 'root'@'localhost'
```

**対処法**:

* MySQLのパスワードが正しいか確認
* MySQLサーバーが起動しているか確認

**エラー2: 「Unknown database」**

```
java.sql.SQLException: Unknown database 'blog_app'
```

**対処法**:

* セクション1で作成したデータベースが存在するか確認
* `SHOW DATABASES;` でデータベース一覧を確認

**エラー3: 「Communications link failure」**

```
java.sql.SQLException: Communications link failure
```

**対処法**:

* MySQLサーバーが起動しているか確認
* ポート番号（3306）が正しいか確認

### 2.4 基本的なSQL操作の確認

#### 投稿件数の取得テスト

データベース接続が正常に動作することが確認できたら、基本的なSQL操作をテストしてみます。

`DatabaseUtil` クラスに以下のテストメソッドを追加します：

```java
/**
 * 投稿件数を取得するテスト
 */
public static void testPostCount() {
    String sql = "SELECT COUNT(*) FROM posts";
    
    try (Connection connection = getConnection();
         java.sql.Statement statement = connection.createStatement();
         java.sql.ResultSet resultSet = statement.executeQuery(sql)) {
        
        if (resultSet.next()) {
            int count = resultSet.getInt(1);
            System.out.println("現在の投稿件数: " + count + "件");
        }
        
    } catch (SQLException e) {
        System.err.println("投稿件数の取得に失敗しました: " + e.getMessage());
    }
}
```

#### mainメソッドの更新

テストメソッドを実行できるように、`main` メソッドを更新します：

```java
public static void main(String[] args) {
    System.out.println("=== データベース接続テスト ===");
    
    if (testConnection()) {
        System.out.println("\n=== 投稿件数テスト ===");
        testPostCount();
    }
    
    System.out.println("=== テスト終了 ===");
}
```

#### テスト実行結果

前のセクションでサンプルデータを挿入していれば、以下のような結果が表示されるはずです：

```
=== データベース接続テスト ===
✓ データベースに正常に接続しました
接続URL: jdbc:mysql://localhost:3306/blog_app

=== 投稿件数テスト ===
現在の投稿件数: 3件
=== テスト終了 ===
```

### 2.5 次のステップの準備

これでJavaからMySQLデータベースに接続する基盤ができました。次のセクションでは、この接続を使用して実際に投稿データの保存と取得を実装していきます。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. MySQL Connector/J がプロジェクトに追加されている
2. `DatabaseUtil` クラスが作成され、接続テストが成功している
3. 基本的なSQL操作（投稿件数取得）が動作している
4. フォルダ構造が適切に整理されている

#### パート1からの変更点

<table><thead><tr><th width="160.272705078125">項目</th><th>パート1</th><th>パート2（現在）</th></tr></thead><tbody><tr><td><strong>データ保存</strong></td><td>アプリケーションスコープ</td><td>MySQL データベース</td></tr><tr><td><strong>ライブラリー</strong></td><td>Servlet API のみ</td><td>Servlet API + MySQL Connector/J</td></tr><tr><td><strong>設定ファイル</strong></td><td>pom.xml（基本）</td><td>pom.xml（データベース対応）</td></tr><tr><td><strong>ユーティリティ</strong></td><td>なし</td><td>DatabaseUtil クラス</td></tr></tbody></table>

> 📝 **ポイント**: このセクションで構築したデータベース接続の仕組みは、アプリケーション全体で使用される重要な基盤です。次のセクションでは、この基盤を使用して投稿データの永続化を実装していきます。テスト用のmainメソッドは、実装確認後に削除しても構いません。
