# 4. PostCreateServlet のデータベース対応

### 4.1 既存のPostCreateServletの確認

前のセクションでデータベース操作の基盤ができました。このセクションでは、パート1で作成した `PostCreateServlet` をデータベース対応に修正していきます。まず、現在のサーブレットの構造を確認しましょう。

#### パート1での実装（復習）

パート1の `PostCreateServlet` は以下のような構造でした：

```java
// パート1の PostCreateServlet（復習）
@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // フォームデータの取得
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title, content);
        
        // アプリケーションスコープから投稿リストを取得して追加
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
        // ... ID設定と保存の処理
        
        // 投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
    }
}
```

このサーブレットをデータベース対応に修正していきます。

### 4.2 段階的な修正

#### インポート文の追加

まず、データベース操作に必要なクラスをインポートします。`PostCreateServlet.java` の先頭部分に以下のインポート文を追加してください：

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
```

> 📝 **ポイント**: `DatabaseUtil` クラスをインポートすることで、前のセクションで作成したデータベース操作メソッドを使用できるようになります。

#### doPostメソッドの基本構造修正

次に、`doPost` メソッドの基本構造を段階的に修正していきます。まず、フォームデータの取得部分は変更しません：

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // リクエストパラメーターの文字コードを設定
    request.setCharacterEncoding("UTF-8");
    
    // フォームから送信されたデータを取得
    String title = request.getParameter("title");
    String content = request.getParameter("content");
    
    // この後の処理を修正していきます
}
```

#### 基本的な入力検証の追加

データベースに保存する前に、基本的な入力検証を追加します：

```java
// 基本的な入力検証
if (title == null || title.trim().isEmpty()) {
    // エラーメッセージを設定してフォームに戻る
    request.setAttribute("errorMessage", "タイトルは必須です。");
    request.setAttribute("title", title);
    request.setAttribute("content", content);
    request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
    return;
}

if (content == null || content.trim().isEmpty()) {
    request.setAttribute("errorMessage", "内容は必須です。");
    request.setAttribute("title", title);
    request.setAttribute("content", content);
    request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
    return;
}
```

> 📝 **ポイント**: エラーが発生した場合、入力値を保持してフォームに戻ることで、ユーザーが再入力する手間を省けます。これはより良いユーザー体験を提供します。

#### データベース保存処理の実装

アプリケーションスコープへの保存処理を、データベース保存処理に置き換えます：

```java
// 新しい投稿オブジェクトを作成
Post newPost = new Post(title.trim(), content.trim());

// データベースに保存
boolean success = DatabaseUtil.savePost(newPost);

if (success) {
    // 保存成功時は投稿一覧ページにリダイレクト
    response.sendRedirect(request.getContextPath() + "/posts");
} else {
    // 保存失敗時はエラーメッセージを表示してフォームに戻る
    request.setAttribute("errorMessage", "投稿の保存に失敗しました。もう一度お試しください。");
    request.setAttribute("title", title);
    request.setAttribute("content", content);
    request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
}
```

> 📝 **ポイント**: データベース操作が失敗した場合のエラーハンドリングを追加しています。`trim()` メソッドで前後の空白を除去してからデータベースに保存しています。

### 4.3 完成したPostCreateServlet

上記の修正を組み合わせた完成版の `PostCreateServlet.java` は以下のようになります：

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
        
        // 基本的な入力検証
        if (title == null || title.trim().isEmpty()) {
            request.setAttribute("errorMessage", "タイトルは必須です。");
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
            return;
        }
        
        if (content == null || content.trim().isEmpty()) {
            request.setAttribute("errorMessage", "内容は必須です。");
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
            return;
        }
        
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title.trim(), content.trim());
        
        // データベースに保存
        boolean success = DatabaseUtil.savePost(newPost);
        
        if (success) {
            // 保存成功時は投稿一覧ページにリダイレクト
            response.sendRedirect(request.getContextPath() + "/posts");
        } else {
            // 保存失敗時はエラーメッセージを表示してフォームに戻る
            request.setAttribute("errorMessage", "投稿の保存に失敗しました。もう一度お試しください。");
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
        }
    }
}
```

### 4.4 投稿作成フォームの改善

#### 既存のJSPの確認

パート1で作成した投稿フォーム（`postForm.jsp`）をパート2用に改善します。まず、ファイルの場所を確認しましょう。

**現在の場所**: `src/main/webapp/jsp/postForm.jsp` **新しい場所**: `src/main/webapp/jsp/post/create.jsp`

#### ディレクトリ構造の整理

まず、JSPファイルを整理するために新しいディレクトリを作成します：

```
src/main/webapp/jsp/
├── post/
│   └── create.jsp (新規作成)
├── common/
│   ├── header.jsp (既存)
│   └── footer.jsp (既存)
└── postForm.jsp (既存、後で削除予定)
```

#### create.jsp の作成

`src/main/webapp/jsp/post/` ディレクトリを作成し、その中に `create.jsp` ファイルを作成します：

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="新規投稿作成" />
</jsp:include>

<h1>新規投稿作成</h1>

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

<form action="<%= request.getContextPath() %>/createPost" method="post">
    <div>
        <label for="title">タイトル:</label>
        <input type="text" id="title" name="title" maxlength="255" required
               value="<%= request.getAttribute("title") != null ? request.getAttribute("title") : "" %>">
        <small style="color: #666;">最大255文字まで入力できます</small>
    </div>
    <div>
        <label for="content">内容:</label>
        <textarea id="content" name="content" rows="10" required 
                  placeholder="投稿の内容を入力してください..."><%= request.getAttribute("content") != null ? request.getAttribute("content") : "" %></textarea>
        <small style="color: #666;">改行も含めて自由に入力してください</small>
    </div>
    <div style="margin-top: 20px;">
        <button type="submit" class="button">投稿する</button>
        <a href="<%= request.getContextPath() %>/posts" class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
    </div>
</form>

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: エラーメッセージの表示機能と、エラー時の入力値保持機能を追加しています。`value` 属性と `textarea` の内容に、エラー時に設定されたリクエスト属性の値を表示することで、ユーザーの入力を保持できます。

### 4.5 動作確認

#### プロジェクトのビルドと実行

修正したサーブレットの動作を確認します：

1. プロジェクトをビルド：`mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザで新しい投稿フォームにアクセス：`http://localhost:8080/blog-app/jsp/post/create.jsp`

#### 正常ケースのテスト

以下の手順で正常な投稿作成をテストします：

1. 投稿フォームにタイトルと内容を入力
2. 「投稿する」ボタンをクリック
3. 投稿一覧ページにリダイレクトされることを確認
4. 新しい投稿がリストに表示されることを確認

#### エラーケースのテスト

エラーハンドリングが正しく動作するかをテストします：

1. **タイトル空白テスト**：
   * タイトルを空白にして投稿
   * エラーメッセージが表示されることを確認
   * 内容フィールドの値が保持されることを確認
2. **内容空白テスト**：
   * 内容を空白にして投稿
   * エラーメッセージが表示されることを確認
   * タイトルフィールドの値が保持されることを確認

#### データベース確認

投稿がデータベースに正しく保存されているかを確認します：

```sql
-- MySQL で確認
USE blog_app;
SELECT * FROM posts ORDER BY created_at DESC LIMIT 5;
```

### 4.6 パート1との動作比較

#### 主な変更点

| 項目            | パート1         | パート2（現在）                |
| ------------- | ------------ | ----------------------- |
| **データ保存先**    | アプリケーションスコープ | MySQL データベース            |
| **ID管理**      | 手動採番         | AUTO\_INCREMENT による自動採番 |
| **データ永続性**    | サーバー再起動で消失   | 永続的に保存                  |
| **エラーハンドリング** | 基本的          | 入力値保持とエラーメッセージ表示        |
| **入力検証**      | HTML5 のみ     | サーバー側検証を追加              |

#### 動作の確認

以下の点を確認して、データベース対応が正しく動作していることを確認しましょう：

1. **投稿の永続化**：サーバーを再起動しても投稿が保持される
2. **自動採番**：投稿IDが自動的に採番される
3. **日時管理**：データベース側で作成日時が自動設定される
4. **エラー処理**：不正な入力時に適切なエラーメッセージが表示される

### 4.7 次のステップの準備

これで投稿作成機能のデータベース対応が完了しました。次のセクションでは、投稿一覧表示機能をデータベース対応に修正していきます。

#### 確認事項

以下の点が正しく完了していることを確認してください：

1. `PostCreateServlet` がデータベース操作を使用するように修正されている
2. エラーハンドリングが実装されている
3. `create.jsp` がエラーメッセージ表示と入力値保持に対応している
4. 投稿作成機能が正常に動作し、データベースに保存されている

#### 実装した改善点

1. **データベース永続化**: アプリケーションスコープからデータベースへの移行
2. **エラーハンドリング**: 保存失敗時の適切な処理
3. **入力検証**: サーバー側での基本的な検証
4. **ユーザー体験**: エラー時の入力値保持
5. **ファイル整理**: JSPファイルの適切な配置

> 📝 **ポイント**: この修正により、投稿作成機能がより堅牢で使いやすくなりました。データベースによる永続化により、真の意味でのWebアプリケーションとしての機能を持つようになりました。次のセクションでは、投稿一覧表示機能も同様にデータベース対応していきます。
