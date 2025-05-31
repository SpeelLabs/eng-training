# Page 4

## 4. 投稿データの読取機能実装

#### 4.1 既存のPostListServletの修正

パート 1 で作成した PostListServlet を修正して、アプリケーションスコープではなくデータベースから投稿データを取得するようにします。この修正により、永続化されたデータを表示できるようになります。

**現在のPostListServletの分析**

まず、パート 1 で作成した PostListServlet の構造を確認します。

```java
// パート1の実装（修正前）
@WebServlet("/posts")
public class PostListServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // アプリケーションスコープから投稿リストを取得
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
        
        // 投稿を作成日時の降順で並べ替え
        List<Post> sortedPosts = posts.stream()
            .sorted(Comparator.comparing(Post::getCreatedAt).reversed())
            .collect(Collectors.toList());
        
        // リクエスト属性に設定
        request.setAttribute("posts", sortedPosts);
        request.setAttribute("totalPosts", sortedPosts.size());
        
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```

**データベースアクセス機能の追加**

DatabaseUtil を使用してデータベースから投稿データを取得する機能を追加します。

#### 4.2 データベースからの投稿取得機能

**必要なインポートの追加**

まず、データベースアクセスに必要なクラスをインポートします。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;
import com.example.blog.util.DatabaseUtil;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
```

**データベースから投稿を取得するメソッド**

データベースから全ての投稿を取得するメソッドを実装します。

```java
/**
 * データベースから全ての投稿を取得する
 * 
 * @return 投稿のリスト
 * @throws SQLException データベースエラー
 */
private List<Post> getAllPostsFromDatabase() throws SQLException {
    List<Post> posts = new ArrayList<>();
    
    // SQL文：作成日時の降順で取得
    String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement statement = connection.prepareStatement(sql);
         ResultSet resultSet = statement.executeQuery()) {
        
        while (resultSet.next()) {
            // ResultSetからデータを取得
            int id = resultSet.getInt("id");
            String title = resultSet.getString("title");
            String content = resultSet.getString("content");
            Timestamp createdAtTimestamp = resultSet.getTimestamp("created_at");
            
            // TimestampをLocalDateTimeに変換
            java.time.LocalDateTime createdAt = createdAtTimestamp.toLocalDateTime();
            
            // Postオブジェクトを作成
            Post post = new Post(id, title, content, createdAt);
            posts.add(post);
        }
    }
    
    return posts;
}
```

> 📝 **ポイント**:
>
> * ORDER BY で SQL レベルでソートを行います
> * ResultSet からデータを取得する際は、カラム名で指定します
> * Timestamp を LocalDateTime に変換して Post オブジェクトを作成します
> * try-with-resources でリソースが確実にクローズされます

**修正されたdoGetメソッド**

データベースアクセスを使用するように doGet メソッドを修正します。

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    try {
        // データベースから投稿を取得
        List<Post> posts = getAllPostsFromDatabase();
        
        // リクエスト属性に設定
        request.setAttribute("posts", posts);
        request.setAttribute("totalPosts", posts.size());
        
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        
    } catch (SQLException e) {
        // データベースエラーの処理
        System.err.println("投稿取得エラー: " + e.getMessage());
        e.printStackTrace();
        
        // エラーページにリダイレクト
        request.setAttribute("error", "投稿の取得に失敗しました。しばらく後でお試しください。");
        request.getRequestDispatcher("/jsp/error.jsp").forward(request, response);
    }
}
```

#### 4.3 完成したPostListServlet

修正した PostListServlet の完全なコードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;
import com.example.blog.util.DatabaseUtil;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/posts")
public class PostListServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        try {
            // データベースから投稿を取得
            List<Post> posts = getAllPostsFromDatabase();
            
            // リクエスト属性に設定
            request.setAttribute("posts", posts);
            request.setAttribute("totalPosts", posts.size());
            
            // JSPにフォワード
            request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
            
        } catch (SQLException e) {
            // データベースエラーの処理
            System.err.println("投稿取得エラー: " + e.getMessage());
            e.printStackTrace();
            
            // エラーページにリダイレクト
            request.setAttribute("error", "投稿の取得に失敗しました。しばらく後でお試しください。");
            request.getRequestDispatcher("/jsp/error.jsp").forward(request, response);
        }
    }
    
    /**
     * データベースから全ての投稿を取得する
     * 
     * @return 投稿のリスト
     * @throws SQLException データベースエラー
     */
    private List<Post> getAllPostsFromDatabase() throws SQLException {
        List<Post> posts = new ArrayList<>();
        
        // SQL文：作成日時の降順で取得
        String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement statement = connection.prepareStatement(sql);
             ResultSet resultSet = statement.executeQuery()) {
            
            while (resultSet.next()) {
                // ResultSetからデータを取得
                int id = resultSet.getInt("id");
                String title = resultSet.getString("title");
                String content = resultSet.getString("content");
                Timestamp createdAtTimestamp = resultSet.getTimestamp("created_at");
                
                // TimestampをLocalDateTimeに変換
                java.time.LocalDateTime createdAt = createdAtTimestamp.toLocalDateTime();
                
                // Postオブジェクトを作成
                Post post = new Post(id, title, content, createdAt);
                posts.add(post);
            }
        }
        
        return posts;
    }
}
```

#### 4.4 PostDetailServletの修正

投稿詳細表示機能もデータベース対応に修正します。

**PostDetailServletの修正**

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Timestamp;

import com.example.blog.model.Post;
import com.example.blog.util.DatabaseUtil;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/post")
public class PostDetailServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // リクエストパラメーターから ID を取得
        String idParam = request.getParameter("id");
        
        if (idParam == null || idParam.isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        int postId;
        try {
            postId = Integer.parseInt(idParam);
        } catch (NumberFormatException e) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        try {
            // データベースから指定IDの投稿を取得
            Post post = getPostById(postId);
            
            if (post == null) {
                // 投稿が見つからない場合
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
            // リクエスト属性に設定
            request.setAttribute("post", post);
            
            // JSPにフォワード
            request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
            
        } catch (SQLException e) {
            System.err.println("投稿取得エラー: " + e.getMessage());
            e.printStackTrace();
            
            request.setAttribute("error", "投稿の取得に失敗しました。");
            request.getRequestDispatcher("/jsp/error.jsp").forward(request, response);
        }
    }
    
    /**
     * 指定されたIDの投稿をデータベースから取得する
     * 
     * @param id 投稿ID
     * @return 投稿オブジェクト（見つからない場合はnull）
     * @throws SQLException データベースエラー
     */
    private Post getPostById(int id) throws SQLException {
        String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement statement = connection.prepareStatement(sql)) {
            
            statement.setInt(1, id);
            
            try (ResultSet resultSet = statement.executeQuery()) {
                if (resultSet.next()) {
                    String title = resultSet.getString("title");
                    String content = resultSet.getString("content");
                    Timestamp createdAtTimestamp = resultSet.getTimestamp("created_at");
                    java.time.LocalDateTime createdAt = createdAtTimestamp.toLocalDateTime();
                    
                    return new Post(id, title, content, createdAt);
                }
            }
        }
        
        return null; // 投稿が見つからない場合
    }
}
```

#### 4.5 エラーページの作成

データベースエラー用のエラーページを作成します。

**error.jsp の作成**

`src/main/webapp/jsp/error.jsp` を作成：

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="エラー" />
</jsp:include>

<h1>エラーが発生しました</h1>

<%
String error = (String) request.getAttribute("error");
if (error != null) {
%>
    <div style="background-color: #f8d7da; color: #721c24; padding: 15px; border: 1px solid #f5c6cb; border-radius: 4px; margin: 20px 0;">
        <strong>エラー:</strong> <%= error %>
    </div>
<% } %>

<p>申し訳ございませんが、エラーが発生しました。</p>
<p>しばらく時間をおいてから再度お試しください。</p>

<div style="margin-top: 30px;">
    <a href="<%= request.getContextPath() %>/posts" class="button">投稿一覧に戻る</a>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

#### 4.6 動作確認

**アプリケーションのビルドとデプロイ**

1. プロジェクトをビルド：`mvn clean package`
2. Tomcat サーバーを再起動
3. VS Code でデバッグコンソールを確認

**データベース対応の確認**

ブラウザで以下の URL にアクセスして、データベースから投稿が取得されることを確認します。

```
http://localhost:8080/blog-app/posts
```

**確認項目**:

* セクション1で作成したサンプル投稿が表示される
* 投稿が作成日時の降順で表示される
* 投稿数が正しく表示される
* 「続きを読む」リンクが動作する

**詳細表示の確認**

投稿詳細ページも確認します。

```
http://localhost:8080/blog-app/post?id=1
```

**確認項目**:

* 指定したIDの投稿が表示される
* 不正なIDの場合は一覧ページにリダイレクトされる
* データベースエラー時はエラーページが表示される

#### 4.7 トラブルシューティング

**よくあるエラーと対処法**

**エラー1: テーブルが存在しない**

```
SQLException: Table 'blog_app.posts' doesn't exist
```

**対処法**:

* セクション1でpostsテーブルが作成されているか確認
* データベース名が正しいか確認

**エラー2: カラムが存在しない**

```
SQLException: Unknown column 'created_at' in 'field list'
```

**対処法**:

* テーブル構造を確認：`DESCRIBE posts`
* カラム名のスペルミスを確認

**エラー3: データ変換エラー**

```
SQLException: Cannot convert value '0000-00-00 00:00:00' to TIMESTAMP
```

**対処法**:

* MySQL の sql\_mode を確認
* 不正な日時データがないか確認

#### 4.8 学習のポイント

**JDBC の基本パターン**

```java
// 基本的なJDBCパターン
try (Connection connection = DatabaseUtil.getConnection();
     PreparedStatement statement = connection.prepareStatement(sql);
     ResultSet resultSet = statement.executeQuery()) {
    
    while (resultSet.next()) {
        // データ処理
    }
}
```

**ResultSet からのデータ取得**

* **getString()**: 文字列データの取得
* **getInt()**: 整数データの取得
* **getTimestamp()**: 日時データの取得
* **カラム名指定**: インデックスではなく名前で指定

**エラーハンドリングの重要性**

* **SQLException**: データベース関連エラーの処理
* **ユーザー向けメッセージ**: 技術的詳細を隠した分かりやすいメッセージ
* **ログ出力**: デバッグのための詳細なエラー情報

> 📝 **ポイント**: このセクションでは、アプリケーションスコープからデータベースへの移行を実現しました。SQL の基本的な使用方法、ResultSet の処理、エラーハンドリングなど、データベースアプリケーション開発の基礎を学習しました。次のセクションでは、投稿作成機能をデータベース対応に修正していきます。
