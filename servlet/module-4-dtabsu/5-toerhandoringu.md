# 5. 接続管理とエラーハンドリング

### 5.1 現在の実装の問題点

これまでのセクションで基本的なデータベース操作を実装しましたが、改善すべき点がいくつかあります。このセクションでは、より堅牢で保守しやすいコードにするための改善を行います。

#### 現在のコードの確認

各サーブレットで以下のようなパターンを使用していました。

```java
try (Connection connection = DatabaseUtil.getConnection();
     PreparedStatement pstmt = connection.prepareStatement(sql)) {
    
    // データベース操作
    
} catch (SQLException e) {
    System.err.println("データベースエラー: " + e.getMessage());
    // 個別のエラー処理
}
```

この実装には以下の課題があります：

* エラーメッセージが統一されていない
* ログ出力が不十分
* エラー処理の重複
* デバッグ情報の不足

> 📝 **ポイント**: try-with-resources構文により接続は自動的に閉じられますが、エラー処理とログ出力を改善することで、運用時の問題解決を容易にできます。

### 5.2 DatabaseUtilクラスの改善

まず、DatabaseUtilクラスにログ機能とエラーハンドリングを追加します。

#### ログ出力機能の追加

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
    
    static {
        try (InputStream input = DatabaseUtil.class.getClassLoader()
                .getResourceAsStream("database.properties")) {
            
            if (input != null) {
                properties.load(input);
                Class.forName(properties.getProperty("db.driver"));
                System.out.println("データベース設定が正常に読み込まれました");
            } else {
                throw new RuntimeException("database.properties ファイルが見つかりません");
            }
            
        } catch (IOException | ClassNotFoundException e) {
            System.err.println("データベース設定の初期化に失敗しました: " + e.getMessage());
            throw new RuntimeException("データベース設定の初期化に失敗しました", e);
        }
    }
    
    /**
     * データベース接続を取得します
     */
    public static Connection getConnection() throws SQLException {
        String url = properties.getProperty("db.url");
        String user = properties.getProperty("db.user");
        String password = properties.getProperty("db.password");
        
        try {
            Connection connection = DriverManager.getConnection(url, user, password);
            return connection;
        } catch (SQLException e) {
            System.err.println("データベース接続に失敗しました: " + e.getMessage());
            throw e;
        }
    }
    
    /**
     * SQLエラーをログに出力します
     */
    public static void logSQLError(String operation, SQLException e) {
        System.err.println("=== SQLエラー ===");
        System.err.println("操作: " + operation);
        System.err.println("エラーコード: " + e.getErrorCode());
        System.err.println("SQLState: " + e.getSQLState());
        System.err.println("メッセージ: " + e.getMessage());
        System.err.println("================");
    }
    
    /**
     * 接続テスト用メソッド
     */
    public static boolean testConnection() {
        try (Connection connection = getConnection()) {
            return connection != null && !connection.isClosed();
        } catch (SQLException e) {
            logSQLError("接続テスト", e);
            return false;
        }
    }
}
```

> 📝 **ポイント**: `logSQLError`メソッドにより、SQLエラーの詳細情報を統一的にログ出力できます。エラーコードやSQLStateは問題の特定に重要な情報です。

### 5.3 PostListServletのエラーハンドリング改善

PostListServletのエラーハンドリングを改善します。

#### エラー処理の統一化

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    try {
        List<Post> posts = getAllPosts();
        
        request.setAttribute("posts", posts);
        request.setAttribute("totalPosts", posts.size());
                
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        
    } catch (SQLException e) {
        // 統一されたエラーログ出力
        DatabaseUtil.logSQLError("投稿一覧取得", e);
        
        // ユーザー向けのエラー処理
        request.setAttribute("posts", new ArrayList<Post>());
        request.setAttribute("totalPosts", 0);
        request.setAttribute("errorMessage", "投稿データの取得中に問題が発生しました。しばらく経ってから再度お試しください。");
        
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```

#### データベース操作メソッドの改善

```java
private List<Post> getAllPosts() throws SQLException {
    List<Post> posts = new ArrayList<>();
    
    String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql);
         ResultSet rs = pstmt.executeQuery()) {
        
        System.out.println("投稿一覧を取得中...");
        
        while (rs.next()) {
            int id = rs.getInt("id");
            String title = rs.getString("title");
            String content = rs.getString("content");
            LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
            
            Post post = new Post(id, title, content, createdAt);
            posts.add(post);
        }
        
        System.out.println("投稿を " + posts.size() + " 件取得しました");
        
    } catch (SQLException e) {
        System.err.println("投稿一覧取得でエラーが発生しました");
        throw e; // 上位にエラーを伝播
    }
    
    return posts;
}
```

> 📝 **ポイント**: メソッド内でのログ出力により、どの処理でエラーが発生したかを特定しやすくなります。SQLExceptionは上位のメソッドで統一的に処理します。

### 5.4 PostDetailServletのエラーハンドリング改善

PostDetailServletのエラーハンドリングも同様に改善します。

#### doGetメソッドの改善

```java
try {
    Post targetPost = getPostById(postId);
    
    if (targetPost == null) {
        System.out.println("投稿ID " + postId + " は見つかりませんでした");
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    System.out.println("投稿詳細を表示: " + targetPost.getTitle());
    request.setAttribute("post", targetPost);
    request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
    
} catch (SQLException e) {
    DatabaseUtil.logSQLError("投稿詳細取得 (ID: " + postId + ")", e);
    response.sendRedirect(request.getContextPath() + "/posts");
}
```

#### データベース操作メソッドの改善

```java
private Post getPostById(int id) throws SQLException {
    String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setInt(1, id);
        System.out.println("投稿ID " + id + " を検索中...");
        
        try (ResultSet rs = pstmt.executeQuery()) {
            if (rs.next()) {
                int postId = rs.getInt("id");
                String title = rs.getString("title");
                String content = rs.getString("content");
                LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
                
                System.out.println("投稿が見つかりました: " + title);
                return new Post(postId, title, content, createdAt);
            }
        }
        
    } catch (SQLException e) {
        System.err.println("投稿ID " + id + " の取得でエラーが発生しました");
        throw e;
    }
    
    return null;
}
```

### 5.5 PostCreateServletのエラーハンドリング改善

PostCreateServletのエラーハンドリングも改善します。

#### doPostメソッドの改善

```java
try {
    // データベースに投稿を保存
    savePost(title.trim(), content.trim());
    
    System.out.println("新規投稿が保存されました: " + title.trim());
    response.sendRedirect(request.getContextPath() + "/posts");
    
} catch (SQLException e) {
    DatabaseUtil.logSQLError("投稿保存", e);
    
    // ユーザー向けのエラー処理
    request.setAttribute("title", title);
    request.setAttribute("content", content);
    request.setAttribute("errorMessage", "投稿の保存中に問題が発生しました。入力内容を確認して再度お試しください。");
    
    request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
}
```

#### データベース操作メソッドの改善

```java
private void savePost(String title, String content) throws SQLException {
    String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setString(1, title);
        pstmt.setString(2, content);
        
        System.out.println("投稿を保存中: " + title);
        
        int rowsAffected = pstmt.executeUpdate();
        
        if (rowsAffected > 0) {
            System.out.println("投稿が正常に保存されました (" + rowsAffected + " 行)");
        } else {
            System.err.println("投稿の保存に失敗しました (影響行数: " + rowsAffected + ")");
        }
        
    } catch (SQLException e) {
        System.err.println("投稿保存でエラーが発生しました: " + title);
        throw e;
    }
}
```

> 📝 **ポイント**: `executeUpdate()`の戻り値で影響を受けた行数を確認することで、操作が成功したかどうかを判定できます。

### 5.6 接続プールの概念と将来の拡張

現在の実装では都度接続を作成していますが、本格的なアプリケーションでは接続プールを使用します。

#### 現在の方式と接続プールの違い

```java
// 現在の方式：都度接続作成
try (Connection connection = DatabaseUtil.getConnection()) {
    // データベース操作
}

// 接続プール方式（将来の実装例）
// try (Connection connection = ConnectionPool.getConnection()) {
//     // データベース操作
// }
```

#### database.propertiesの拡張

将来の接続プール対応のため、設定ファイルを拡張しておきます。

```properties
# データベース接続設定
db.url=jdbc:mysql://localhost:3306/blog_app
db.user=root
db.password=your_password
db.driver=com.mysql.cj.jdbc.Driver

# 接続プール設定（将来の拡張用）
db.pool.maxConnections=20
db.pool.minConnections=5
db.pool.timeout=30000

# ログレベル設定
db.log.level=INFO
```

> 📝 **ポイント**: 接続プールはパフォーマンス向上のための技術ですが、モジュール4では基本的な接続管理に集中します。接続プールはより高度なトピックとして将来学習します。

### 5.7 動作確認とデバッグ

改善されたエラーハンドリングの動作を確認します。

#### 正常動作の確認

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. 各機能を動作させてコンソールログを確認

#### エラーケースのテスト

意図的にエラーを発生させて、ログ出力を確認します。

1. **データベースを停止してアクセス**
   * MySQL サーバーを停止
   * アプリケーションにアクセス
   * コンソールに詳細なエラーログが出力されることを確認
2. **不正なIDでアクセス**
   * `http://localhost:8080/blog-app/post?id=999`
   * ログに適切なメッセージが出力されることを確認

#### 期待されるログ出力例

```
データベース設定が正常に読み込まれました
投稿一覧を取得中...
投稿を 3 件取得しました
投稿ID 1 を検索中...
投稿が見つかりました: データベース連携の開始
新規投稿が保存されました: テスト投稿
投稿が正常に保存されました (1 行)
```

### 5.8 まとめと次のステップ

このセクションで以下の改善を行いました：

#### 実装した改善点

1. **統一されたエラーログ出力**: DatabaseUtil.logSQLError()による詳細なエラー情報
2. **操作ログの追加**: 各データベース操作の開始と完了をログ出力
3. **ユーザーフレンドリーなエラーメッセージ**: 技術的詳細を隠した分かりやすいメッセージ
4. **デバッグ情報の充実**: 問題の特定を容易にする情報の出力

#### 得られたメリット

* **運用時の問題解決の効率化**: 詳細なログによる問題の早期特定
* **ユーザー体験の向上**: 分かりやすいエラーメッセージ
* **開発効率の向上**: デバッグ情報による開発時の問題解決
* **保守性の向上**: 統一されたエラー処理パターン

> 📝 **ポイント**: エラーハンドリングとログ出力は、開発時だけでなく運用時にも重要です。適切なログ出力により、問題の早期発見と迅速な解決が可能になります。

次のセクションでは、これまでの実装を総合的にテストし、モジュール5（DAOパターンの導入）への準備を行います。
