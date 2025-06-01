# 2. 投稿のCRUD操作完全実装

### 2.1 CRUD操作の現状確認

セクション1でDAOパターンを導入し、Create（作成）、Read（読取）の操作は実装済みです。このセクションでは、Update（更新）とDelete（削除）を追加して、CRUD操作を完全に実装します。

#### 現在実装済みの操作

```java
// Create（作成）- PostCreateServlet + PostDAO.save()
postDAO.save(title, content);

// Read（読取）- PostListServlet + PostDAO.getAllPosts()
List<Post> posts = postDAO.getAllPosts();

// Read（読取）- PostDetailServlet + PostDAO.getById()
Post post = postDAO.getById(id);
```

#### 今回実装する操作

```java
// Update（更新）- 新規実装
postDAO.update(id, title, content);

// Delete（削除）- 新規実装  
postDAO.delete(id);
```

> 📝 **ポイント**: CRUD操作の完全実装により、データの生成から削除までの全ライフサイクルを管理できるようになります。これにより、実用的なWebアプリケーションの基本機能が揃います。

### 2.2 PostDAOへのUpdate機能追加

PostDAOクラスに投稿更新機能を追加します。

#### updateメソッドの実装

```java
/**
 * 投稿を更新する
 * 
 * @param id 投稿ID
 * @param title 新しいタイトル
 * @param content 新しい内容
 * @throws SQLException データベースエラー
 */
public void update(int id, String title, String content) throws SQLException {
    String sql = "UPDATE posts SET title = ?, content = ?, updated_at = CURRENT_TIMESTAMP WHERE id = ?";
    
    try (Connection connection = getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setString(1, title);
        pstmt.setString(2, content);
        pstmt.setInt(3, id);
        
        int rowsAffected = pstmt.executeUpdate();
        
        if (rowsAffected == 0) {
            throw new SQLException("投稿ID " + id + " が見つからないため、更新できませんでした");
        }
        
    } catch (SQLException e) {
        logSQLError("投稿更新 (ID: " + id + ")", e);
        throw e;
    }
}
```

> 📝 **ポイント**: `updated_at = CURRENT_TIMESTAMP` により、更新日時を自動的に現在時刻に設定します。`rowsAffected` が0の場合、指定されたIDの投稿が存在しないことを意味します。

### 2.3 PostDAOへのDelete機能追加

PostDAOクラスに投稿削除機能を追加します。

#### deleteメソッドの実装

```java
/**
 * 投稿を削除する
 * 
 * @param id 投稿ID
 * @throws SQLException データベースエラー
 */
public void delete(int id) throws SQLException {
    String sql = "DELETE FROM posts WHERE id = ?";
    
    try (Connection connection = getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        pstmt.setInt(1, id);
        
        int rowsAffected = pstmt.executeUpdate();
        
        if (rowsAffected == 0) {
            throw new SQLException("投稿ID " + id + " が見つからないため、削除できませんでした");
        }
        
    } catch (SQLException e) {
        logSQLError("投稿削除 (ID: " + id + ")", e);
        throw e;
    }
}
```

### 2.4 残りのサーブレットのDAO対応

PostDetailServletとPostCreateServletも、PostDAOを使用するように修正します。

#### PostDetailServletの修正

```java
public class PostDetailServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
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
            // DAOを使用して投稿を取得
            Post targetPost = postDAO.getById(postId);
            
            if (targetPost == null) {
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
            request.setAttribute("post", targetPost);
            request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
            
        } catch (SQLException e) {
            response.sendRedirect(request.getContextPath() + "/posts");
        }
    }
}
```

#### PostCreateServletの修正

```java
public class PostCreateServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        if (title == null || title.trim().isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
            return;
        }
        
        if (content == null || content.trim().isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
            return;
        }
        
        try {
            // DAOを使用して投稿を保存
            postDAO.save(title.trim(), content.trim());
            
            response.sendRedirect(request.getContextPath() + "/posts");
            
        } catch (SQLException e) {
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.setAttribute("errorMessage", "投稿の保存中に問題が発生しました。");
            
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
        }
    }
}
```

### 2.5 投稿編集機能のサーブレット作成

投稿編集機能を実装するため、2つのサーブレットを作成します。

#### PostEditServlet（編集フォーム表示）の作成

`src/main/java/com/example/blog/servlet` に `PostEditServlet.java` を作成します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.SQLException;

import com.example.blog.dao.PostDAO;
import com.example.blog.model.Post;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/post/edit")
public class PostEditServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
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
            Post post = postDAO.getById(postId);
            
            if (post == null) {
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
            request.setAttribute("post", post);
            request.getRequestDispatcher("/jsp/postEdit.jsp").forward(request, response);
            
        } catch (SQLException e) {
            response.sendRedirect(request.getContextPath() + "/posts");
        }
    }
}
```

#### PostUpdateServlet（更新処理）の作成

同じく `PostUpdateServlet.java` を作成します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.SQLException;

import com.example.blog.dao.PostDAO;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/post/update")
public class PostUpdateServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        
        String idParam = request.getParameter("id");
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 基本的な入力チェック
        if (idParam == null || title == null || content == null || 
            title.trim().isEmpty() || content.trim().isEmpty()) {
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
            // 投稿を更新
            postDAO.update(postId, title.trim(), content.trim());
            
            // 詳細ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/post?id=" + postId);
            
        } catch (SQLException e) {
            // エラー時は編集ページに戻る
            response.sendRedirect(request.getContextPath() + "/post/edit?id=" + postId);
        }
    }
}
```

> 📝 **ポイント**: 更新成功後は投稿詳細ページにリダイレクトすることで、更新された内容をユーザーが確認できます。エラー時は編集ページに戻ることで、再編集を促します。

### 2.6 投稿削除機能のサーブレット作成

投稿削除機能を実装するサーブレットを作成します。

#### PostDeleteServlet（削除処理）の作成

`PostDeleteServlet.java` を作成します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.SQLException;

import com.example.blog.dao.PostDAO;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/post/delete")
public class PostDeleteServlet extends HttpServlet {
    
    private PostDAO postDAO = new PostDAO();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
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
            // 投稿を削除
            postDAO.delete(postId);
            
            // 投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
            
        } catch (SQLException e) {
            // エラー時も投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
        }
    }
}
```

> 📝 **ポイント**: 削除は元に戻せない操作なので、POSTメソッドを使用します。削除後は投稿一覧ページにリダイレクトして、削除された投稿がなくなったことを確認できます。

### 2.7 投稿編集用JSPファイルの作成

投稿編集用のJSPファイルを作成します。

#### postEdit.jspの作成

`src/main/webapp/jsp` に `postEdit.jsp` ファイルを作成します。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="com.example.blog.model.Post" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿編集" />
</jsp:include>

<%
Post post = (Post) request.getAttribute("post");
%>

<h1>投稿編集</h1>

<form action="<%= request.getContextPath() %>/post/update" method="post">
    <input type="hidden" name="id" value="<%= post.getId() %>">
    
    <div>
        <label for="title">タイトル:</label>
        <input type="text" id="title" name="title" maxlength="100" 
               value="<%= post.getTitle() %>" required>
    </div>
    
    <div>
        <label for="content">内容:</label>
        <textarea id="content" name="content" rows="10" required><%= post.getContent() %></textarea>
    </div>
    
    <div style="margin-top: 20px;">
        <button type="submit" class="button">更新</button>
        <a href="<%= request.getContextPath() %>/post?id=<%= post.getId() %>" 
           class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
    </div>
</form>

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: `hidden`フィールドで投稿IDを送信することで、更新対象の投稿を特定します。フォームには既存の投稿内容が事前に入力されており、ユーザーは必要な部分のみを変更できます。

### 2.8 投稿詳細ページに編集・削除リンクを追加

postDetail.jspに編集・削除リンクを追加します。

#### postDetail.jspの修正

投稿詳細の表示部分の後に、編集・削除リンクを追加します。

```html
<div class="post-detail">
    <h1><%= post.getTitle() %></h1>
    <p class="post-meta">
        <strong>投稿日時:</strong> <%= post.getFormattedCreatedAt() %><br>
        <strong>投稿ID:</strong> <%= post.getId() %>
    </p>
    <div class="post-content">
        <%= post.getContent().replace("\n", "<br>") %>
    </div>
</div>

<!-- 編集・削除リンクを追加 -->
<div style="margin-top: 30px; padding: 15px; background-color: #f8f9fa; border-radius: 4px;">
    <h3>管理</h3>
    <a href="<%= request.getContextPath() %>/post/edit?id=<%= post.getId() %>" 
       class="button" style="margin-right: 10px;">編集</a>
    
    <form action="<%= request.getContextPath() %>/post/delete" method="post" 
          style="display: inline;"
          onsubmit="return confirm('この投稿を削除してもよろしいですか？');">
        <input type="hidden" name="id" value="<%= post.getId() %>">
        <button type="submit" class="button" 
                style="background-color: #dc3545; border-color: #dc3545;">削除</button>
    </form>
</div>
```

> 📝 **ポイント**: 削除ボタンにはJavaScriptの`confirm()`を使用して、誤操作による削除を防ぎます。編集リンクはGETリクエスト、削除ボタンはPOSTリクエストを使用します。

### 2.9 動作確認

CRUD操作の完全実装が完了したので、全機能の動作を確認します。

#### ビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### 各機能のテスト

1. **Create（作成）**
   * 新規投稿フォームから投稿作成
   * 正常に投稿一覧に表示されることを確認
2. **Read（読取）**
   * 投稿一覧表示の確認
   * 投稿詳細表示の確認
3. **Update（更新）**
   * 投稿詳細ページの「編集」リンクをクリック
   * 投稿内容を修正して「更新」ボタンをクリック
   * 詳細ページで更新内容が反映されていることを確認
4. **Delete（削除）**
   * 投稿詳細ページの「削除」ボタンをクリック
   * 確認ダイアログで「OK」をクリック
   * 投稿一覧から削除されていることを確認

#### データベースでの確認

更新・削除操作がデータベースに正しく反映されているか確認します。

```sql
USE blog_app;

-- 更新操作の確認（updated_atが更新されているか）
SELECT id, title, created_at, updated_at FROM posts WHERE updated_at IS NOT NULL;

-- 総投稿数の確認
SELECT COUNT(*) FROM posts;
```

### 2.10 完成したPostDAOクラス

CRUD操作を完全実装したPostDAOクラスの完全なコードは以下のようになります。

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
    
    public void update(int id, String title, String content) throws SQLException {
        String sql = "UPDATE posts SET title = ?, content = ?, updated_at = CURRENT_TIMESTAMP WHERE id = ?";
        
        try (Connection connection = getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setString(1, title);
            pstmt.setString(2, content);
            pstmt.setInt(3, id);
            
            int rowsAffected = pstmt.executeUpdate();
            
            if (rowsAffected == 0) {
                throw new SQLException("投稿ID " + id + " が見つからないため、更新できませんでした");
            }
            
        } catch (SQLException e) {
            logSQLError("投稿更新 (ID: " + id + ")", e);
            throw e;
        }
    }
    
    public void delete(int id) throws SQLException {
        String sql = "DELETE FROM posts WHERE id = ?";
        
        try (Connection connection = getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setInt(1, id);
            
            int rowsAffected = pstmt.executeUpdate();
            
            if (rowsAffected == 0) {
                throw new SQLException("投稿ID " + id + " が見つからないため、削除できませんでした");
            }
            
        } catch (SQLException e) {
            logSQLError("投稿削除 (ID: " + id + ")", e);
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

これでCRUD操作の完全実装が完了しました。次のセクションでは、Serviceレイヤーの導入により、さらにアーキテクチャを改善していきます。
