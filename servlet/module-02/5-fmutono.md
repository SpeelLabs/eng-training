# 5. 投稿フォームと一覧表示の連携

### 5.1 アプリケーションのナビゲーション改善

前のセクションまでで、基本的な投稿フォームと一覧表示機能を実装しました。しかし、アプリケーション内のナビゲーションが不十分で、ページ間の移動がスムーズではありません。このセクションでは、リンクの追加や絶対パスの使用などによって、アプリケーション内のナビゲーションを改善します。

#### 投稿フォームのリンク修正

まず、`postForm.jsp` の投稿フォームのアクションパスを修正して、コンテキストパスを含めた絶対パスに変更します。

```html
<form action="<%= request.getContextPath() %>/createPost" method="post">
```

> 📝 **ポイント**: `request.getContextPath()` を使用することで、アプリケーションのコンテキストパス (例: `/blog-app` ) を取得できます。これにより、フォームがどのディレクトリに置かれていても、正しいサーブレットにデータを送信できます。

また、投稿一覧ページに戻るためのリンクを追加します。フォームの下に以下のコードを追加しましょう。

```html
<div style="margin-top: 20px;">
    <a href="<%= request.getContextPath() %>/posts" style="text-decoration: none; color: #337ab7;">
        投稿一覧に戻る
    </a>
</div>
```

#### 投稿一覧ページのリンク修正

同様に、`postList.jsp` の新規投稿リンクも絶対パスを使用するように修正します。

```html
<a href="<%= request.getContextPath() %>/jsp/postForm.jsp" class="new-post-link">新規投稿</a>
```

> 📝 **ポイント**: 絶対パスを使用することで、どの URL からアクセスしても正しいリンク先に移動できます。アプリケーションが別のコンテキストパスにデプロイされた場合でも問題なく機能します。

#### PostCreateServlet のリダイレクト修正

最後に、`PostCreateServlet` のリダイレクト処理もコンテキストパスを含めた絶対パスを使用するように修正します。

```java
// 投稿一覧ページにリダイレクト
response.sendRedirect(request.getContextPath() + "/posts");
```

### 5.2 改善された画面遷移フロー

これらの修正により、アプリケーション内のナビゲーションが以下のように改善されます。

1. 投稿一覧ページ ( `/posts` )
   * 新規投稿ページへのリンク
2. 新規投稿ページ ( `/jsp/postForm.jsp` )
   * フォーム送信先を正確に指定 ( `/createPost` )
   * 投稿一覧ページへ戻るリンク
3. 投稿処理 ( `/createPost`)
   * 処理後、正確に投稿一覧ページへリダイレクト

この改善により、ユーザーはアプリケーション内でスムーズに移動でき、どのページからでも正しく機能するようになります。

### 5.3 完成した postForm.jsp

修正した `postForm.jsp` の全体コードは以下のようになります。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>新規投稿</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #333;
        }
        form {
            max-width: 600px;
            margin-top: 20px;
        }
        div {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"], textarea {
            width: 100%;
            padding: 8px;
            box-sizing: border-box;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        a {
            color: #337ab7;
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <h1>新規ブログ投稿</h1>
    <form action="<%= request.getContextPath() %>/createPost" method="post">
        <div>
            <label for="title">タイトル:</label>
            <input type="text" id="title" name="title" required>
        </div>
        <div>
            <label for="content">内容:</label>
            <textarea id="content" name="content" rows="5" required></textarea>
        </div>
        <div>
            <button type="submit">投稿</button>
        </div>
    </form>
    
    <div style="margin-top: 20px;">
        <a href="<%= request.getContextPath() %>/posts">
            投稿一覧に戻る
        </a>
    </div>
</body>
</html>
```

### 5.4 完成した postList.jsp

修正した `postList.jsp` の全体コードは以下のようになります。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<%@ page import="java.time.LocalDateTime" %>
<%@ page import="java.time.format.DateTimeFormatter" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>投稿一覧</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #333;
        }
        .post {
            border: 1px solid #ddd;
            padding: 15px;
            margin-bottom: 15px;
            border-radius: 5px;
        }
        .post h2 {
            margin-top: 0;
            color: #444;
        }
        .post-date {
            color: #777;
            font-size: 0.9em;
            margin-bottom: 10px;
        }
        .new-post-link {
            display: inline-block;
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            text-decoration: none;
            border-radius: 4px;
            margin-bottom: 20px;
        }
        .new-post-link:hover {
            background-color: #45a049;
        }
        .no-posts {
            color: #666;
            font-style: italic;
        }
        .post-count {
            font-size: 0.8em;
            color: #777;
            font-weight: normal;
        }
    </style>
</head>
<body>
    <h1>投稿一覧 <span class="post-count">(全${totalPosts}件)</span></h1>
    
    <a href="<%= request.getContextPath() %>/jsp/postForm.jsp" class="new-post-link">新規投稿</a>

    <div style="margin-top: 20px;">
    <% 
    List<String[]> posts = (List<String[]>) request.getAttribute("posts");
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm");

    if (posts != null && !posts.isEmpty()) {
        for (String[] post : posts) {
            String title = post[0];
            String content = post[1];
            String dateTimeStr = post[2];
            
            // LocalDateTimeに変換してフォーマット
            LocalDateTime dateTime = LocalDateTime.parse(dateTimeStr);
    %>
        <div class="post">
            <h2><%= title %></h2>
            <p class="post-date">投稿日時: <%= dateTime.format(formatter) %></p>
            <p><%= content.replace("\n", "<br>") %></p>
        </div>
    <% 
        }
    } else {
    %>
        <p class="no-posts">投稿がありません。</p>
    <% } %>
    </div>
</body>
</html>
```

### 5.5 完成した PostCreateServlet

修正した `PostCreateServlet.java` の全体コードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    // 投稿を一時的に保存するリスト (アプリケーション再起動で消えます)
    private static List<String[]> posts = new ArrayList<>();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // リクエストパラメーターの文字コードを設定
        request.setCharacterEncoding("UTF-8");
        
        // フォームから送信されたデータを取得
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 現在の日時を取得
        LocalDateTime now = LocalDateTime.now();
        
        // 投稿データをリストに追加（タイトル、内容、日時の順）
        posts.add(new String[] { title, content, now.toString() });
        
        // 投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
    }
    
    // 投稿リストを取得するメソッド (後で他のサーブレットから使用)
    public static List<String[]> getPosts() {
        return posts;
    }
}
```

### 5.6 動作確認

修正したアプリケーションの動作を確認します。

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザで投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`
4. ページ間のナビゲーションが正しく機能することを確認
   * 投稿一覧から新規投稿ページへ
   * 新規投稿ページから投稿一覧へ
   * 投稿処理後、正しく投稿一覧へ

これらの改善により、アプリケーション内の移動がより直感的になり、ユーザーエクスペリエンスが向上します。

> 📝 **ポイント**: モジュール 2 では最小限のナビゲーション機能に焦点を当てています。モジュール 3 やパート 2 でさらに高度な機能 (投稿の編集・削除など) を追加する際に、ナビゲーション構造も拡張していきます。また、大規模なアプリケーションでは、ナビゲーションパーツを共通の JSP ファイルとして切り出し、再利用するのが一般的です。
