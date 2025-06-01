# 2. 投稿一覧表示のデータベース対応

### 2.1 現在の実装の分析

パート1で作成したPostListServletは、アプリケーションスコープから投稿データを取得していました。これをデータベースから取得するように段階的に変更していきます。

#### 既存コードの確認

現在のPostListServletの重要な部分を確認してみましょう。

```java
// 現在の実装：アプリケーションスコープから取得
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");

// 投稿を作成日時の降順で並べ替え
List<Post> sortedPosts = posts.stream()
    .sorted(Comparator.comparing(Post::getCreatedAt).reversed())
    .collect(Collectors.toList());
```

この部分を、データベースから投稿を取得するように変更します。

> 📝 **ポイント**: データ取得方法は変わりますが、JSPでの表示方法やPostオブジェクトの構造は変更する必要がありません。これがMVCパターンの利点です。

### 2.2 必要なimport文の追加

まず、データベース操作に必要なimport文をPostListServletに追加します。

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import com.example.blog.util.DatabaseUtil;
```

既存のimport文に加えて、上記を追加してください。

### 2.3 データベース取得メソッドの実装

PostListServletクラス内に、データベースから投稿を取得するメソッドを段階的に実装します。

#### メソッドの基本構造

まず、メソッドの枠組みを作成します。

```java
/**
 * データベースから全ての投稿を取得する
 */
private List<Post> getAllPosts() throws SQLException {
    List<Post> posts = new ArrayList<>();
    
    // ここにデータベース操作を実装
    
    return posts;
}
```

#### SQL文の作成と実行

次に、実際のデータベース操作を実装します。

```java
private List<Post> getAllPosts() throws SQLException {
    List<Post> posts = new ArrayList<>();
    
    // 投稿を新しい順で取得するSQL
    String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql);
         ResultSet rs = pstmt.executeQuery()) {
        
        // ここでResultSetからデータを取得
        
    }
    
    return posts;
}
```

> 📝 **ポイント**: `ORDER BY created_at DESC` により、データベース側で並べ替えを行います。これまでのJavaでのソート処理が不要になります。

#### ResultSetからPostオブジェクトの作成

最後に、データベースの結果からPostオブジェクトを作成する処理を追加します。

```java
while (rs.next()) {
    // ResultSetからデータを取得
    int id = rs.getInt("id");
    String title = rs.getString("title");
    String content = rs.getString("content");
    LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
    
    // Postオブジェクトを作成してリストに追加
    Post post = new Post(id, title, content, createdAt);
    posts.add(post);
}
```

> 📝 **ポイント**: `rs.getTimestamp("created_at").toLocalDateTime()` により、データベースのTIMESTAMP型をLocalDateTimeに変換しています。

### 2.4 doGetメソッドの修正

既存のdoGetメソッドを、新しいデータベース取得メソッドを使用するように修正します。

#### アプリケーションスコープの削除

まず、アプリケーションスコープからの取得部分を削除します。

```java
// 削除：これまでの実装
// List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
// List<Post> sortedPosts = posts.stream()...

// 新しい実装
List<Post> posts = getPostsFromDatabase();
```

#### エラーハンドリングの追加

データベース操作ではSQLExceptionが発生する可能性があるため、適切なエラーハンドリングを追加します。

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    try {
        // データベースから投稿を取得
        List<Post> posts = getAllPosts();
        
        // リクエスト属性に設定
        request.setAttribute("posts", posts);
        request.setAttribute("totalPosts", posts.size());
        
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        
    } catch (SQLException e) {
        // エラー時の処理
        System.err.println("データベースエラー: " + e.getMessage());
        
        request.setAttribute("posts", new ArrayList<Post>());
        request.setAttribute("totalPosts", 0);
        request.setAttribute("errorMessage", "投稿の取得中にエラーが発生しました。");
        
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```

### 2.5 エラーメッセージの表示対応

JSPファイルに簡単なエラーメッセージ表示機能を追加します。

#### postList.jspの修正

h1タグの後に、以下のエラーメッセージ表示コードを追加します。

```html
<h1>投稿一覧 <span class="post-count">(全${totalPosts}件)</span></h1>

<!-- エラーメッセージの表示 -->
<%
String errorMessage = (String) request.getAttribute("errorMessage");
if (errorMessage != null) {
%>
    <div style="background-color: #f8d7da; color: #721c24; padding: 10px; margin: 10px 0; border-radius: 4px;">
        <%= errorMessage %>
    </div>
<%
}
%>
```

### 2.6 動作確認

修正したアプリケーションの動作を確認します。

#### 事前確認

ブラウザでアクセスする前に、データベースにデータがあることを確認してください。

```sql
USE blog_app;
SELECT * FROM posts ORDER BY created_at DESC;
```

#### アプリケーションのテスト

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザでアクセス： `http://localhost:8080/blog-app/posts`

#### 期待される結果

* データベースのサンプル投稿が表示される
* 新しい投稿が上に表示される
* 投稿数が正しく表示される

> 📝 **ポイント**: エラーが発生した場合は、コンソールに表示されるSQLExceptionのメッセージを確認してください。多くの場合、データベース接続やSQL文の問題が原因です。

### 2.7 完成したPostListServlet

すべての修正を適用したPostListServletの完全なコードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.LocalDateTime;
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
            List<Post> posts = getAllPosts();
            
            request.setAttribute("posts", posts);
            request.setAttribute("totalPosts", posts.size());
                    
            request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
            
        } catch (SQLException e) {
            System.err.println("データベースエラー: " + e.getMessage());
            
            request.setAttribute("posts", new ArrayList<Post>());
            request.setAttribute("totalPosts", 0);
            request.setAttribute("errorMessage", "投稿の取得中にエラーが発生しました。");
            
            request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        }
    }
    
    private List<Post> getAllPosts() throws SQLException {
        List<Post> posts = new ArrayList<>();
        
        String sql = "SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql);
             ResultSet rs = pstmt.executeQuery()) {
            
            while (rs.next()) {
                int id = rs.getInt("id");
                String title = rs.getString("title");
                String content = rs.getString("content");
                LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
                
                Post post = new Post(id, title, content, createdAt);
                posts.add(post);
            }
        }
        
        return posts;
    }
}
```

これで投稿一覧表示のデータベース対応が完了しました。次のセクションでは、投稿詳細表示機能をデータベース対応に変更していきます。
