# 3. 投稿詳細表示のデータベース対応

### 3.1 現在の実装の分析

パート1で作成したPostDetailServletは、アプリケーションスコープから投稿データを検索していました。これをデータベースから取得するように段階的に変更していきます。

#### 既存コードの確認

現在のPostDetailServletの重要な部分を確認してみましょう。

```java
// アプリケーションスコープから投稿リストを取得
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");

// 取得した ID の投稿を検索
Post targetPost = null;
for (Post post : posts) {
    if (post.getId() == postId) {
        targetPost = post;
        break;
    }
}
```

この部分を、データベースから直接投稿を取得するように変更します。

> 📝 **ポイント**: リスト全体を取得してから検索するのではなく、データベースで直接ID指定で検索することで効率的になります。

### 3.2 必要なimport文の追加

データベース操作に必要なimport文をPostDetailServletに追加します。

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import com.example.blog.util.DatabaseUtil;
```

既存のimport文に加えて、上記を追加してください。

### 3.3 ID指定の投稿取得メソッドの実装

PostDetailServletクラス内に、指定されたIDの投稿をデータベースから取得するメソッドを段階的に実装します。

#### メソッドの基本構造

まず、メソッドの枠組みを作成します。

```java
/**
 * 指定されたIDの投稿を取得する
 * 
 * @param id 投稿ID
 * @return 投稿データ、見つからない場合はnull
 */
private Post getPostById(int id) throws SQLException {
    // ここにデータベース操作を実装
    return null;
}
```

#### SQL文の作成と実行

次に、実際のデータベース操作を実装します。

```java
private Post getPostById(int id) throws SQLException {
    String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        // パラメータを設定
        pstmt.setInt(1, id);
        
        // ここでResultSetからデータを取得
        
    }
    
    return null;
}
```

> 📝 **ポイント**: `WHERE id = ?` により、指定されたIDの投稿のみを取得します。PreparedStatementのパラメータ設定により、SQLインジェクション攻撃を防げます。

#### ResultSetからPostオブジェクトの作成

ResultSetからデータを取得してPostオブジェクトを作成する処理を追加します。

```java
try (ResultSet rs = pstmt.executeQuery()) {
    if (rs.next()) {
        // データが見つかった場合
        int postId = rs.getInt("id");
        String title = rs.getString("title");
        String content = rs.getString("content");
        LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
        
        return new Post(postId, title, content, createdAt);
    }
}

// データが見つからない場合
return null;
```

> 📝 **ポイント**: `if (rs.next())` により、データが存在するかチェックします。IDで検索する場合は最大1件なので、`while`ではなく`if`を使用します。

### 3.4 doGetメソッドの修正

既存のdoGetメソッドを、新しいデータベース取得メソッドを使用するように修正します。

#### アプリケーションスコープ部分の削除

まず、アプリケーションスコープからの取得部分を削除します。

```java
// 削除：これまでの実装
// List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
// Post targetPost = null;
// for (Post post : posts) { ... }

// 新しい実装
Post targetPost = getPostById(postId);
```

#### エラーハンドリングの追加

データベース操作ではSQLExceptionが発生する可能性があるため、適切なエラーハンドリングを追加します。

```java
try {
    // データベースから投稿を取得
    Post targetPost = getPostById(postId);
    
    // 投稿が見つからない場合は投稿一覧ページにリダイレクト
    if (targetPost == null) {
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // 見つかった投稿をリクエスト属性にセット
    request.setAttribute("post", targetPost);
    
    // 詳細表示用 JSP にフォワード
    request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
    
} catch (SQLException e) {
    // データベースエラーの場合
    System.err.println("データベースエラー: " + e.getMessage());
    response.sendRedirect(request.getContextPath() + "/posts");
}
```

### 3.5 完全なdoGetメソッドの実装

パラメータチェックと新しいデータベース処理を組み合わせた完全なdoGetメソッドは以下のようになります。

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // リクエストパラメーターから ID を取得
    String idParam = request.getParameter("id");
    
    // ID が指定されていない場合は投稿一覧ページにリダイレクト
    if (idParam == null || idParam.isEmpty()) {
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // ID を整数に変換
    int postId;
    try {
        postId = Integer.parseInt(idParam);
    } catch (NumberFormatException e) {
        // ID が数値でない場合は投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // データベースから投稿を取得
    try {
        Post targetPost = getPostById(postId);
        
        // 投稿が見つからない場合は投稿一覧ページにリダイレクト
        if (targetPost == null) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        // 見つかった投稿をリクエスト属性にセット
        request.setAttribute("post", targetPost);
        
        // 詳細表示用 JSP にフォワード
        request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
        
    } catch (SQLException e) {
        System.err.println("データベースエラー: " + e.getMessage());
        response.sendRedirect(request.getContextPath() + "/posts");
    }
}
```

### 3.6 動作確認

修正したアプリケーションの動作を確認します。

#### 事前確認

データベースに投稿データがあることを確認してください。

```sql
USE blog_app;
SELECT id, title FROM posts ORDER BY created_at DESC;
```

#### アプリケーションのテスト

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. 投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`
4. いずれかの投稿の「続きを読む」リンクをクリック

#### 期待される結果

* 選択した投稿の詳細ページが表示される
* 投稿のタイトル、内容、日時が正しく表示される
* 存在しないIDでアクセスした場合は一覧ページにリダイレクトされる

#### エラーケースのテスト

以下のURLで正しくエラーハンドリングされることを確認してください。

* IDなし: `http://localhost:8080/blog-app/post`
* 不正なID: `http://localhost:8080/blog-app/post?id=abc`
* 存在しないID: `http://localhost:8080/blog-app/post?id=999`

> 📝 **ポイント**: すべてのケースで投稿一覧ページにリダイレクトされれば正常です。

### 3.7 完成したPostDetailServlet

すべての修正を適用したPostDetailServletの完全なコードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.LocalDateTime;

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
            Post targetPost = getPostById(postId);
            
            if (targetPost == null) {
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
            request.setAttribute("post", targetPost);
            request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
            
        } catch (SQLException e) {
            System.err.println("データベースエラー: " + e.getMessage());
            response.sendRedirect(request.getContextPath() + "/posts");
        }
    }
    
    private Post getPostById(int id) throws SQLException {
        String sql = "SELECT id, title, content, created_at FROM posts WHERE id = ?";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setInt(1, id);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    int postId = rs.getInt("id");
                    String title = rs.getString("title");
                    String content = rs.getString("content");
                    LocalDateTime createdAt = rs.getTimestamp("created_at").toLocalDateTime();
                    
                    return new Post(postId, title, content, createdAt);
                }
            }
        }
        
        return null;
    }
}
```

これで投稿詳細表示のデータベース対応が完了しました。次のセクションでは、新規投稿機能をデータベース対応に変更していきます。
