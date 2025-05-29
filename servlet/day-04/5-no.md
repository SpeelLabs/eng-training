# 5. 新規投稿機能の完成

### 5.1 新規投稿フォームの改善

モジュール 2 で作成した投稿フォームは基本的な機能を持っていましたが、Post クラスを導入したことで、フォームもそれに合わせて改善する必要があります。既存の投稿フォームを見直し、より使いやすくするための改善を行います。

#### 投稿フォームのパスの修正

まず、投稿フォームのパスを修正して、プロジェクト全体で一貫したナビゲーション構造にします。`jsp` ディレクトリー内の `postForm.jsp` を確認し、必要に応じて修正します。

既存のフォームの基本的な構造は以下のようになっています。

```html
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
        <button type="submit" class="button">投稿</button>
    </div>
</form>
```

#### タイトルとページヘッダーの変更

フォームページのタイトルとヘッダーを明確にして、ユーザーにとって分かりやすいページにします。`postForm.jsp` のヘッダー部分を以下のように修正します。

```html
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="新規投稿の作成" />
</jsp:include>

<h1>新規投稿の作成</h1>
```

#### キャンセルボタンの追加

フォームにキャンセルボタンを追加して、ユーザーが投稿作成を中止して一覧ページに戻れるようにします。

```html
<div style="margin-top: 20px;">
    <button type="submit" class="button">投稿</button>
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">キャンセル</a>
</div>
```

#### フォームの入力支援機能追加

ユーザーの入力を支援するための機能を追加します。

```html
<div>
    <label for="title">タイトル:</label>
    <input type="text" id="title" name="title" maxlength="100" required>
    <small style="color: #666;">最大100文字まで入力できます</small>
</div>
<div>
    <label for="content">内容:</label>
    <textarea id="content" name="content" rows="10" required placeholder="投稿の内容を入力してください..."></textarea>
    <small style="color: #666;">改行も含めて自由に入力してください</small>
</div>
```

#### 完成した投稿フォーム

上記の修正を適用した完成版の `postForm.jsp` は以下のようになります。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="新規投稿の作成" />
</jsp:include>

<h1>新規投稿の作成</h1>

<form action="<%= request.getContextPath() %>/createPost" method="post">
    <div>
        <label for="title">タイトル:</label>
        <input type="text" id="title" name="title" maxlength="100" required>
        <small style="color: #666; font-size: 0.9em;">最大100文字まで入力できます</small>
    </div>
    <div>
        <label for="content">内容:</label>
        <textarea id="content" name="content" rows="10" required placeholder="投稿の内容を入力してください..."></textarea>
        <small style="color: #666; font-size: 0.9em;">改行も含めて自由に入力してください</small>
    </div>
    <div style="margin-top: 20px;">
        <button type="submit" class="button">投稿</button>
        <a href="<%= request.getContextPath() %>/posts" class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
    </div>
</form>

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: テキストエリアの行数を増やして ( `rows="10"` ) 、より広い入力欄にすることで、ユーザーが長い内容を入力しやすくなります。また、placeholder属性とmaxlength属性を追加して、入力支援機能を向上させています。

### 5.2 PostCreateServlet の最終確認と改良

セクション 2 で既に `PostCreateServlet` をPost クラスとアプリケーションスコープを使用するように更新しましたが、ここで最終的な動作確認と微調整を行います。

#### 現在の PostCreateServlet の確認

既に更新済みの `PostCreateServlet` の構造を確認します：

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
        
        // 新しい投稿オブジェクトを作成
        Post newPost = new Post(title, content);
        
        // アプリケーションスコープから投稿リストと次の ID を取得
        List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
        Integer nextId = (Integer) getServletContext().getAttribute("nextId");
        
        if (nextId == null) {
            nextId = 1; // 初期値の設定
        }
        
        // 投稿に ID を設定
        newPost.setId(nextId);
        
        // 投稿をリストに追加
        posts.add(newPost);
        
        // 次の ID をインクリメントしてアプリケーションスコープに保存
        getServletContext().setAttribute("nextId", nextId + 1);
        
        // 投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
    }
}
```

#### エラーハンドリングの追加

より堅牢なサーブレットにするため、基本的なエラーハンドリングを追加します。

```java
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
        // エラーの場合はフォームに戻る
        response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
        return;
    }
    
    if (content == null || content.trim().isEmpty()) {
        // エラーの場合はフォームに戻る
        response.sendRedirect(request.getContextPath() + "/jsp/postForm.jsp");
        return;
    }
    
    // 新しい投稿オブジェクトを作成
    Post newPost = new Post(title.trim(), content.trim());
    
    // アプリケーションスコープから投稿リストと次の ID を取得
    List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
    Integer nextId = (Integer) getServletContext().getAttribute("nextId");
    
    if (nextId == null) {
        nextId = 1; // 初期値の設定
    }
    
    // 投稿に ID を設定
    newPost.setId(nextId);
    
    // 投稿をリストに追加
    posts.add(newPost);
    
    // 次の ID をインクリメントしてアプリケーションスコープに保存
    getServletContext().setAttribute("nextId", nextId + 1);
    
    // 投稿一覧ページにリダイレクト
    response.sendRedirect(request.getContextPath() + "/posts");
}
```

> 📝 **ポイント**: この基本的な入力チェックは、HTMLフォームのrequired属性が無効化された場合の対策です。より高度な入力検証は発展モジュール 1-A で詳細に実装します。また、trim()メソッドを使用して、前後の空白文字を除去しています。

### 5.3 投稿作成フローの完成

これまでの実装により、以下のような完全な投稿作成フローが実現されました。

#### 投稿作成の流れ

1. **投稿一覧ページから開始**
   * ユーザーが `/posts` にアクセス
   * 「新規投稿」ボタンをクリック
2. **投稿フォーム入力**
   * `/jsp/postForm.jsp` に移動
   * タイトルと内容を入力
   * 入力支援機能（文字数制限表示、プレースホルダー）の活用
3. **投稿データ送信**
   * 「投稿」ボタンで `/createPost` にPOSTリクエスト
   * サーバー側で入力データを検証
4. **投稿オブジェクト作成**
   * Postクラスのインスタンス作成
   * LocalDateTimeによる現在日時の自動設定
   * 一意IDの自動採番
5. **データ永続化**
   * アプリケーションスコープのリストに追加
   * 次のID値の更新
6. **結果確認**
   * 投稿一覧ページにリダイレクト
   * 新しい投稿が最上部に表示される

#### データフロー図

```
[投稿フォーム] → [PostCreateServlet] → [アプリケーションスコープ] → [投稿一覧]
     ↓                ↓                         ↓                    ↓
[ユーザー入力]    [Post オブジェクト作成]    [List<Post>に追加]    [最新投稿表示]
```

### 5.4 動作確認

以上の実装で、新規投稿機能が完成しました。動作を確認するには、以下の手順を実行します。

#### プロジェクトのビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### 投稿機能の確認

1. ブラウザーで投稿一覧ページにアクセスし、「新規投稿」 ボタンをクリックします。
2. 投稿フォームが表示されるので、タイトルと内容を入力して 「投稿」 ボタンをクリックします。
3. 投稿一覧ページにリダイレクトされ、新しく作成した投稿が最上部に表示されることを確認します。
4. 新しい投稿の「続きを読む」リンクをクリックして、詳細ページが正しく表示されることを確認します。

#### 追加確認項目

1. **投稿数の更新**: 投稿一覧ページの投稿数カウントが正しく増加している
2. **日時の表示**: 投稿日時が現在時刻で正しく記録・表示されている
3. **ID の採番**: 新しい投稿のIDが適切に採番されている
4. **ナビゲーション**: キャンセルボタンで一覧ページに戻れる
5. **入力支援**: プレースホルダーや文字数制限の説明が表示されている

#### エラーケースの確認

1. **空のタイトル**: 空のタイトルで投稿しようとした場合の動作
2. **空の内容**: 空の内容で投稿しようとした場合の動作
3. **ブラウザバック**: 投稿後にブラウザの戻るボタンを使った場合の動作

### 5.5 技術的な成果

新規投稿機能の完成により、以下の技術的な成果が得られました：

#### オブジェクト指向設計の活用

* **Postクラス**: データと操作をカプセル化
* **LocalDateTime**: 現代的な日時処理
* **メソッドの活用**: getFormattedCreatedAt()などの便利メソッド

#### アプリケーションアーキテクチャの改善

* **アプリケーションスコープ**: 適切なデータ共有
* **ServletContextListener**: アプリケーション初期化
* **MVC パターン**: 役割の分離

#### ユーザー体験の向上

* **入力支援**: プレースホルダー、文字数制限表示
* **エラーハンドリング**: 不正入力に対する適切な対応
* **直感的ナビゲーション**: 明確な画面遷移

> 📝 **ポイント**: この実装では、HTML 側の `required` 属性とサーバー側の基本的な入力チェックの組み合わせによるシンプルな検証を使用しています。より堅牢なアプリケーションにするためには、サーバー側での詳細な入力検証も必要ですが、それは発展モジュール 1-A で扱います。また、投稿完了時のメッセージ表示機能などの追加機能も発展モジュールで学習します。
