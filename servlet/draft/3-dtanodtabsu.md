# 3. 投稿データのデータベース永続化

### 3.1 Post クラスのデータベース対応

前のセクションでデータベース接続の基盤ができました。このセクションでは、パート1で作成した `Post` クラスをデータベース対応に拡張し、実際にデータの保存と取得を実装していきます。

#### 現在の Post クラスの確認

まず、パート1で作成した `Post` クラスの構造を確認しましょう。

```java
// パート1の Post クラス（確認）
public class Post {
    private int id;
    private String title;
    private String content;
    private LocalDateTime createdAt;
    
    // コンストラクター、ゲッター、セッターなどは省略
}
```

この構造は、前のセクションで作成したデータベースのテーブル構造と一致しています。そのため、大きな修正は必要ありません。

#### データベース用コンストラクターの追加

データベースから取得したデータでPostオブジェクトを作成するための専用コンストラクターを追加します。

既存の `Post.java` ファイルに以下のコンストラクターを追加してください：

```java
/**
 * データベースから取得したデータ用のコンストラクター
 * 
 * @param id データベースで自動生成されたID
 * @param title 投稿タイトル
 * @param content 投稿内容
 * @param createdAt データベースに保存された作成日時
 */
public Post(int id, String title, String content, LocalDateTime createdAt) {
    this.id = id;
    this.title = title;
    this.content = content;
    this.createdAt = createdAt;
}
```

> 📝 **ポイント**: このコンストラクターは、データベースから `SELECT` で取得したデータからPostオブジェクトを作成する際に使用します。IDと作成日時はデータベース側で管理されるため、これらの値も含めて設定します。

### 3.2 基本的なデータベース操作の実装

#### 投稿保存機能の実装

まず、新しい投稿をデータベースに保存する機能を実装します。`DatabaseUtil` クラスに投稿保存用のメソッドを追加します。

```java
/**
 * 新しい投稿をデータベースに保存する
 * 
 * @param post 保存する投稿データ
 * @return 保存成功の場合 true
 */
public static boolean savePost(Post post) {
    String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
    
    try (Connection connection = getConnection();
         java.sql.PreparedStatement statement = connection.prepareStatement(sql)) {
        
        // パラメーターを設定
        statement.setString(1, post.getTitle());
        statement.setString(2, post.getContent());
        
        // SQL を実行
        int rowsAffected = statement.executeUpdate();
        
        if (rowsAffected > 0) {
            System.out.println("✓ 投稿が正常に保存されました");
            return true;
        } else {
            System.err.println("✗ 投稿の保存に失敗しました");
            return false;
        }
        
    } catch (SQLException e) {
        System.err.println("データベースエラー: " + e.getMessage());
        return false;
    }
}
```

> 📝 **ポイント**: `PreparedStatement` を使用することで、SQLインジェクション攻撃を防ぐことができます。`?` プレースホルダーに値を設定することで、安全にSQL文を実行できます。`created_at` は設定していませんが、これはテーブル定義で `DEFAULT CURRENT_TIMESTAMP` を設定しているため、自動的に現在時刻が設定されます。

#### 投稿一覧取得機能の実装

次に、データベースから投稿一覧を取得する機能を実装します。

```java
/**
 * すべての投稿を新しい順で取得する
 * 
 * @return 投稿のリスト
 */
public static java.util.List<Post> getAllPosts() {
    java.util.List<Post> posts = new java.util.ArrayList<>();
    String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
    
    try (Connection connection = getConnection();
         java.sql.Statement statement = connection.createStatement();
         java.sql.ResultSet resultSet = statement.executeQuery(sql)) {
        
        while (resultSet.next()) {
            // データベースから値を取得
            int id = resultSet.getInt("id");
            String title = resultSet.getString("title");
            String content = resultSet.getString("content");
            java.sql.Timestamp timestamp = resultSet.getTimestamp("created_at");
            
            // Timestamp を LocalDateTime に変換
            java.time.LocalDateTime createdAt = timestamp.toLocalDateTime();
            
            // Post オブジェクトを作成してリストに追加
            Post post = new Post(id, title, content, createdAt);
            posts.add(post);
        }
        
        System.out.println("✓ " + posts.size() + "件の投稿を取得しました");
        
    } catch (SQLException e) {
        System.err.println("投稿一覧の取得に失敗しました: " + e.getMessage());
    }
    
    return posts;
}
```

> 📝 **ポイント**: `ORDER BY created_at DESC` により、新しい投稿から順番に取得されます。`Timestamp.toLocalDateTime()` を使用して、データベースの `DATETIME` 型を Java の `LocalDateTime` に変換しています。

#### ID指定での投稿取得機能の実装

特定のIDの投稿を取得する機能も実装します。

```java
/**
 * 指定されたIDの投稿を取得する
 * 
 * @param id 投稿ID
 * @return 投稿データ（見つからない場合は null）
 */
public static Post getPostById(int id) {
    String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
    
    try (Connection connection = getConnection();
         java.sql.PreparedStatement statement = connection.prepareStatement(sql)) {
        
        // パラメーターを設定
        statement.setInt(1, id);
        
        try (java.sql.ResultSet resultSet = statement.executeQuery()) {
            if (resultSet.next()) {
                // データベースから値を取得
                String title = resultSet.getString("title");
                String content = resultSet.getString("content");
                java.sql.Timestamp timestamp = resultSet.getTimestamp("created_at");
                java.time.LocalDateTime createdAt = timestamp.toLocalDateTime();
                
                // Post オブジェクトを作成
                return new Post(id, title, content, createdAt);
            }
        }
        
    } catch (SQLException e) {
        System.err.println("投稿の取得に失敗しました: " + e.getMessage());
    }
    
    return null; // 見つからない場合
}
```

### 3.3 データベース操作のテスト

#### 投稿保存のテスト

作成した機能をテストするため、`DatabaseUtil` クラスの `main` メソッドを更新します。

```java
public static void main(String[] args) {
    System.out.println("=== データベース操作テスト ===");
    
    if (!testConnection()) {
        return;
    }
    
    // 投稿保存のテスト
    System.out.println("\n=== 投稿保存テスト ===");
    Post newPost = new Post("JDBCテスト投稿", "データベースへの保存テストです。PreparedStatementを使用して安全にデータを保存しています。");
    boolean saved = savePost(newPost);
    
    if (saved) {
        // 投稿一覧取得のテスト
        System.out.println("\n=== 投稿一覧取得テスト ===");
        java.util.List<Post> posts = getAllPosts();
        
        for (Post post : posts) {
            System.out.println("ID: " + post.getId() + ", タイトル: " + post.getTitle());
        }
        
        // ID指定取得のテスト
        if (!posts.isEmpty()) {
            System.out.println("\n=== ID指定取得テスト ===");
            Post firstPost = posts.get(0);
            Post retrievedPost = getPostById(firstPost.getId());
            
            if (retrievedPost != null) {
                System.out.println("取得成功: " + retrievedPost.getTitle());
            }
        }
    }
    
    System.out.println("\n=== テスト終了 ===");
}
```

#### テストの実行

更新された `DatabaseUtil` クラスを実行して、データベース操作が正しく動作することを確認します。

**期待される実行結果例**:

```
=== データベース操作テスト ===
✓ データベースに正常に接続しました
接続URL: jdbc:mysql://localhost:3306/blog_app

=== 投稿保存テスト ===
✓ 投稿が正常に保存されました

=== 投稿一覧取得テスト ===
✓ 4件の投稿を取得しました
ID: 4, タイトル: JDBCテスト投稿
ID: 3, タイトル: データ永続化の実現
ID: 2, タイトル: JDBCの学習
ID: 1, タイトル: 最初の投稿

=== ID指定取得テスト ===
取得成功: JDBCテスト投稿

=== テスト終了 ===
```

### 3.4 完成したDatabaseUtilクラス

これまでの実装を組み合わせた完成版の `DatabaseUtil.java` の重要部分を示します：

```java
package com.example.blog.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

/**
 * データベース接続を管理するユーティリティクラス
 */
public class DatabaseUtil {
    
    // データベース接続情報
    private static final String URL = "jdbc:mysql://localhost:3306/blog_app";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password";
    
    // 接続メソッド（前のセクションから継続）
    public static Connection getConnection() throws SQLException { /* 実装済み */ }
    
    // テストメソッド（前のセクションから継続）
    public static boolean testConnection() { /* 実装済み */ }
    
    // 新規追加メソッド
    public static boolean savePost(Post post) { /* 上記で実装 */ }
    public static List<Post> getAllPosts() { /* 上記で実装 */ }
    public static Post getPostById(int id) { /* 上記で実装 */ }
    
    // テスト用mainメソッド
    public static void main(String[] args) { /* 上記で実装 */ }
}
```

### 3.5 パート1との動作比較

#### データの永続性確認

パート1との最大の違いは、データの永続性です。以下の点を確認してみましょう：

1. **アプリケーションを再起動しても投稿データが保持される**
2. **複数の実行で投稿が累積される**
3. **データベースツールからも同じデータが確認できる**

#### MySQLでの確認

VS Code の MySQL 拡張機能や MySQL コマンドラインで以下のSQLを実行して、Javaから保存されたデータを確認できます：

```sql
USE blog_app;
SELECT * FROM posts ORDER BY created_at DESC;
```

### 3.6 次のステップの準備

これで、Javaからデータベースへの基本的な操作（保存・取得）が実装できました。次のセクションでは、パート1で作成したサーブレットをこのデータベース操作を使用するように修正していきます。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. `Post` クラスにデータベース用コンストラクターが追加されている
2. `DatabaseUtil` に投稿の保存・取得メソッドが実装されている
3. テストが正常に実行され、データベースにデータが保存されている
4. MySQLツールからもデータが確認できる

#### 実装した機能

| 機能         | メソッド               | 説明              |
| ---------- | ------------------ | --------------- |
| **投稿保存**   | `savePost(Post)`   | 新しい投稿をデータベースに保存 |
| **投稿一覧取得** | `getAllPosts()`    | すべての投稿を新しい順で取得  |
| **ID指定取得** | `getPostById(int)` | 特定のIDの投稿を取得     |

> 📝 **ポイント**: このセクションで実装した機能は、Webアプリケーションの基盤となる重要な部分です。次のセクションでは、この基盤を使用してパート1のサーブレットをデータベース対応に修正していきます。テスト用のmainメソッドは、各サーブレットの実装が完了した後に削除しても構いません。
