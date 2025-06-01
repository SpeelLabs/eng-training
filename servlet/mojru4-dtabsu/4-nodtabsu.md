# 4. 新規投稿機能のデータベース対応

### 4.1 現在の実装の分析

パート1で作成したPostCreateServletは、アプリケーションスコープに投稿データを保存していました。これをデータベースに保存するように段階的に変更していきます。

#### 既存コードの確認

現在のPostCreateServletの重要な部分を確認してみましょう。

```java
// 新しい投稿オブジェクトを作成
Post newPost = new Post(title, content);

// アプリケーションスコープから投稿リストと次の ID を取得
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
Integer nextId = (Integer) getServletContext().getAttribute("nextId");

// 投稿に ID を設定
newPost.setId(nextId);

// 投稿をリストに追加
posts.add(newPost);
```

この部分を、データベースに投稿を保存するように変更します。

> 📝 **ポイント**: データベースのAUTO\_INCREMENT機能により、IDの管理が自動化されます。アプリケーションスコープでのID管理は不要になります。

### 4.2 必要なimport文の追加

データベース操作に必要なimport文をPostCreateServletに追加します。

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import com.example.blog.util.DatabaseUtil;
```

既存のimport文に加えて、上記を追加してください。

### 4.3 投稿保存メソッドの実装

PostCreateServletクラス内に、投稿をデータベースに保存するメソッドを段階的に実装します。

#### メソッドの基本構造

まず、メソッドの枠組みを作成します。

```java
/**
 * 投稿をデータベースに保存する
 * 
 * @param title 投稿タイトル
 * @param content 投稿内容
 * @throws SQLException データベースエラー
 */
private void savePost(String title, String content) throws SQLException {
    // ここにデータベース操作を実装
}
```

#### SQL文の作成と実行

次に、実際のデータベース操作を実装します。

```java
private void savePost(String title, String content) throws SQLException {
    String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
    
    try (Connection connection = DatabaseUtil.getConnection();
         PreparedStatement pstmt = connection.prepareStatement(sql)) {
        
        // パラメータを設定
        pstmt.setString(1, title);
        pstmt.setString(2, content);
        
        // SQL を実行
        pstmt.executeUpdate();
    }
}
```

> 📝 **ポイント**: `INSERT`文では`id`と`created_at`を指定していません。これらはAUTO\_INCREMENTとDEFAULT CURRENT\_TIMESTAMPにより自動的に設定されます。`executeUpdate()`はINSERT、UPDATE、DELETE文に使用します。

### 4.4 doPostメソッドの修正

既存のdoPostメソッドを、新しいデータベース保存メソッドを使用するように修正します。

#### アプリケーションスコープ部分の削除

まず、アプリケーションスコープへの保存部分を削除します。

```java
// 削除：これまでの実装
// Post newPost = new Post(title, content);
// List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
// Integer nextId = (Integer) getServletContext().getAttribute("nextId");
// newPost.setId(nextId);
// posts.add(newPost);
// getServletContext().setAttribute("nextId", nextId + 1);

// 新しい実装
savePost(title, content);
```

#### エラーハンドリングの追加

データベース操作ではSQLExceptionが発生する可能性があるため、適切なエラーハンドリングを追加します。

```java
try {
    // データベースに投稿を保存
    savePost(title, content);
    
    // 投稿一覧ページにリダイレクト
    response.sendRedirect(request.getContextPath() + "/posts");
    
} catch (SQLException e) {
    // データベースエラーの場合
    System.err.println("データベースエラー: " + e.getMessage());
    
    // エラー情報をリクエスト属性に設定
    request.setAttribute("title", title);
    request.setAttribute("content", content);
    request.setAttribute("errorMessage", "投稿の保存中にエラーが発生しました。");
    
    // フォームページに戻る
    request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
}
```

> 📝 **ポイント**: エラーが発生した場合、入力された内容を保持してフォームページに戻ります。これによりユーザーは再入力の手間を省けます。

### 4.5 投稿フォームのエラー表示対応

JSPファイルに簡単なエラーメッセージ表示機能を追加します。

#### postForm.jspの修正

h1タグの後に、以下のエラーメッセージ表示コードを追加します。

```html
<h1>新規ブログ投稿</h1>

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

#### 入力値の保持

フォームの入力フィールドで、エラー時の値を保持するように修正します。

```html
<%
String savedTitle = (String) request.getAttribute("title");
String savedContent = (String) request.getAttribute("content");
%>

<form action="<%= request.getContextPath() %>/createPost" method="post">
    <div>
        <label for="title">タイトル:</label>
        <input type="text" id="title" name="title" maxlength="100" 
               value="<%= savedTitle != null ? savedTitle : "" %>" required>
        <small style="color: #666; font-size: 0.9em;">最大100文字まで入力できます</small>
    </div>
    <div>
        <label for="content">内容:</label>
        <textarea id="content" name="content" rows="10" required 
                  placeholder="投稿の内容を入力してください..."><%= savedContent != null ? savedContent : "" %></textarea>
        <small style="color: #666; font-size: 0.9em;">改行も含めて自由に入力してください</small>
    </div>
    <div style="margin-top: 20px;">
        <button type="submit" class="button">投稿</button>
        <a href="<%= request.getContextPath() %>/posts" class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
    </div>
</form>
```

> 📝 **ポイント**: `value`属性と`textarea`の内容で、エラー時に入力された値を保持します。`!= null ? savedTitle : ""`により、値が存在しない場合は空文字を表示します。

### 4.6 動作確認

修正したアプリケーションの動作を確認します。

#### 正常ケースのテスト

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. 新規投稿ページにアクセス： `http://localhost:8080/blog-app/jsp/postForm.jsp`
4. タイトルと内容を入力して「投稿」ボタンをクリック
5. 投稿一覧ページにリダイレクトされ、新しい投稿が表示されることを確認

#### データベースでの確認

投稿後、データベースで実際にデータが保存されているか確認します。

```sql
USE blog_app;
SELECT * FROM posts ORDER BY created_at DESC LIMIT 5;
```

#### 期待される結果

* 新しい投稿がデータベースに保存される
* IDが自動的に採番される
* 作成日時が自動的に設定される
* 投稿一覧ページで新しい投稿が最上部に表示される

### 4.7 AppInitializerクラスの削除

データベース対応により、アプリケーションスコープでの投稿管理が不要になったため、AppInitializerクラスを削除できます。

#### 削除手順

1. `src/main/java/com/example/blog/listener/AppInitializer.java` を削除
2. プロジェクトをクリーンビルド： `mvn clean package`

> 📝 **ポイント**: AppInitializerクラスを削除しても、既存の投稿機能には影響ありません。すべてデータベースから取得するようになったためです。

### 4.8 完成したPostCreateServlet

すべての修正を適用したPostCreateServletの完全なコードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

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
        
        // 基本的な入力チェック
        if (title == null || title.trim().isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
            return;
        }
        
        if (content == null || content.trim().isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
            return;
        }
        
        try {
            // データベースに投稿を保存
            savePost(title.trim(), content.trim());
            
            // 投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
            
        } catch (SQLException e) {
            System.err.println("データベースエラー: " + e.getMessage());
            
            // エラー情報をリクエスト属性に設定
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.setAttribute("errorMessage", "投稿の保存中にエラーが発生しました。");
            
            // フォームページに戻る
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
        }
    }
    
    private void savePost(String title, String content) throws SQLException {
        String sql = "INSERT INTO posts (title, content) VALUES (?, ?)";
        
        try (Connection connection = DatabaseUtil.getConnection();
             PreparedStatement pstmt = connection.prepareStatement(sql)) {
            
            pstmt.setString(1, title);
            pstmt.setString(2, content);
            
            pstmt.executeUpdate();
        }
    }
}
```

これで新規投稿機能のデータベース対応が完了しました。次のセクションでは、接続管理とエラーハンドリングの改善について学習していきます。
