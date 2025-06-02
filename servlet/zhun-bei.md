# 準備

{% code title="Post.java" %}
```java
package com.example.blog.model;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

/**
 * ブログ投稿を表すモデルクラス
 */
public class Post {
    // プロパティの宣言
    private int id;
    private String title;
    private String content;
    private LocalDateTime createdAt;
    
    /**
     * デフォルトコンストラクター
     */
    public Post() {
        this.createdAt = LocalDateTime.now(); // 現在の日時で初期化
    }
    
    /**
     * タイトルと内容を指定して投稿を作成するコンストラクター
     * 
     * @param title   投稿のタイトル
     * @param content 投稿の内容
     */
    public Post(String title, String content) {
        this(); // デフォルトコンストラクターを呼び出す (日時の初期化) 
        this.title = title;
        this.content = content;
    }
    
    /**
     * すべてのプロパティを指定して投稿を作成するコンストラクター
     * 
     * @param id        投稿ID
     * @param title     投稿のタイトル
     * @param content   投稿の内容
     * @param createdAt 投稿の作成日時
     */
    public Post(int id, String title, String content, LocalDateTime createdAt) {
        this.id = id;
        this.title = title;
        this.content = content;
        this.createdAt = createdAt;
    }
    
    // ゲッターとセッター
    public int getId() {
        return id;
    }
    
    public void setId(int id) {
        this.id = id;
    }
    
    public String getTitle() {
        return title;
    }
    
    public void setTitle(String title) {
        this.title = title;
    }
    
    public String getContent() {
        return content;
    }
    
    public void setContent(String content) {
        this.content = content;
    }
    
    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
    
    public void setCreatedAt(LocalDateTime createdAt) {
        this.createdAt = createdAt;
    }
    
    /**
     * 投稿内容の要約を返す (最初の100文字まで) 
     * 
     * @return 投稿内容の要約
     */
    public String getSummary() {
        if (content == null || content.length() <= 100) {
            return content;
        }
        return content.substring(0, 100) + "...";
    }
    
    /**
     * 作成日時を指定フォーマットで返す
     * 
     * @param pattern フォーマットパターン
     * @return フォーマットされた日時文字列
     */
    public String getFormattedCreatedAt(String pattern) {
        return createdAt.format(DateTimeFormatter.ofPattern(pattern));
    }

    /**
     * 作成日時を標準フォーマット（yyyy/MM/dd HH:mm:ss）で返す
     * 
     * @return フォーマットされた日時文字列
     */
    public String getFormattedCreatedAt() {
        return getFormattedCreatedAt("yyyy/MM/dd HH:mm:ss");
    }
    
    /**
     * 投稿の文字列表現を返す
     * 
     * @return 投稿の文字列表現
     */
    @Override
    public String toString() {
        String displayContent = content != null && content.length() > 20 
            ? content.substring(0, 20) + "..." 
            : content;
        
        return "Post [id=" + id + 
            ", title=" + title + 
            ", content=" + displayContent + 
            ", createdAt=" + createdAt + 
            "]";
    }
}
```
{% endcode %}

{% code title="PostCreateServlet.java" %}
```java
package com.example.blog.servlet;

import java.io.IOException;
// import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    // 投稿を一時的に保存するリスト (アプリケーション再起動で消えます)
    private static List<Post> posts = new ArrayList<>();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // リクエストパラメーターの文字コードを設定
        request.setCharacterEncoding("UTF-8");
        
        // フォームから送信されたデータを取得
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 現在の日時を取得
        // LocalDateTime now = LocalDateTime.now();
        
        // 投稿データをリストに追加（タイトル、内容、日時の順）
        // posts.add(new String[] { title, content, now.toString() });
        
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title, content);

        // 投稿をリストに追加
        posts.add(newPost);
        
        // 投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
    }
    
    // 投稿リストを取得するメソッド (後で他のサーブレットから使用)
    public static List<Post> getPosts() {
        return posts;
    }
}
```
{% endcode %}

{% code title="PostListServlet.java" %}
```java
package com.example.blog.servlet;

import java.io.IOException;
// import java.time.LocalDateTime;
// import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import com.example.blog.model.Post;

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
        
        // 投稿データを取得
        List<Post> posts = PostCreateServlet.getPosts();
        
        // 投稿を新しい順に並べ替え (日時の降順)
        List<Post> sortedPosts = new ArrayList<>(posts);
        Collections.reverse(sortedPosts);
        
        // リクエスト属性に投稿リストと総数をセット
        request.setAttribute("posts", sortedPosts);
        request.setAttribute("totalPosts", sortedPosts.size());
                
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```
{% endcode %}

{% code title="postForm.jsp" %}
```java
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
{% endcode %}

{% code title="postList.jsp" %}
```java
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<%@ page import="com.example.blog.model.Post" %>
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

    <a href="<%= request.getContextPath() %>/jsp/postForm.jsp" class="button">新規投稿</a>

    <div style="margin-top: 20px;">
    <% 
    List<Post> posts = (List<Post>) request.getAttribute("posts");

    if (posts != null && !posts.isEmpty()) {
        for (Post post : posts) {
    %>
        <div class="post">
            <h2><%= post.getTitle() %></h2>
            <p class="post-date">投稿日時: <%= post.getFormattedCreatedAt("yyyy/MM/dd HH:mm") %></p>
            <p><%= post.getSummary().replace("\n", "<br>") %></p>
            <p><a href="<%= request.getContextPath() %>/post?id=<%= post.getId() %>">続きを読む</a></p>
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
{% endcode %}

{% code title="" %}
```java
// Some code
```
{% endcode %}
