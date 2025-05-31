# 6. PostDetailServlet のデータベース対応

### 6.1 既存のPostDetailServletの確認

前のセクションで投稿一覧表示機能のデータベース対応が完了しました。このセクションでは、投稿詳細表示機能をデータベース対応に修正していきます。まず、パート1で作成した `PostDetailServlet` の構造を確認しましょう。

#### パート1での実装（復習）

パート1の `PostDetailServlet` は以下のような構造でした：

```java
// パート1の PostDetailServlet（復習）
@WebServlet("/post")
public class PostDetailServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // リクエストパラメーターから ID を取得
        String idParam = request.getParameter("id");
        // ID検証処理...
        
        // アプリケーションスコープから投稿リストを取得
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
        
        // 線形探索で該当投稿を検索
        Post targetPost = null;
        for (Post post : posts) {
            if (post.getId() == postId) {
                targetPost = post;
                break;
            }
        }
        
        // JSPにフォワード
        request.getRequestDispatcher("/jsp/postDetail.jsp").forward(request, response);
    }
}
```

このサーブレットをデータベース対応に修正していきます。

### 6.2 段階的な修正

#### インポート文の修正

まず、データベース操作に必要なクラスをインポートします。`PostDetailServlet.java` の先頭部分を以下のように修正してください：

```java
package com.example.blog.servlet;

import java.io.IOException;

import com.example.blog.model.Post;
import com.example.blog.util.DatabaseUtil; // 追加

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// 以下のインポートは不要になったので削除
// import java.util.List;
```

> 📝 **ポイント**: データベースから直接IDで投稿を取得するため、リスト操作は不要になります。代わりに `DatabaseUtil` をインポートします。

#### ID取得と検証処理の改善

パラメーター取得と検証処理を改善します：

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // リクエストパラメーターから ID を取得
    String idParam = request.getParameter("id");
    
    // ID が指定されていない場合
    if (idParam == null || idParam.trim().isEmpty()) {
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // ID を整数に変換
    int postId;
    try {
        postId = Integer.parseInt(idParam.trim());
        
        // 負の数や0の場合は無効とする
        if (postId <= 0) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
    } catch (NumberFormatException e) {
        // ID が数値でない場合は投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // ここから投稿取得処理を追加していきます
}
```

> 📝 **ポイント**: ID の検証を強化し、負の数や0も無効な値として扱います。これにより、より堅牢なエラーハンドリングが実現できます。

#### データベースからの投稿取得

アプリケーションスコープからの検索処理を、データベースからの取得処理に置き換えます：

```java
// データベースから投稿を取得
Post targetPost = DatabaseUtil.getPostById(postId);

// 投稿が見つからない場合
if (targetPost == null) {
    // エラーメッセージを設定して投稿一覧ページにリダイレクト
    request.getSession().setAttribute("errorMessage", "指定された投稿が見つかりません。");
    response.sendRedirect(request.getContextPath() + "/posts");
    return;
}

// 見つかった投稿をリクエスト属性にセット
request.setAttribute("post", targetPost);

// 詳細表示用 JSP にフォワード
request.getRequestDispatcher("/jsp/post/detail.jsp").forward(request, response);
```

> 📝 **ポイント**: 投稿が見つからない場合、セッションスコープにエラーメッセージを設定します。これにより、リダイレクト後もメッセージを表示できます。JSPファイルのパスも新しい構造に合わせて変更しています。

#### 例外処理の追加

データベース操作に対する例外処理を追加します：

```java
try {
    // データベースから投稿を取得
    Post targetPost = DatabaseUtil.getPostById(postId);
    
    // 投稿が見つからない場合
    if (targetPost == null) {
        request.getSession().setAttribute("errorMessage", "指定された投稿が見つかりません。");
        response.sendRedirect(request.getContextPath() + "/posts");
        return;
    }
    
    // 見つかった投稿をリクエスト属性にセット
    request.setAttribute("post", targetPost);
    
    // 詳細表示用 JSP にフォワード
    request.getRequestDispatcher("/jsp/post/detail.jsp").forward(request, response);
    
} catch (Exception e) {
    // データベースエラーが発生した場合
    request.getSession().setAttribute("errorMessage", "投稿の取得中にエラーが発生しました。");
    response.sendRedirect(request.getContextPath() + "/posts");
    
    // ログに出力（開発時のデバッグ用）
    e.printStackTrace();
}
```

### 6.3 完成したPostDetailServlet

上記の修正を組み合わせた完成版の `PostDetailServlet.java` は以下のようになります：

```java
package com.example.blog.servlet;

import java.io.IOException;

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
        
        // リクエストパラメーターから ID を取得
        String idParam = request.getParameter("id");
        
        // ID が指定されていない場合
        if (idParam == null || idParam.trim().isEmpty()) {
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        // ID を整数に変換
        int postId;
        try {
            postId = Integer.parseInt(idParam.trim());
            
            // 負の数や0の場合は無効とする
            if (postId <= 0) {
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
        } catch (NumberFormatException e) {
            // ID が数値でない場合は投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
            return;
        }
        
        try {
            // データベースから投稿を取得
            Post targetPost = DatabaseUtil.getPostById(postId);
            
            // 投稿が見つからない場合
            if (targetPost == null) {
                request.getSession().setAttribute("errorMessage", "指定された投稿が見つかりません。");
                response.sendRedirect(request.getContextPath() + "/posts");
                return;
            }
            
            // 見つかった投稿をリクエスト属性にセット
            request.setAttribute("post", targetPost);
            
            // 詳細表示用 JSP にフォワード
            request.getRequestDispatcher("/jsp/post/detail.jsp").forward(request, response);
            
        } catch (Exception e) {
            // データベースエラーが発生した場合
            request.getSession().setAttribute("errorMessage", "投稿の取得中にエラーが発生しました。");
            response.sendRedirect(request.getContextPath() + "/posts");
            
            // ログに出力（開発時のデバッグ用）
            e.printStackTrace();
        }
    }
}
```

### 6.4 投稿詳細表示JSPの改善

#### 新しいdetail.jspの作成

`src/main/webapp/jsp/post/` ディレクトリに `detail.jsp` ファイルを作成します。パート1の `postDetail.jsp` を基に改善します：

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
        <strong>投稿日時:</strong> <%= post.getFormattedCreatedAt("yyyy年MM月dd日 HH:mm:ss") %><br>
        <strong>投稿ID:</strong> <%= post.getId() %>
    </p>
    <div class="post-content">
        <%= post.getContent().replace("\n", "<br>") %>
    </div>
</div>

<%-- 将来の機能拡張用（編集・削除リンクなど） --%>
<div class="post-actions" style="margin-top: 30px; padding-top: 20px; border-top: 1px solid #eee;">
    <p style="color: #666; font-size: 0.9em;">
        ※ 編集・削除機能は次のモジュールで実装予定です
    </p>
</div>

<div class="post-navigation" style="margin-top: 30px;">
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">← 投稿一覧に戻る</a>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

#### 投稿一覧でのエラーメッセージ表示

`list.jsp` にセッションからのエラーメッセージ表示機能を追加します。`list.jsp` のエラーメッセージ表示部分を以下のように修正します：

```html
<%-- エラーメッセージの表示（リクエストまたはセッション） --%>
<%
String errorMessage = (String) request.getAttribute("errorMessage");
if (errorMessage == null) {
    errorMessage = (String) session.getAttribute("errorMessage");
    if (errorMessage != null) {
        // セッションから取得した場合は、表示後に削除
        session.removeAttribute("errorMessage");
    }
}

if (errorMessage != null) {
%>
    <div style="color: red; background-color: #ffebee; padding: 10px; margin-bottom: 15px; border: 1px solid #ffcdd2; border-radius: 4px;">
        <strong>エラー:</strong> <%= errorMessage %>
    </div>
<%
}
%>
```

> 📝 **ポイント**: リダイレクト後にエラーメッセージを表示するため、セッションスコープを使用しています。メッセージを表示した後は、セッションから削除して重複表示を防いでいます。

### 6.5 動作確認

#### プロジェクトのビルドと実行

修正したサーブレットの動作を確認します：

1. プロジェクトをビルド：`mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザで投稿一覧ページにアクセス：`http://localhost:8080/blog-app/posts`

#### 正常ケースのテスト

以下の手順で正常な詳細表示をテストします：

1. 投稿一覧ページで任意の投稿タイトルをクリック
2. 投稿詳細ページが正しく表示されることを確認
3. 投稿の内容、日時、IDが正しく表示されることを確認
4. 「投稿一覧に戻る」ボタンで一覧ページに戻れることを確認

#### エラーケースのテスト

エラーハンドリングが正しく動作するかをテストします：

1. **存在しないID**：
   * URL: `http://localhost:8080/blog-app/post?id=999`
   * 一覧ページにリダイレクトされ、エラーメッセージが表示されることを確認
2. **無効なID**：
   * URL: `http://localhost:8080/blog-app/post?id=abc`
   * 一覧ページにリダイレクトされることを確認
3. **IDなし**：
   * URL: `http://localhost:8080/blog-app/post`
   * 一覧ページにリダイレクトされることを確認
4. **負のID**：
   * URL: `http://localhost:8080/blog-app/post?id=-1`
   * 一覧ページにリダイレクトされることを確認

### 6.6 パート1との動作比較

#### 主な変更点

| 項目          | パート1                  | パート2（現在）                 |
| ----------- | --------------------- | ------------------------ |
| **データ検索**   | 線形探索（O(n)）            | データベースインデックス検索（O(log n)） |
| **データソース**  | アプリケーションスコープ          | MySQL データベース             |
| **エラー処理**   | 基本的なリダイレクト            | 詳細なエラーメッセージ表示            |
| **パフォーマンス** | リスト全体を走査              | インデックスによる高速検索            |
| **ファイル構造**  | `/jsp/postDetail.jsp` | `/jsp/post/detail.jsp`   |

#### パフォーマンスの改善

**パート1の処理フロー**:

```
リクエスト → アプリケーションスコープ → 全投稿リスト取得 → 線形探索 → 結果
```

**パート2の処理フロー**:

```
リクエスト → データベース → インデックス検索 → 結果
```

データベースのインデックスにより、投稿数が増えてもほぼ一定時間で検索できます。

### 6.7 次のステップの準備

これで投稿詳細表示機能のデータベース対応が完了しました。次のセクションでは、エラーハンドリングの強化と、アプリケーション全体の動作確認を行います。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. `PostDetailServlet` がデータベース操作を使用するように修正されている
2. `detail.jsp` が新しい構造で作成されている
3. `list.jsp` にセッションからのエラーメッセージ表示機能が追加されている
4. 正常ケースとエラーケースの両方が適切に動作している

#### 実装した改善点

1. **効率的な検索**: データベースインデックスによる高速検索
2. **強化された検証**: ID パラメーターの厳密な検証
3. **エラーメッセージ**: ユーザーフレンドリーなエラー表示
4. **例外処理**: データベースエラーへの適切な対応
5. **ファイル構造**: 整理されたJSP配置

> 📝 **ポイント**: この修正により、投稿詳細表示機能がデータベースベースの効率的な処理に変わりました。特に大量の投稿がある場合でも、インデックスにより高速に特定の投稿を取得できるようになりました。次のセクションでは、アプリケーション全体のエラーハンドリングを強化していきます。
