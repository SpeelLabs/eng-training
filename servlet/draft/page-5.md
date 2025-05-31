# Page 5

## 5. 投稿データの作成機能実装

#### 5.1 既存のPostCreateServletの修正

パート 1 で作成した PostCreateServlet を修正して、アプリケーションスコープではなくデータベースに投稿データを保存するようにします。この修正により、作成した投稿がデータベースに永続化されるようになります。

**現在のPostCreateServletの分析**

まず、パート 1 で作成した PostCreateServlet の構造を確認します。

```java
// パート1の実装（修正前）
@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title, content);
        
        // アプリケーションスコープから投稿リストと次の ID を取得
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
        Integer nextId = (Integer) getServletContext().getAttribute("nextId");
        
        if (nextId == null) {
            nextId = 1;
        }
        
        newPost.setId(nextId);
        posts.add(newPost);
        getServletContext().setAttribute("nextId", nextId + 1);
        
        response.sendRedirect(request.getContextPath() + "/posts");
    }
}
```

#### 5.2 データベースへの投稿保存機能

**必要なインポートの追加**

データベースアクセスに必要なクラスをインポートします。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
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

**投稿のバリデーション機能**

投稿データの基本的なバリデーションを行う機能を追加します。

```java
/**
 * 投稿データのバリデーションを行う
 * 
 * @param title タイトル
 * @param content 内容
 * @return エラーメッセージのリスト
 */
private List<String> validatePost(String title, String content) {
    List<String> errors = new ArrayList<>();
    
    // タイトルのバリデーション
    if (title == null || title.trim().isEmpty()) {
        errors.add("タイトルは必須です。");
    } else if (title.length() > 200) {
        errors.add("タイトルは200文字以内で入力してください。");
    }
    
    // 内容のバリデーション
    if (content == null || content.trim().isEmpty()) {
        errors.add("内容は必須です。");
    } else if (content.length() > 5000) {
        errors.add("内容は5000文字以内で入力してください。");
    }
    
    return errors;
}
```

**データベースに投稿を保存するメソッド**

投稿をデータベースに保存するメソッドを実装します。

```java
/**
 * 投稿をデータベースに保存する
 * 
 * @param post 保存する投稿
 * @throws SQLException データベースエラー
 */
private void savePostToDatabase(Post post) throws SQLException {
    String sql = "INSERT INTO posts (title, content, created_at) VALUES (?, ?, ?)";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement statement = connection.prepareStatement(sql)) {
        
        // パラメータを設定
        statement.setString(1, post.getTitle());
        statement.setString(2, post.getContent());
        statement.setTimestamp(3, Timestamp.valueOf(post.getCreatedAt()));
        
        // SQL を実行
        int rowsAffected = statement.executeUpdate();
        
        if (rowsAffected == 0) {
            throw new SQLException("投稿の保存に失敗しました。影響を受けた行数: 0");
        }
        
        System.out.println("投稿が正常に保存されました。タイトル: " + post.getTitle());
    }
}
```

> 📝 **ポイント**:
>
> * INSERT 文で新しい投稿をデータベースに追加します
> * id カラムは AUTO\_INCREMENT なので指定しません
> * LocalDateTime を Timestamp に変換してデータベースに保存します
> * executeUpdate() の戻り値で保存結果を確認します

**修正されたdoPostメソッド**

データベース保存を使用するように doPost メソッドを修正します。

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // リクエストパラメーターの文字コードを設定
    request.setCharacterEncoding("UTF-8");
    
    // フォームから送信されたデータを取得
    String title = request.getParameter("title");
    String content = request.getParameter("content");
    
    // 入力値のバリデーション
    List<String> errors = validatePost(title, content);
    
    if (!errors.isEmpty()) {
        // バリデーションエラーがある場合、フォームに戻る
        request.setAttribute("errors", errors);
        request.setAttribute("title", title);
        request.setAttribute("content", content);
        request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
        return;
    }
    
    try {
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title.trim(), content.trim());
        
        // データベースに保存
        savePostToDatabase(newPost);
        
        // 成功メッセージをセッションに設定
        request.getSession().setAttribute("message", "投稿が正常に作成されました。");
        
        // 投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
        
    } catch (SQLException e) {
        // データベースエラーの処理
        System.err.println("投稿保存エラー: " + e.getMessage());
        e.printStackTrace();
        
        // エラーメッセージを設定してフォームに戻る
        request.setAttribute("error", "投稿の保存に失敗しました。しばらく後でお試しください。");
        request.setAttribute("title", title);
        request.setAttribute("content", content);
        request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
    }
}
```

#### 5.3 完成したPostCreateServlet

修正した PostCreateServlet の完全なコードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
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

@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // リクエストパラメーターの文字コードを設定
        request.setCharacterEncoding("UTF-8");
        
        // フォームから送信されたデータを取得
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 入力値のバリデーション
        List<String> errors = validatePost(title, content);
        
        if (!errors.isEmpty()) {
            // バリデーションエラーがある場合、フォームに戻る
            request.setAttribute("errors", errors);
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
            return;
        }
        
        try {
            // 新しい投稿オブジェクトを作成
            Post newPost = new Post(title.trim(), content.trim());
            
            // データベースに保存
            savePostToDatabase(newPost);
            
            // 成功メッセージをセッションに設定
            request.getSession().setAttribute("message", "投稿が正常に作成されました。");
            
            // 投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
            
        } catch (SQLException e) {
            // データベースエラーの処理
            System.err.println("投稿保存エラー: " + e.getMessage());
            e.printStackTrace();
            
            // エラーメッセージを設定してフォームに戻る
            request.setAttribute("error", "投稿の保存に失敗しました。しばらく後でお試しください。");
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
        }
    }
    
    /**
     * 投稿データのバリデーションを行う
     * 
     * @param title タイトル
     * @param content 内容
     * @return エラーメッセージのリスト
     */
    private List<String> validatePost(String title, String content) {
        List<String> errors = new ArrayList<>();
        
        // タイトルのバリデーション
        if (title == null || title.trim().isEmpty()) {
            errors.add("タイトルは必須です。");
        } else if (title.length() > 200) {
            errors.add("タイトルは200文字以内で入力してください。");
        }
        
        // 内容のバリデーション
        if (content == null || content.trim().isEmpty()) {
            errors.add("内容は必須です。");
        } else if (content.length() > 5000) {
            errors.add("内容は5000文字以内で入力してください。");
        }
        
        return errors;
    }
    
    /**
     * 投稿をデータベースに保存する
     * 
     * @param post 保存する投稿
     * @throws SQLException データベースエラー
     */
    private void savePostToDatabase(Post post) throws SQLException {
        String sql = "INSERT INTO posts (title, content, created_at) VALUES (?, ?, ?)";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement statement = connection.prepareStatement(sql)) {
            
            // パラメータを設定
            statement.setString(1, post.getTitle());
            statement.setString(2, post.getContent());
            statement.setTimestamp(3, Timestamp.valueOf(post.getCreatedAt()));
            
            // SQL を実行
            int rowsAffected = statement.executeUpdate();
            
            if (rowsAffected == 0) {
                throw new SQLException("投稿の保存に失敗しました。影響を受けた行数: 0");
            }
            
            System.out.println("投稿が正常に保存されました。タイトル: " + post.getTitle());
        }
    }
}
```

#### 5.4 投稿フォームの改善

エラーメッセージと成功メッセージを表示するように投稿フォームを改善します。

**postForm.jsp の修正**

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="新規投稿の作成" />
</jsp:include>

<h1>新規投稿の作成</h1>

<!-- エラーメッセージの表示 -->
<%
List<String> errors = (List<String>) request.getAttribute("errors");
if (errors != null && !errors.isEmpty()) {
%>
    <div style="background-color: #f8d7da; color: #721c24; padding: 15px; border: 1px solid #f5c6cb; border-radius: 4px; margin: 20px 0;">
        <strong>入力エラー:</strong>
        <ul style="margin: 10px 0 0 20px;">
            <% for (String error : errors) { %>
                <li><%= error %></li>
            <% } %>
        </ul>
    </div>
<% } %>

<!-- 一般的なエラーメッセージの表示 -->
<%
String error = (String) request.getAttribute("error");
if (error != null) {
%>
    <div style="background-color: #f8d7da; color: #721c24; padding: 15px; border: 1px solid #f5c6cb; border-radius: 4px; margin: 20px 0;">
        <strong>エラー:</strong> <%= error %>
    </div>
<% } %>

<form action="<%= request.getContextPath() %>/createPost" method="post">
    <div>
        <label for="title">タイトル:</label>
        <input type="text" 
               id="title" 
               name="title" 
               maxlength="200" 
               value="<%= request.getAttribute("title") != null ? request.getAttribute("title") : "" %>"
               required>
        <small style="color: #666; font-size: 0.9em;">最大200文字まで入力できます</small>
    </div>
    <div>
        <label for="content">内容:</label>
        <textarea id="content" 
                  name="content" 
                  rows="10" 
                  maxlength="5000"
                  required 
                  placeholder="投稿の内容を入力してください..."><%= request.getAttribute("content") != null ? request.getAttribute("content") : "" %></textarea>
        <small style="color: #666; font-size: 0.9em;">最大5000文字まで入力できます</small>
    </div>
    <div style="margin-top: 20px;">
        <button type="submit" class="button">投稿</button>
        <a href="<%= request.getContextPath() %>/posts" class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
    </div>
</form>

<jsp:include page="/jsp/common/footer.jsp" />
```

#### 5.5 成功メッセージの表示

投稿一覧ページで成功メッセージを表示するように修正します。

**postList.jsp に成功メッセージ表示を追加**

```html
<!-- postList.jsp の先頭部分に追加 -->
<%
String message = (String) session.getAttribute("message");
if (message != null) {
    session.removeAttribute("message"); // 一度表示したら削除
%>
    <div style="background-color: #d4edda; color: #155724; padding: 15px; border: 1px solid #c3e6cb; border-radius: 4px; margin: 20px 0;">
        <strong>成功:</strong> <%= message %>
    </div>
<% } %>
```

#### 5.6 AppInitializerの削除または修正

データベースを使用するようになったため、パート1で作成した AppInitializer の投稿データ初期化部分は不要になります。

**AppInitializer.java の修正**

```java
package com.example.blog.listener;

import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

@WebListener
public class AppInitializer implements ServletContextListener {
    
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ブログアプリケーションが開始されました");
        // データベースを使用するため、投稿データの初期化は不要
    }
    
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ブログアプリケーションが終了しました");
    }
}
```

#### 5.7 動作確認

**アプリケーションのビルドとデプロイ**

1. プロジェクトをビルド：`mvn clean package`
2. Tomcat サーバーを再起動
3. VS Code でコンソール出力を確認

**新規投稿機能の確認**

ブラウザで以下の手順を実行して、データベースへの保存が正常に動作することを確認します。

1.  **投稿フォームにアクセス**：

    ```
    http://localhost:8080/blog-app/jsp/postForm.jsp
    ```
2. **正常な投稿の作成**：
   * タイトルと内容を入力
   * 「投稿」ボタンをクリック
   * 投稿一覧ページにリダイレクトされ、成功メッセージが表示される
   * 新しい投稿がリストの最上部に表示される
3. **バリデーションエラーの確認**：
   * 空のタイトルで投稿を試行
   * エラーメッセージが表示され、入力値が保持される
4.  **データベースでの確認**：

    ```sql
    SELECT * FROM posts ORDER BY created_at DESC LIMIT 5;
    ```

**投稿詳細ページの確認**

新しく作成した投稿の詳細ページも確認します。

```
http://localhost:8080/blog-app/post?id=[新しい投稿のID]
```

#### 5.8 トラブルシューティング

**よくあるエラーと対処法**

**エラー1: 文字化け**

```
データベースに日本語が正しく保存されない
```

**対処法**:

*   database.properties の URL に文字エンコーディングを追加：

    ```
    db.url=jdbc:mysql://localhost:3306/blog_app?useUnicode=true&characterEncoding=UTF-8
    ```

**エラー2: タイムゾーン警告**

```
The server time zone value 'JST' is unrecognized
```

**対処法**:

*   URL にタイムゾーン設定を追加：

    ```
    db.url=jdbc:mysql://localhost:3306/blog_app?serverTimezone=Asia/Tokyo
    ```

**エラー3: バリデーションが動作しない**

```
空の値でも投稿が作成される
```

**対処法**:

* HTML フォームの required 属性は参考程度
* サーバー側のバリデーションが重要
* JavaScript が無効でも動作するようにサーバー側で必ず検証

#### 5.9 学習のポイント

**PreparedStatement の使用**

```java
// SQL インジェクション対策
String sql = "INSERT INTO posts (title, content, created_at) VALUES (?, ?, ?)";
statement.setString(1, post.getTitle());
statement.setString(2, post.getContent());
statement.setTimestamp(3, Timestamp.valueOf(post.getCreatedAt()));
```

**エラーハンドリングのパターン**

* **バリデーションエラー**: フォームに戻る（forward）
* **データベースエラー**: エラーメッセージを表示してフォームに戻る
* **成功**: 一覧ページにリダイレクト

**リダイレクトとフォワードの使い分け**

* **POST 後のリダイレクト**: ブラウザの再読み込みで重複投稿を防ぐ
* **エラー時のフォワード**: 入力値を保持したままフォーム再表示

> 📝 **ポイント**: このセクションでは、投稿作成機能をデータベース対応に修正しました。PreparedStatement による安全な SQL 実行、適切なバリデーション、エラーハンドリングなど、データベースアプリケーション開発の重要な要素を学習しました。次のセクションでは、アプリケーション全体の動作確認とテストを行います。
