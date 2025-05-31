# Page 3

## 3. データベース接続ユーティリティの作成

#### 3.1 DatabaseUtil クラスの設計

アプリケーション全体でデータベース接続を統一的に管理するため、DatabaseUtil クラスを作成します。このクラスは、データベース接続の取得とエラーハンドリングなどの共通機能を提供します。

**DatabaseUtil の役割**

1. **接続管理**: データベース接続の取得と管理
2. **エラーハンドリング**: 接続エラーの適切な処理
3. **リソース管理**: Connection の適切なクローズ

**クラス設計の原則**

* **ファクトリーメソッド**: Connection の生成を担当
* **エラーハンドリング**: 適切な例外処理
* **シンプルな API**: 使いやすいインターフェース

#### 3.2 DatabaseUtil クラスの実装

**基本構造の作成**

`src/main/java/com/example/blog/util/DatabaseUtil.java` を作成します。

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
    private static final String DRIVER = "com.mysql.cj.jdbc.Driver";
    
    // クラス初期化時にドライバーを読み込み
    static {
        try {
            Class.forName(DRIVER);
            System.out.println("JDBC ドライバーが正常に読み込まれました");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException("JDBC ドライバーの読み込みに失敗しました", e);
        }
    }
    
    /**
     * プライベートコンストラクター（ユーティリティクラスのため）
     */
    private DatabaseUtil() {
        // インスタンス化を防ぐ
    }
}
```

> 📝 **ポイント**:
>
> * 接続情報を定数として直接定義しています
> * static ブロックでドライバーを事前に読み込みます
> * 実際のパスワードに `your_password` を変更してください

**Connection 取得機能**

データベース接続を取得するメインメソッドを実装します。

```java
/**
 * データベース接続を取得する
 * 
 * @return データベース接続
 * @throws SQLException データベース接続エラー
 */
public static Connection getConnection() throws SQLException {
    try {
        Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
        
        // 接続の基本設定
        connection.setAutoCommit(true);  // 基本はオートコミット
        
        return connection;
        
    } catch (SQLException e) {
        System.err.println("データベース接続エラー: " + e.getMessage());
        throw e;
    }
}
```

> 📝 **ポイント**:
>
> * `setAutoCommit(true)` により、各SQL文が自動的にコミットされます
> * エラー情報をログ出力してからre-throwしています

**接続テスト機能**

接続が正常に動作するかテストする機能を追加します。

```java
/**
 * データベース接続をテストする
 * 
 * @return 接続テストの結果
 */
public static boolean testConnection() {
    try (Connection connection = getConnection()) {
        return connection != null && !connection.isClosed();
    } catch (SQLException e) {
        System.err.println("接続テスト失敗: " + e.getMessage());
        return false;
    }
}

/**
 * データベース接続情報を表示する（デバッグ用）
 */
public static void printConnectionInfo() {
    System.out.println("=== データベース接続情報 ===");
    System.out.println("URL: " + URL);
    System.out.println("ユーザー: " + USER);
    System.out.println("ドライバー: " + DRIVER);
}
```

#### 3.3 完成した DatabaseUtil クラス

上記の機能を組み合わせた完成版の DatabaseUtil.java は以下のようになります。

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
    private static final String DRIVER = "com.mysql.cj.jdbc.Driver";
    
    // クラス初期化時にドライバーを読み込み
    static {
        try {
            Class.forName(DRIVER);
            System.out.println("JDBC ドライバーが正常に読み込まれました");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException("JDBC ドライバーの読み込みに失敗しました", e);
        }
    }
    
    /**
     * プライベートコンストラクター（ユーティリティクラスのため）
     */
    private DatabaseUtil() {
        // インスタンス化を防ぐ
    }
    
    /**
     * データベース接続を取得する
     * 
     * @return データベース接続
     * @throws SQLException データベース接続エラー
     */
    public static Connection getConnection() throws SQLException {
        try {
            Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
            
            // 接続の基本設定
            connection.setAutoCommit(true);  // 基本はオートコミット
            
            return connection;
            
        } catch (SQLException e) {
            System.err.println("データベース接続エラー: " + e.getMessage());
            throw e;
        }
    }
    
    /**
     * データベース接続をテストする
     * 
     * @return 接続テストの結果
     */
    public static boolean testConnection() {
        try (Connection connection = getConnection()) {
            return connection != null && !connection.isClosed();
        } catch (SQLException e) {
            System.err.println("接続テスト失敗: " + e.getMessage());
            return false;
        }
    }
    
    /**
     * データベース接続情報を表示する（デバッグ用）
     */
    public static void printConnectionInfo() {
        System.out.println("=== データベース接続情報 ===");
        System.out.println("URL: " + URL);
        System.out.println("ユーザー: " + USER);
        System.out.println("ドライバー: " + DRIVER);
    }
}
```

#### 3.4 DatabaseUtil の動作確認

**テストクラスの作成**

DatabaseUtil クラスが正常に動作するかテストします。

`src/main/java/com/example/blog/util/DatabaseUtilTest.java` を作成：

```java
package com.example.blog.util;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class DatabaseUtilTest {
    
    public static void main(String[] args) {
        testDatabaseUtil();
    }
    
    public static void testDatabaseUtil() {
        System.out.println("=== DatabaseUtil テスト開始 ===");
        
        // 1. 設定情報の確認
        DatabaseUtil.printConnectionInfo();
        
        // 2. 接続テスト
        if (DatabaseUtil.testConnection()) {
            System.out.println("✅ 接続テスト成功");
        } else {
            System.out.println("❌ 接続テスト失敗");
            return;
        }
        
        // 3. 実際の接続とクエリ実行
        testActualConnection();
        
        // 4. 設定値取得テスト
        testPropertyAccess();
        
        System.out.println("=== DatabaseUtil テスト終了 ===");
    }
    
    private static void testActualConnection() {
        System.out.println("\n--- 実際の接続テスト ---");
        
        try (Connection connection = DatabaseUtil.getConnection()) {
            System.out.println("✅ 接続取得成功");
            
            // 簡単なクエリを実行
            String sql = "SELECT COUNT(*) AS post_count FROM posts";
            try (Statement statement = connection.createStatement();
                 ResultSet resultSet = statement.executeQuery(sql)) {
                
                if (resultSet.next()) {
                    int count = resultSet.getInt("post_count");
                    System.out.println("✅ クエリ実行成功 - 投稿数: " + count);
                }
            }
            
        } catch (SQLException e) {
            System.err.println("❌ 接続エラー: " + e.getMessage());
        }
    }
    
    private static void testPropertyAccess() {
        System.out.println("\n--- 設定値取得テスト ---");
        
        String url = DatabaseUtil.getProperty("db.url");
        String username = DatabaseUtil.getProperty("db.username");
        int maxConnections = DatabaseUtil.getIntProperty("db.maxConnections", 5);
        
        System.out.println("URL: " + url);
        System.out.println("ユーザー名: " + username);
        System.out.println("最大接続数: " + maxConnections);
    }
}
```

**テストの実行**

作成したテストを実行して、DatabaseUtil が正常に動作することを確認します。

**期待される出力例**:

```
=== DatabaseUtil テスト開始 ===
データベース設定が正常に読み込まれました
=== データベース接続情報 ===
URL: jdbc:mysql://localhost:3306/blog_app
ユーザー: root
初期化状態: 完了
✅ 接続テスト成功

--- 実際の接続テスト ---
✅ 接続取得成功
✅ クエリ実行成功 - 投稿数: 5

--- 設定値取得テスト ---
URL: jdbc:mysql://localhost:3306/blog_app
ユーザー名: root
最大接続数: 10
=== DatabaseUtil テスト終了 ===
```

#### 3.5 使用方法とベストプラクティス

**基本的な使用方法**

```java
// データベース接続の取得と使用
try (Connection connection = DatabaseUtil.getConnection()) {
    // SQL操作を実行
    // try-with-resources により自動的にクローズされる
} catch (SQLException e) {
    // エラーハンドリング
}
```

**ベストプラクティス**

1. **try-with-resources の使用**: Connection の確実なクローズ
2. **適切なエラーハンドリング**: SQLException の捕捉と処理
3. **接続の再利用避ける**: 各操作で新しい接続を取得
4. **設定の外部化**: database.properties による設定管理

**避けるべきパターン**

```java
// ❌ 接続をクローズしない
Connection connection = DatabaseUtil.getConnection();
// 処理...
// connection.close() を忘れる

// ❌ 接続を長時間保持
Connection connection = DatabaseUtil.getConnection();
// 長時間の処理...

// ❌ エラーハンドリングなし
Connection connection = DatabaseUtil.getConnection(); // SQLException未処理
```

#### 3.6 次のステップへの準備

DatabaseUtil クラスの実装により、以降のセクションで以下の利点があります：

1. **統一された接続管理**: 全てのDAO クラスで同じ方法で接続取得
2. **設定の一元管理**: database.properties での設定管理
3. **エラー処理の標準化**: 共通のエラーハンドリング
4. **保守性の向上**: 接続ロジックの変更が一箇所で済む

次のセクションでは、この DatabaseUtil を使用して、実際のデータベース操作（読取・作成）を実装していきます。

> 📝 **ポイント**: DatabaseUtil は、アプリケーション全体のデータベースアクセスの基盤となる重要なクラスです。適切に実装することで、以降の開発が大幅に効率化されます。コネクションプールなどの高度な機能は将来の拡張として考慮し、現時点では基本的な機能に集中しています。
