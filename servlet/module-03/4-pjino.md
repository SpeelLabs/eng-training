# 4. 投稿詳細表示ページの実装

### 4.1 投稿詳細表示用のサーブレット作成

投稿一覧ページの 「続きを読む」 リンクをクリックした際に、その投稿の詳細を表示するためのサーブレットを作成します。このサーブレットは、URL パラメーターで指定された投稿 ID を使って、特定の投稿データを取得して表示します。

#### PostDetailServlet の基本構造

`src/main/java/com/example/blog/servlet` パッケージに `PostDetailServlet.java` ファイルを作成します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.util.List;

import com.example.blog.model.Post;

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
        // ここに ID による投稿検索と詳細表示のコードを追加します
    }
}
```

> 📝 **ポイント**: このサーブレットの URL `/post` に対して、投稿 ID をパラメーターとして渡します (例: `/post?id=1` )。これにより、投稿一覧ページからのリンクで詳細ページに移動できるようになります。

#### 投稿 ID の取得と検証

`doGet` メソッド内で、リクエストパラメーターから投稿 ID を取得し、検証するコードを追加します。

```java
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
```

> 📝 **ポイント**: URL パラメーターはすべて文字列として取得されるため、`Integer.parseInt()` で数値に変換する必要があります。また、不正な ID が指定された場合は、例外処理を行い、ユーザーを投稿一覧ページに戻します。

#### 投稿データの検索

次に、取得した ID に一致する投稿をアプリケーションスコープの投稿リストから検索します。

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

// 投稿が見つからない場合は投稿一覧ページにリダイレクト
if (targetPost == null) {
    response.sendRedirect(request.getContextPath() + "/posts");
    return;
}
```

> 📝 **ポイント**: ここでは線形探索を使用して投稿を検索しています。次のような Stream API を使ったより簡潔な書き方も可能です。

```java
// 取得した ID の投稿を検索
Post targetPost = posts.stream()
                       .filter(post -> post.getId() == postId)
                       .findFirst()
                       .orElse(null);
```

#### JSP へのフォワード

見つかった投稿をリクエスト属性にセットし、詳細表示用の JSP にフォワードします。

```java
// 見つかった投稿をリクエスト属性にセット
request.setAttribute("post", targetPost);

// 詳細表示用 JSP にフォワード
request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
```

#### 完成した PostDetailServlet

上記のコードを組み合わせた、完成版の `PostDetailServlet.java` は以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.util.List;

import com.example.blog.model.Post;

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
        
        // 投稿が見つからない場合は投稿一覧ページにリダイレクト
        if (targetPost == null) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        // 見つかった投稿をリクエスト属性にセット
        request.setAttribute("post", targetPost);
        
        // 詳細表示用 JSP にフォワード
        request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
    }
}
```

### 4.2 投稿詳細表示用の JSP ファイル作成

次に、投稿の詳細を表示するための JSP ファイルを作成します。このファイルは、サーブレットから渡された投稿データを表示します。

#### postDetail.jsp の作成

`src/main/webapp/jsp` ディレクトリに `postDetail.jsp` ファイルを作成します。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="com.example.blog.model.Post" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿詳細" />
</jsp:include>

<%
Post post = (Post) request.getAttribute("post");
%>

<div class="post-navigation">
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">← 投稿一覧に戻る</a>
</div>

<div class="post-detail">
    <h1><%= post.getTitle() %></h1>
    <p class="post-meta">投稿日時: <%= post.getFormattedCreatedAt() %></p>
    <div class="post-content">
        <%= post.getContent().replace("\n", "<br>") %>
    </div>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: 投稿詳細ページでは、以下の要素を表示しています。
>
> * 投稿のタイトル
> * 投稿の作成日時（Post クラスの getFormattedCreatedAt() メソッドを使用）
> * 投稿の内容 (改行はHTMLの `<br>` タグに変換)
> * 投稿一覧ページに戻るためのリンク

#### 詳細表示用のスタイル追加

投稿詳細ページのスタイルを共通ヘッダーに追加します。`src/main/webapp/jsp/common/header.jsp` のスタイル部分に以下を追加します。

```css
/* 投稿詳細ページのスタイル */
.post-navigation {
    margin-bottom: 20px;
}
.post-detail h1 {
    margin-top: 0;
    color: #333;
    border-bottom: 2px solid #4a89dc;
    padding-bottom: 10px;
}
.post-meta {
    color: #777;
    font-size: 0.9em;
    margin-bottom: 20px;
    padding: 10px;
    background-color: #f8f9fa;
    border-left: 4px solid #4a89dc;
}
.post-content {
    line-height: 1.8;
    font-size: 1.1em;
    margin-top: 20px;
}
```

これらのスタイルを header.jsp の `<style>` タグ内の適切な場所に追加します。

### 4.3 完成した postDetail.jsp

スタイル追加後の完成版 `postDetail.jsp` は以下のようになります。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="com.example.blog.model.Post" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿詳細" />
</jsp:include>

<%
Post post = (Post) request.getAttribute("post");
%>

<div class="post-navigation">
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">← 投稿一覧に戻る</a>
</div>

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

<div class="post-navigation" style="margin-top: 30px;">
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">← 投稿一覧に戻る</a>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

### 4.4 動作確認

以上の実装で、投稿詳細表示機能が完成しました。動作を確認するには、以下の手順を実行します。

#### プロジェクトのビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### 詳細ページの確認

ブラウザーで投稿一覧ページにアクセスし、いずれかの投稿の 「続きを読む」 リンクをクリックすると、その投稿の詳細ページが表示されます。

#### 表示内容の確認

詳細ページで以下の項目が正しく表示されることを確認してください：

1. **投稿タイトル**: ページのメインタイトルとして表示
2. **投稿メタ情報**: 投稿日時とIDの表示
3. **投稿内容**: 完全な投稿内容（改行も適切に表示）
4. **ナビゲーション**: 投稿一覧に戻るボタン（上下に配置）
5. **レイアウト**: 統一されたヘッダー・フッターデザイン

#### URL の確認

詳細ページの URL は `/post?id=X` の形式になります。ここで `X` は投稿の ID です。

例:

* `http://localhost:8080/blog-app/post?id=1`
* `http://localhost:8080/blog-app/post?id=2`
* `http://localhost:8080/blog-app/post?id=3`

#### エラーハンドリングの確認

以下のケースでエラーハンドリングが正しく動作することを確認してください：

1. **IDなし**: `http://localhost:8080/blog-app/post` → 投稿一覧にリダイレクト
2. **不正なID**: `http://localhost:8080/blog-app/post?id=abc` → 投稿一覧にリダイレクト
3. **存在しないID**: `http://localhost:8080/blog-app/post?id=999` → 投稿一覧にリダイレクト

#### 完全な動作フローの確認

1. 投稿一覧ページにアクセス
2. いずれかの投稿の「続きを読む」をクリック
3. 投稿詳細ページが正しく表示される
4. 「投稿一覧に戻る」ボタンで一覧に戻れる
5. 新規投稿を作成後、その投稿の詳細ページも正しく表示される

> 📝 **ポイント**: 投稿詳細ページでは、Postクラスの各メソッド（getTitle(), getFormattedCreatedAt(), getContent(), getId()）を活用して、構造化されたデータ表示を実現しています。これにより、コードの可読性と保守性が向上しています。また、エラーハンドリングにより、不正なアクセスに対しても適切に対応できています。
