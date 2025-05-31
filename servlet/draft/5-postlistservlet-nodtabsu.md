# 5. PostListServlet のデータベース対応

### 5.1 既存のPostListServletの確認

前のセクションで投稿作成機能のデータベース対応が完了しました。このセクションでは、投稿一覧表示機能をデータベース対応に修正していきます。まず、パート1で作成した `PostListServlet` の構造を確認しましょう。

#### パート1での実装（復習）

パート1の `PostListServlet` は以下のような構造でした：

```java
// パート1の PostListServlet（復習）
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

このサーブレットをデータベース対応に修正していきます。

### 5.2 段階的な修正

#### インポート文の修正

まず、不要になったインポート文を削除し、必要なインポート文を追加します。`PostListServlet.java` の先頭部分を以下のように修正してください：

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.util.List;

import com.example.blog.model.Post;
import com.example.blog.util.DatabaseUtil; // 追加

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// 以下のインポートは不要になったので削除
// import java.util.Comparator;
// import java.util.stream.Collectors;
```

> 📝 **ポイント**: データベースから取得する際に `ORDER BY created_at DESC` でソートするため、Java側でのストリーム操作は不要になります。データベース側でソートする方が効率的です。

#### doGetメソッドの修正

次に、`doGet` メソッドをデータベース対応に修正していきます。まず、基本構造を変更します：

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // データベースから投稿一覧を取得
    List<Post> posts = DatabaseUtil.getAllPosts();
    
    // リクエスト属性に投稿リストと総数をセット
    request.setAttribute("posts", posts);
    request.setAttribute("totalPosts", posts.size());
            
    // JSPにフォワード
    request.getRequestDispatcher("/jsp/post/list.jsp").forward(request, response);
}
```

> 📝 **ポイント**: `DatabaseUtil.getAllPosts()` は既に新しい順（`ORDER BY created_at DESC`）でソートされた結果を返すため、Java側でのソート処理は不要です。JSPファイルのパスも新しい構造に合わせて変更しています。

#### エラーハンドリングの追加

データベース操作が失敗した場合のエラーハンドリングを追加します：

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    try {
        // データベースから投稿一覧を取得
        List<Post> posts = DatabaseUtil.getAllPosts();
        
        // リクエスト属性に投稿リストと総数をセット
        request.setAttribute("posts", posts);
        request.setAttribute("totalPosts", posts.size());
        
    } catch (Exception e) {
        // エラーが発生した場合は空のリストを設定
        request.setAttribute("posts", new java.util.ArrayList<Post>());
        request.setAttribute("totalPosts", 0);
        request.setAttribute("errorMessage", "投稿一覧の取得に失敗しました。");
        
        // ログに出力（開発時のデバッグ用）
        e.printStackTrace();
    }
            
    // JSPにフォワード
    request.getRequestDispatcher("/jsp/post/list.jsp").forward(request, response);
}
```

> 📝 **ポイント**: データベース接続エラーなどが発生した場合でも、アプリケーションが停止しないように適切なエラーハンドリングを追加しています。エラー時は空のリストを設定してJSPに渡します。

### 5.3 完成したPostListServlet

上記の修正を組み合わせた完成版の `PostListServlet.java` は以下のようになります：

```java
package com.example.blog.servlet;

import java.io.IOException;
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
            // データベースから投稿一覧を取得
            List<Post> posts = DatabaseUtil.getAllPosts();
            
            // リクエスト属性に投稿リストと総数をセット
            request.setAttribute("posts", posts);
            request.setAttribute("totalPosts", posts.size());
            
        } catch (Exception e) {
            // エラーが発生した場合は空のリストを設定
            request.setAttribute("posts", new java.util.ArrayList<Post>());
            request.setAttribute("totalPosts", 0);
            request.setAttribute("errorMessage", "投稿一覧の取得に失敗しました。");
            
            // ログに出力（開発時のデバッグ用）
            e.printStackTrace();
        }
                
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/post/list.jsp").forward(request, response);
    }
}
```

### 5.4 投稿一覧表示JSPの改善

#### 新しいlist.jspの作成

`src/main/webapp/jsp/post/` ディレクトリに `list.jsp` ファイルを作成します。パート1の `postList.jsp` を基に、データベース対応とエラー表示機能を追加します：

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<%@ page import="com.example.blog.model.Post" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿一覧" />
</jsp:include>

<h1>投稿一覧 <span class="post-count">(全${totalPosts}件)</span></h1>

<%-- エラーメッセージの表示 --%>
<%
String errorMessage = (String) request.getAttribute("errorMessage");
if (errorMessage != null) {
%>
    <div style="color: red; background-color: #ffebee; padding: 10px; margin-bottom: 15px; border: 1px solid #ffcdd2; border-radius: 4px;">
        <strong>エラー:</strong> <%= errorMessage %>
    </div>
<%
}
%>

<a href="<%= request.getContextPath() %>/jsp/post/create.jsp" class="button">新規投稿</a>

<div style="margin-top: 20px;">
<% 
List<Post> posts = (List<Post>) request.getAttribute("posts");

if (posts != null && !posts.isEmpty()) {
    for (Post post : posts) {
%>
    <div class="post">
        <h2>
            <a href="<%= request.getContextPath() %>/post?id=<%= post.getId() %>">
                <%= post.getTitle() %>
            </a>
        </h2>
        <p class="post-date">投稿日時: <%= post.getFormattedCreatedAt("yyyy/MM/dd HH:mm") %></p>
        <p><%= post.getSummary().replace("\n", "<br>") %></p>
        <p><a href="<%= request.getContextPath() %>/post?id=<%= post.getId() %>">続きを読む</a></p>
    </div>
<% 
    }
} else {
%>
    <div class="no-posts">
        <h3>投稿がありません</h3>
        <p>最初の投稿を作成してみましょう！</p>
        <a href="<%= request.getContextPath() %>/jsp/post/create.jsp" class="button">新規投稿を作成</a>
    </div>
<% } %>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

#### 主な改善点

1. **エラーメッセージ表示**: データベース接続エラーなどの場合にメッセージを表示
2. **リンクの更新**: 新規投稿リンクを新しいパスに更新
3. **空状態の改善**: 投稿がない場合のメッセージを改善
4. **データベース対応**: IDベースの詳細ページリンク

> 📝 **ポイント**: パート1では投稿の詳細表示リンクがありませんでしたが、データベースではIDによる検索が効率的に行えるため、詳細ページへのリンクを追加しています。

### 5.5 ナビゲーションリンクの更新

#### 共通ヘッダーの修正

`src/main/webapp/jsp/common/header.jsp` のナビゲーション部分を新しいパス構造に合わせて更新します：

```html
<div class="nav">
    <a href="<%= request.getContextPath() %>/posts">投稿一覧</a>
    <a href="<%= request.getContextPath() %>/jsp/post/create.jsp">新規投稿</a>
</div>
```

この修正により、アプリケーション全体のナビゲーションが一貫して新しい構造を使用するようになります。

### 5.6 動作確認

#### プロジェクトのビルドと実行

修正したサーブレットの動作を確認します：

1. プロジェクトをビルド：`mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザで投稿一覧ページにアクセス：`http://localhost:8080/blog-app/posts`

#### 正常ケースのテスト

以下の点を確認します：

1. **投稿一覧の表示**: データベースに保存された投稿が新しい順で表示される
2. **投稿数の表示**: 正しい投稿数が表示される
3. **リンクの動作**: 新規投稿リンクが正しく動作する
4. **レイアウト**: 統一されたデザインで表示される

#### データベースとの連携確認

投稿一覧に表示される内容がデータベースの内容と一致することを確認します：

```sql
-- MySQL で確認
USE blog_app;
SELECT id, title, LEFT(content, 50) AS content_preview, created_at 
FROM posts 
ORDER BY created_at DESC;
```

この結果とブラウザに表示される内容が一致していることを確認してください。

#### エラーケースのテスト（オプション）

データベース接続エラーのテストは、実際の開発では重要ですが、学習段階では以下の方法で簡単に確認できます：

1. MySQL サーバーを一時的に停止
2. 投稿一覧ページにアクセス
3. エラーメッセージが表示されることを確認
4. MySQL サーバーを再起動

### 5.7 パート1との動作比較

#### 主な変更点

| 項目          | パート1            | パート2（現在）       |
| ----------- | --------------- | -------------- |
| **データソース**  | アプリケーションスコープ    | MySQL データベース   |
| **ソート処理**   | Java Stream API | SQL ORDER BY   |
| **パフォーマンス** | メモリ内操作          | データベースクエリ      |
| **エラー処理**   | 基本的             | データベースエラーに対応   |
| **詳細リンク**   | なし              | IDベースの詳細ページリンク |

#### パフォーマンスの比較

**パート1**:

```
アプリケーションスコープ → List取得 → Streamソート → JSP表示
```

**パート2**:

```
データベース → SQLソート済み取得 → JSP表示
```

データベースでのソートの方が大量データに対して効率的です。

### 5.8 次のステップの準備

これで投稿一覧表示機能のデータベース対応が完了しました。次のセクションでは、投稿詳細表示機能をデータベース対応に修正していきます。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. `PostListServlet` がデータベース操作を使用するように修正されている
2. `list.jsp` がエラーメッセージ表示に対応している
3. 共通ヘッダーのナビゲーションが更新されている
4. 投稿一覧が正常に表示され、データベースの内容と一致している

#### 実装した改善点

1. **効率的なソート**: データベース側でのソート処理
2. **エラーハンドリング**: データベースエラーへの対応
3. **ユーザビリティ**: 詳細ページへのリンク追加
4. **ファイル構造**: JSPファイルの整理
5. **一貫性**: ナビゲーションリンクの統一

> 📝 **ポイント**: この修正により、投稿一覧表示機能がデータベースベースの効率的な処理に変わりました。特に、大量の投稿がある場合でもデータベース側でソートされるため、パフォーマンスが向上します。次のセクションでは、投稿詳細表示機能も同様にデータベース対応していきます。
