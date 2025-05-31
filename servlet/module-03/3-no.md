# 3. 投稿一覧表示機能の強化

### 3.1 PostListServlet の改良

前のセクションでは、アプリケーションスコープを使用して投稿データを保存する仕組みを実装しました。このセクションでは、モジュール 2 で作成した `PostListServlet` を修正して、アプリケーションスコープから投稿データを取得し、一覧表示する機能を実装します。

#### 必要なインポート

まず、必要なクラスをインポートします。

```java
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;
import com.example.blog.model.Post;
```

#### アプリケーションスコープからのデータ取得

`PostListServlet.java` を開き、投稿データを取得する部分を以下のように変更します。

```java
// アプリケーションスコープから投稿リストを取得
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");

// リクエスト属性に投稿リストをセット
request.setAttribute("posts", posts);
```

#### 投稿の並べ替え

投稿を新しい順 (作成日時の降順) に並べ替えて表示するようにします。

```java
// アプリケーションスコープから投稿リストを取得
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");

// 投稿を作成日時の降順で並べ替え
List<Post> sortedPosts = posts.stream()
    .sorted(Comparator.comparing(Post::getCreatedAt).reversed())
    .collect(Collectors.toList());

// リクエスト属性に投稿リストと総数をセット
request.setAttribute("posts", sortedPosts);
request.setAttribute("totalPosts", sortedPosts.size());
```

> 📝 **ポイント**: Stream API とラムダ式を使用することで、コレクションの操作を簡潔に記述できます。`Comparator.comparing(Post::getCreatedAt).reversed()` は、Post オブジェクトの `getCreatedAt()` メソッドの戻り値を比較し、その順序を反転させることで降順ソートを実現しています。メソッド参照 ( :: 演算子) は、メソッドを直接参照する簡潔な記述方法です。

#### 完成したPostListServlet

修正した `PostListServlet` の全体コードは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;

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
        
        // アプリケーションスコープから投稿リストを取得
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");

        // 投稿を作成日時の降順で並べ替え
        List<Post> sortedPosts = posts.stream()
            .sorted(Comparator.comparing(Post::getCreatedAt).reversed())
            .collect(Collectors.toList());

        // リクエスト属性に投稿リストと総数をセット
        request.setAttribute("posts", sortedPosts);
        request.setAttribute("totalPosts", sortedPosts.size());
                
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
    }
}
```

### 3.2 投稿一覧表示用 JSP の修正

次に、`postList.jsp` ファイルを修正して、Post オブジェクトの内容を適切に表示できるようにします。

#### 必要なクラスのインポート

まず、必要なクラスをインポートします。

```html
<%@ page import="java.util.List" %>
<%@ page import="com.example.blog.model.Post" %>
<%@ page import="java.time.format.DateTimeFormatter" %>
```

#### 投稿リストの取得とフォーマッター設定

次に、投稿リストの取得とキャストを行い、日付フォーマッターを作成します。

```html
<%
List<Post> posts = (List<Post>) request.getAttribute("posts");
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");
%>
```

#### 投稿表示部分の修正

投稿の表示部分を修正して、Post オブジェクトの情報を表示します。

```html
<% 
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
```

#### 投稿数の表示

投稿の総数を表示する部分を追加します。

```html
<h1>投稿一覧 <span class="post-count">(全${totalPosts}件)</span></h1>
```

#### 投稿日時表示用のスタイル追加

投稿日時の表示用に、スタイルを header.jsp に追加します。`header.jsp` ファイルを開き、style タグ内に以下のスタイルを追加します。

```css
.post-date {
    color: #777;
    font-size: 0.9em;
    margin-bottom: 10px;
}

.post-count {
    font-size: 0.8em;
    color: #777;
    font-weight: normal;
}
```

#### 完成した postList.jsp

修正した `postList.jsp` の全体コードは次のようになります。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<%@ page import="com.example.blog.model.Post" %>
<%@ page import="java.time.format.DateTimeFormatter" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿一覧" />
</jsp:include>

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

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: ここでは `Post` クラスの `getSummary()` メソッドを使用して、投稿内容の要約を表示しています。これにより、長い投稿内容の一部だけを表示し、詳細は 「続きを読む」 リンクから確認できるようにしています。また、Postクラスに追加した `getFormattedCreatedAt()` メソッドを使用して、日時を読みやすい形式で表示しています。

### 3.3 共通ヘッダーのスタイル更新

投稿日時と投稿数表示用のスタイルを共通ヘッダーに追加します。`src/main/webapp/jsp/common/header.jsp` のスタイル部分に以下を追加します。

```css
.post-date {
    color: #777;
    font-size: 0.9em;
    margin-bottom: 10px;
}

.post-count {
    font-size: 0.8em;
    color: #777;
    font-weight: normal;
}
```

これらのスタイルを header.jsp の `<style>` タグ内の適切な場所（例えば、既存の `.post` スタイルの後）に追加します。

### 3.4 動作確認

以上の実装で、投稿一覧表示機能が強化されました。動作を確認するには、以下の手順を実行します。

#### プロジェクトのビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### 投稿一覧の確認

ブラウザーで投稿一覧ページにアクセスします。

```
http://localhost:8080/blog-app/posts
```

AppInitializer で作成したサンプル投稿が表示されるはずです。各投稿には、タイトル、投稿日時、内容の要約、および 「続きを読む」 リンクが表示されます。また、ページ上部には投稿の総数も表示されます。

#### 表示内容の確認

以下の項目が正しく表示されることを確認してください：

1. **投稿数の表示**: 「投稿一覧 (全3件)」のような形式で表示
2. **投稿の並び順**: 最新の投稿が上に表示される（作成日時の降順）
3. **投稿の基本情報**: タイトル、投稿日時、内容の要約
4. **ナビゲーション**: 新規投稿ボタンと続きを読むリンク
5. **レイアウト**: 共通ヘッダー・フッターによる統一されたデザイン

#### 新規投稿の動作確認

1. 「新規投稿」ボタンをクリックして投稿フォームに移動
2. 新しい投稿を作成
3. 投稿後、一覧ページに戻り、新しい投稿が最上部に表示されることを確認
4. 投稿数が正しく更新されることを確認

> 📝 **ポイント**: 現時点では 「続きを読む」 リンクをクリックしても、詳細表示するサーブレットが実装されていないため、エラーになります。次のセクションで投稿詳細表示機能を実装します。また、PostクラスのgetFormattedCreatedAt()メソッドを使用することで、日時の表示が統一され、コードの保守性も向上しています。
