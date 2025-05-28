# 4. 投稿保存と一覧表示機能の実装

### 4.1 投稿一覧表示機能の実装

前のセクションで作成した `PostCreateServlet` はデータを保存するものでしたが、保存したデータを表示する機能はまだありません。このセクションでは、保存された投稿を一覧表示するための機能を実装します。

サーブレットが投稿データを取得し、JSP に渡して表示するという流れを構築します。

### 4.2 投稿一覧表示用のサーブレットを作成する

まず、投稿一覧を表示するためのサーブレットの基本構造を作成します。`src/main/java/com/example/blog/servlet` に `PostListServlet.java` ファイルを作成し、以下のコードを入力します。

{% code overflow="wrap" %}
```java
package com.example.blog.servlet;

import java.io.IOException;
import java.util.List;

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
        // ここに投稿一覧表示の処理を実装していきます
    }
}
```
{% endcode %}

> 📝 **ポイント**: このサーブレットは `/posts` という URL パターンに対応し、GET リクエストを処理します。一覧表示はデータの取得 (読み取り) 操作なので、GET メソッドを使用するのが適切です。

#### 投稿データの取得と設定

次に、`PostCreateServlet` から投稿データを取得し、リクエスト属性に設定します。`doGet` メソッド内に以下のコードを追加します。

{% code overflow="wrap" %}
```java
// 投稿データを取得
List<String[]> posts = PostCreateServlet.getPosts();

// リクエスト属性に投稿リストをセット
request.setAttribute("posts", posts);
```
{% endcode %}

> 📝 **ポイント**: `request.setAttribute()` メソッドを使用して、データをリクエスト属性として設定します。これは、サーブレットから JSP にデータを渡すための重要な方法です。最初の引数は属性の名前 (キー) で、二番目の引数は属性の値 (オブジェクト) です。ここでは、取得した投稿リストを "posts" という名前の属性として設定しています。この属性は、後でJSPから `request.getAttribute("posts")` を使ってアクセスできます。

#### JSPへのフォワード

最後に、処理をJSPにフォワードするコードを追加します。

{% code overflow="wrap" %}
```java
// JSPにフォワード
request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
```
{% endcode %}

> 📝 **ポイント**: `request.getRequestDispatcher()` メソッドで指定したJSPに処理を転送し、`forward()` メソッドでリクエストとレスポンスオブジェクトを JSP に引き渡します。これにより、設定したリクエスト属性をJSPで利用できるようになります。

### 4.3 投稿一覧表示用の JSP を作成する

次に、投稿一覧を表示するための JSP ファイルを作成します。`src/main/webapp/jsp` に `postList.jsp` ファイルを作成します。

#### 基本的な HTML 構造

まず、ページの基本構造とスタイルを定義します。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
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
    </style>
</head>
<body>
    <h1>投稿一覧</h1>
    
    <a href="jsp/postForm.jsp" class="new-post-link">新規投稿</a>
</body>
</html>
```
{% endcode %}

#### 投稿データの表示

次に、リクエスト属性から投稿データを取得し、それを表示するコードを追加します。

{% code overflow="wrap" %}
```html
<div style="margin-top: 20px;">
<% 
List<String[]> posts = (List<String[]>) request.getAttribute("posts");

if (posts != null && !posts.isEmpty()) {
    for (int i = posts.size() - 1; i >= 0; i--) {
        String[] post = posts.get(i);
%>
    <div class="post">
        <h2><%= post[0] %></h2>
        <p><%= post[1].replace("\n", "<br>") %></p>
    </div>
<% 
    }
} else {
%>
    <p class="no-posts">投稿がありません。</p>
<% } %>
</div>
```
{% endcode %}

> 📝 **ポイント**: `request.getAttribute("posts")` を使って、サーブレットで設定したリクエスト属性から投稿リストを取得しています。取得したオブジェクトは元々 `List<String[]>` 型なので、適切にキャストする必要があります。リストが空ではない場合、各投稿の内容を表示し、空の場合は 「投稿がありません」 というメッセージを表示します。最新の投稿を先に表示するため、リストを逆順に処理しています。

### 4.4 投稿後の画面遷移を改善する

現在の `PostCreateServlet` では、投稿後に完了メッセージを表示していますが、より良いユーザー体験のためには、投稿後に投稿一覧ページに自動的にリダイレクトするようにします。`PostCreateServlet.java` の `doPost` メソッド内の処理を修正します。

{% code overflow="wrap" %}
```java
// 投稿データをリストに追加
posts.add(new String[] { title, content });

// 投稿一覧ページにリダイレクト
response.sendRedirect("posts");
```
{% endcode %}

> 📝 **ポイント**: `response.sendRedirect()` メソッドを使用すると、処理完了後に別の URL にリダイレクトできます。これにより、ユーザーを投稿一覧ページに自動的に移動させることができます。リダイレクトすると、新しい HTTP リクエストが発生するため、リクエスト属性は引き継がれませんが、現在のケースではそれは問題ありません。

### 4.5 完成した画面遷移フロー

これで、以下のようなユーザーフローが実現できました。

1. ユーザーが投稿一覧ページにアクセス
2. 「新規投稿」 リンクをクリックして投稿フォームに移動
3. フォームに入力して投稿
4. 投稿処理後、自動的に投稿一覧ページに戻る (投稿が一覧に表示される)

このフローにより、ユーザーは継続的に投稿を作成し、すぐに結果を確認することができます。

### 4.6 動作確認と検証

修正したアプリケーションの動作を確認します。

1. プロジェクトをビルド： `mvn clean package`
2. Tomcatサーバーを再起動
3. ブラウザで投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`

最初はまだ投稿がないため、「投稿がありません。」 というメッセージが表示されるはずです。「新規投稿」 リンクをクリックして投稿を追加すると、投稿一覧ページに追加した投稿が表示されます。

> 📝 **ポイント**: 現在の実装では、投稿データはサーバーのメモリーに保存されているため、サーバーを再起動すると消えてしまいます。後のモジュールでデータベースを使用して永続化する方法を学びます。
