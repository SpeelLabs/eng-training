# 1. DAOパターンの導入

### 1.1 現在のコードの課題分析

モジュール4で実装したアプリケーションは正常に動作していますが、保守性と拡張性の観点で改善すべき点があります。現在のコードを分析して、どのような問題があるかを確認していきます。

#### 重複するデータベース操作コード

現在の各サーブレットには、似たようなデータベース操作コードが散らばっています。

```java
// PostListServlet内
try (Connection connection = DatabaseUtil.getConnection();
     PreparedStatement pstmt = connection.prepareStatement(sql);
     ResultSet rs = pstmt.executeQuery()) {
    // データ取得処理
}

// PostDetailServlet内  
try (Connection connection = DatabaseUtil.getConnection();
     PreparedStatement pstmt = connection.prepareStatement(sql)) {
    // データ取得処理
}

// PostCreateServlet内
try (Connection connection = DatabaseUtil.getConnection();
     PreparedStatement pstmt = connection.prepareStatement(sql)) {
    // データ保存処理
}
```

この構造には以下の問題があります：

* **コードの重複**: 似たような接続・クエリ実行コードが複数箇所に存在
* **保守性の低下**: データベースアクセス方法を変更する際、複数箇所を修正が必要
* **テストの困難性**: サーブレット全体をテストしないとデータベース操作をテストできない

> 📝 **ポイント**: DAOパターンは、これらの問題を解決するための設計パターンです。Data Access Object（データアクセスオブジェクト）により、データアクセスロジックを一箇所に集約します。

### 1.2 DAOパターンの概念

DAOパターンとは、データアクセスの処理を専用のクラスに分離する設計パターンです。

#### DAOパターンの構成要素

```java
// 改善後のアーキテクチャ
[Servlet] → [DAO] → [Database]

// 具体例
PostListServlet → PostDAO.getAllPosts() → Database
PostDetailServlet → PostDAO.getById() → Database  
PostCreateServlet → PostDAO.save() → Database
```

#### DAOパターンの利点

1. **関心の分離**: ビジネスロジックとデータアクセスロジックの分離
2. **再利用性**: 複数のサーブレットから同じDAOメソッドを利用可能
3. **テスト容易性**: DAOクラス単体でのテストが可能
4. **保守性**: データアクセス方法の変更時、DAOクラスのみ修正すれば済む

### 1.3 BaseDAOクラスの作成

まず、共通のデータベース操作機能を提供するBaseDAOクラスを作成します。

#### パッケージ構成の準備

`src/main/java/com/example/blog/dao` パッケージを作成し、その中に `BaseDAO.java` ファイルを作成します。

#### BaseDAOクラスの基本構造

```java
package com.example.blog.dao;

import java.sql.Connection;
import java.sql.SQLException;
import com.example.blog.util.DatabaseUtil;

/**
 * 全てのDAOクラスの基底クラス
 * 共通のデータベース操作機能を提供する
 */
public abstract class BaseDAO {
    
    /**
     * データベース接続を取得する
     * 
     * @return データベース接続
     * @throws SQLException データベース接続エラー
     */
    protected Connection getConnection() throws SQLException {
        return DatabaseUtil.getConnection();
    }
    
    /**
     * SQLエラーをログに出力する
     * 
     * @param operation 操作名
     * @param e SQLException
     */
    protected void logSQLError(String operation, SQLException e) {
        DatabaseUtil.logSQLError(operation, e);
    }
}
```

> 📝 **ポイント**: `abstract`クラスとすることで、BaseDAOを直接インスタンス化することを防ぎ、継承して使用することを明確にしています。`protected`メソッドにより、サブクラスからのみアクセス可能にしています。

### 1.4 PostDAOクラスの作成

次に、投稿データの操作に特化したPostDAOクラスを作成します。

#### PostDAOクラスの基本構造

同じdaoパッケージ内に `PostDAO.java` ファイルを作成します。

```java
package com.example.blog.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

/**
 * 投稿データのデータアクセスオブジェクト
 */
public class PostDAO extends BaseDAO {
    
    // ここに投稿データの操作メソッドを実装していきます
}
```

### 1.5 getAllPostsメソッドの実装

PostListServletで使用している投稿一覧取得機能をDAOに移行します。

#### メソッドの実装

```java
/**
 * 全ての投稿を新しい順で取得する
 * 
 * @return 投稿のリスト
 * @throws SQLException データベースエラー
 */
public List<Post> getAllPosts() throws SQLException {
    List<Post> posts = new ArrayList<>();
    
    String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
    
    try (Connection connection = getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql);
         ResultSet rs = pstmt.executeQuery()) {
        
        while (rs.next()) {
            Post post = createPostFromResultSet(rs);
            posts.add(post);
        }
        
    } catch (SQLException e) {
        logSQLError("投稿一覧取得", e);
        throw e;
    }
    
    return posts;
}
```

#### ResultSetからPostオブジェクト作成の共通化

PostオブジェクトをResultSetから作成する処理を共通メソッド化します。

```java
/**
 * ResultSetからPostオブジェクトを作成する
 * 
 * @param rs ResultSet
 * @return Postオブジェクト
 * @throws SQLException データベースエラー
 */
private Post createPostFromResultSet(ResultSet rs) throws SQLException {
    int id = rs.getInt("id");
    String title = rs.getString("title");
    String content = rs.getString("content");
    LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
    
    return new Post(id, title, content, createdAt);
}
```

> 📝 **ポイント**: 共通メソッド`createPostFromResultSet()`により、Postオブジェクトの作成ロジックが一箇所に集約されます。カラム名の変更やデータ変換ロジックの変更時に、修正箇所を最小限に抑えられます。

### 1.6 getByIdメソッドの実装

PostDetailServletで使用している投稿取得機能をDAOに移行します。

```java
/**
 * 指定されたIDの投稿を取得する
 * 
 * @param id 投稿ID
 * @return 投稿データ、見つからない場合はnull
 * @throws SQLException データベースエラー
 */
public Post getById(int id) throws SQLException {
    String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
    
    try (Connection connection = getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setInt(1, id);
        
        try (ResultSet rs = pstmt.executeQuery()) {
            if (rs.next()) {
                return createPostFromResultSet(rs);
            }
        }
        
    } catch (SQLException e) {
        logSQLError("投稿取得 (ID: " + id + ")", e);
        throw e;
    }
    
    return null;
}
```

### 1.7 saveメソッドの実装

PostCreateServletで使用している投稿保存機能をDAOに移行します。

```java
/**
 * 投稿を保存する
 * 
 * @param title 投稿タイトル
 * @param content 投稿内容
 * @throws SQLException データベースエラー
 */
public void save(String title, String content) throws SQLException {
    String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
    
    try (Connection connection = getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setString(1, title);
        pstmt.setString(2, content);
        
        int rowsAffected = pstmt.executeUpdate();
        
        if (rowsAffected == 0) {
            throw new SQLException("投稿の保存に失敗しました");
        }
        
    } catch (SQLException e) {
        logSQLError("投稿保存", e);
        throw e;
    }
}
```

> 📝 **ポイント**: `executeUpdate()`の戻り値で影響行数をチェックし、0の場合は例外を投げることで、保存失敗を確実に検出できます。

### 1.8 PostListServletのDAO対応

既存のPostListServletを、PostDAOを使用するように修正します。

#### インスタンス変数の追加

```java
public class PostListServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
    // 既存のdoGetメソッドを修正...
}
```

#### doGetメソッドの簡素化

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    try {
        // DAOを使用して投稿を取得
        List<Post> posts = postDAO.getAllPosts();
        
        request.setAttribute("posts", posts);
        request.setAttribute("totalPosts", posts.size());
                
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        
    } catch (SQLException e) {
        // エラー処理
        request.setAttribute("posts", new ArrayList<Post>());
        request.setAttribute("totalPosts", 0);
        request.setAttribute("errorMessage", "投稿データの取得中に問題が発生しました。");
        
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```

#### 不要なメソッドの削除

PostListServlet内の `getAllPosts()` メソッドは不要になったので削除します。

### 1.9 動作確認

DAOパターンを導入したPostListServletの動作を確認します。

#### ビルドとテスト

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. 投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`

#### 期待される結果

* 投稿一覧が正常に表示される
* DAOパターン導入前と同じ動作をする
* エラーが発生しない

#### コードの改善点確認

DAOパターン導入により、以下の改善が実現されました：

1. **PostListServlet の簡素化**: データベース操作コードがなくなり、ビジネスロジックに集中
2. **再利用可能なDAO**: PostDAOは他のサーブレットからも利用可能
3. **テスト容易性**: PostDAOクラス単体でのテスト実装が可能
4. **保守性向上**: データアクセスロジックがPostDAOに集約

> 📝 **ポイント**: 動作確認により、リファクタリングが成功したことを確認できます。外部から見た動作は変わらないが、内部の構造が改善されています。

### 1.10 完成したPostDAOクラス

現在のセクションで実装したPostDAOクラスの完全なコードは以下のようになります。

```java
package com.example.blog.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

public class PostDAO extends BaseDAO {
    
    public List<Post> getAllPosts() throws SQLException {
        List<Post> posts = new ArrayList<>();
        
        String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
        
        try (Connection connection = getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql);
             ResultSet rs = pstmt.executeQuery()) {
            
            while (rs.next()) {
                Post post = createPostFromResultSet(rs);
                posts.add(post);
            }
            
        } catch (SQLException e) {
            logSQLError("投稿一覧取得", e);
            throw e;
        }
        
        return posts;
    }
    
    public Post getById(int id) throws SQLException {
        String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
        
        try (Connection connection = getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setInt(1, id);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    return createPostFromResultSet(rs);
                }
            }
            
        } catch (SQLException e) {
            logSQLError("投稿取得 (ID: " + id + ")", e);
            throw e;
        }
        
        return null;
    }
    
    public void save(String title, String content) throws SQLException {
        String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
        
        try (Connection connection = getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setString(1, title);
            pstmt.setString(2, content);
            
            int rowsAffected = pstmt.executeUpdate();
            
            if (rowsAffected == 0) {
                throw new SQLException("投稿の保存に失敗しました");
            }
            
        } catch (SQLException e) {
            logSQLError("投稿保存", e);
            throw e;
        }
    }
    
    private Post createPostFromResultSet(ResultSet rs) throws SQLException {
        int id = rs.getInt("id");
        String title = rs.getString("title");
        String content = rs.getString("content");
        LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
        
        return new Post(id, title, content, createdAt);
    }
}
```

これでDAOパターンの基礎が完成しました。次のセクションでは、残りのサーブレットもDAO対応に変更し、投稿の編集・削除機能を追加していきます。
