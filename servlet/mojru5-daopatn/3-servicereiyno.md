# 3. Serviceレイヤーの導入

### 3.1 現在のアーキテクチャの分析

セクション2でCRUD操作を完全実装しましたが、さらにアーキテクチャを改善する余地があります。現在のサーブレットには、ビジネスロジックとデータアクセス処理が混在しています。

#### 現在のサーブレットの構造

```java
// PostCreateServlet の例
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) {
    // 1. 入力値の取得と検証（ビジネスロジック）
    String title = request.getParameter("title");
    if (title == null || title.trim().isEmpty()) { ... }
    
    // 2. データアクセス処理
    postDAO.save(title.trim(), content.trim());
    
    // 3. 画面遷移の制御（プレゼンテーション層の責務）
    response.sendRedirect(...);
}
```

この構造には以下の課題があります：

* **責務の混在**: 入力検証、データ処理、画面制御が一つのメソッドに混在
* **テストの困難性**: ビジネスロジックをサーブレットから分離してテストできない
* **再利用性の低下**: ビジネスロジックを他の場所から利用しにくい

> 📝 **ポイント**: Serviceレイヤーは、ビジネスロジックを集約し、サーブレット（プレゼンテーション層）とDAO（データアクセス層）の橋渡しをする役割を担います。

### 3.2 Serviceレイヤーの概念

Serviceレイヤーは、アプリケーションのビジネスロジックを管理する層です。

#### アーキテクチャの進化

```java
// 改善前
[Servlet] → [DAO] → [Database]

// 改善後
[Servlet] → [Service] → [DAO] → [Database]
```

#### Serviceレイヤーの責務

1. **ビジネスルールの実装**: アプリケーション固有のルールや制約
2. **トランザクション管理**: 複数のDAO操作の一貫性確保
3. **データの変換**: DAOからの生データをビジネスオブジェクトに変換
4. **エラーハンドリング**: ビジネス例外の処理

### 3.3 PostServiceクラスの作成

投稿関連のビジネスロジックを管理するPostServiceクラスを作成します。

#### パッケージ構成の準備

`src/main/java/com/example/blog/service` パッケージを作成し、その中に `PostService.java` ファイルを作成します。

#### PostServiceクラスの基本構造

```java
package com.example.blog.service;

import java.sql.SQLException;
import java.util.List;

import com.example.blog.dao.PostDAO;
import com.example.blog.model.Post;

/**
 * 投稿に関するビジネスロジックを管理するサービスクラス
 */
public class PostService {
    
    private PostDAO postDAO;
    
    /**
     * コンストラクタ
     */
    public PostService() {
        this.postDAO = new PostDAO();
    }
    
    // ここにビジネスロジックメソッドを実装していきます
}
```

> 📝 **ポイント**: ServiceクラスはDAOを内部で利用しますが、サーブレットからはServiceのみを呼び出すことで、層の責務が明確になります。

### 3.4 投稿一覧取得機能の実装

PostServiceに投稿一覧取得のビジネスロジックを実装します。

#### getAllPostsメソッドの実装

```java
/**
 * 全ての投稿を取得する
 * 
 * @return 投稿のリスト
 * @throws SQLException データベースエラー
 */
public List<Post> getAllPosts() throws SQLException {
    try {
        List<Post> posts = postDAO.getAllPosts();
        
        // ビジネスロジック: 投稿が存在しない場合のログ出力
        if (posts.isEmpty()) {
            System.out.println("投稿が一件もありません");
        } else {
            System.out.println("投稿を " + posts.size() + " 件取得しました");
        }
        
        return posts;
        
    } catch (SQLException e) {
        System.err.println("投稿一覧取得でエラーが発生しました: " + e.getMessage());
        throw e;
    }
}
```

### 3.5 投稿詳細取得機能の実装

投稿詳細取得にビジネスロジックを追加します。

#### getPostByIdメソッドの実装

```java
/**
 * 指定されたIDの投稿を取得する
 * 
 * @param id 投稿ID
 * @return 投稿データ、見つからない場合はnull
 * @throws SQLException データベースエラー
 * @throws IllegalArgumentException 不正なID
 */
public Post getPostById(int id) throws SQLException {
    // ビジネスロジック: IDの妥当性チェック
    if (id <= 0) {
        throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
    }
    
    try {
        Post post = postDAO.getById(id);
        
        if (post == null) {
            System.out.println("投稿ID " + id + " は見つかりませんでした");
        } else {
            System.out.println("投稿を取得しました: " + post.getTitle());
        }
        
        return post;
        
    } catch (SQLException e) {
        System.err.println("投稿詳細取得でエラーが発生しました (ID: " + id + "): " + e.getMessage());
        throw e;
    }
}
```

> 📝 **ポイント**: IDの妥当性チェックなどのビジネスルールをServiceレイヤーで実装することで、データアクセス前に不正な値を排除できます。

### 3.6 投稿作成機能の実装

投稿作成にビジネスロジックを追加します。

#### createPostメソッドの実装

```java
/**
 * 新しい投稿を作成する
 * 
 * @param title 投稿タイトル
 * @param content 投稿内容
 * @throws SQLException データベースエラー
 * @throws IllegalArgumentException 不正な入力値
 */
public void createPost(String title, String content) throws SQLException {
    // ビジネスロジック: 入力値の検証
    validatePostInput(title, content);
    
    // ビジネスロジック: タイトルと内容のトリミング
    String trimmedTitle = title.trim();
    String trimmedContent = content.trim();
    
    try {
        postDAO.save(trimmedTitle, trimmedContent);
        System.out.println("新しい投稿を作成しました: " + trimmedTitle);
        
    } catch (SQLException e) {
        System.err.println("投稿作成でエラーが発生しました: " + e.getMessage());
        throw e;
    }
}
```

#### 入力値検証の共通メソッド

```java
/**
 * 投稿の入力値を検証する
 * 
 * @param title タイトル
 * @param content 内容
 * @throws IllegalArgumentException 不正な入力値
 */
private void validatePostInput(String title, String content) {
    if (title == null || title.trim().isEmpty()) {
        throw new IllegalArgumentException("タイトルは必須です");
    }
    
    if (content == null || content.trim().isEmpty()) {
        throw new IllegalArgumentException("内容は必須です");
    }
    
    if (title.trim().length() > 200) {
        throw new IllegalArgumentException("タイトルは200文字以内で入力してください");
    }
    
    if (content.trim().length() > 10000) {
        throw new IllegalArgumentException("内容は10000文字以内で入力してください");
    }
}
```

> 📝 **ポイント**: 入力値検証をprivateメソッドに分離することで、更新機能でも同じ検証ロジックを再利用できます。

### 3.7 投稿更新機能の実装

投稿更新にビジネスロジックを追加します。

#### updatePostメソッドの実装

```java
/**
 * 投稿を更新する
 * 
 * @param id 投稿ID
 * @param title 新しいタイトル
 * @param content 新しい内容
 * @throws SQLException データベースエラー
 * @throws IllegalArgumentException 不正な入力値
 */
public void updatePost(int id, String title, String content) throws SQLException {
    // ビジネスロジック: IDの妥当性チェック
    if (id <= 0) {
        throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
    }
    
    // ビジネスロジック: 入力値の検証
    validatePostInput(title, content);
    
    // ビジネスロジック: 投稿の存在確認
    Post existingPost = postDAO.getById(id);
    if (existingPost == null) {
        throw new IllegalArgumentException("投稿ID " + id + " は存在しません");
    }
    
    String trimmedTitle = title.trim();
    String trimmedContent = content.trim();
    
    try {
        postDAO.update(id, trimmedTitle, trimmedContent);
        System.out.println("投稿を更新しました: " + trimmedTitle + " (ID: " + id + ")");
        
    } catch (SQLException e) {
        System.err.println("投稿更新でエラーが発生しました (ID: " + id + "): " + e.getMessage());
        throw e;
    }
}
```

### 3.8 投稿削除機能の実装

投稿削除にビジネスロジックを追加します。

#### deletePostメソッドの実装

```java
/**
 * 投稿を削除する
 * 
 * @param id 投稿ID
 * @throws SQLException データベースエラー
 * @throws IllegalArgumentException 不正なID
 */
public void deletePost(int id) throws SQLException {
    // ビジネスロジック: IDの妥当性チェック
    if (id <= 0) {
        throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
    }
    
    // ビジネスロジック: 投稿の存在確認
    Post existingPost = postDAO.getById(id);
    if (existingPost == null) {
        throw new IllegalArgumentException("投稿ID " + id + " は存在しません");
    }
    
    try {
        postDAO.delete(id);
        System.out.println("投稿を削除しました: " + existingPost.getTitle() + " (ID: " + id + ")");
        
    } catch (SQLException e) {
        System.err.println("投稿削除でエラーが発生しました (ID: " + id + "): " + e.getMessage());
        throw e;
    }
}
```

> 📝 **ポイント**: 削除前に投稿の存在確認を行うことで、より詳細なエラーメッセージを提供できます。また、削除された投稿のタイトルもログに出力することで、運用時の追跡が容易になります。

### 3.9 PostListServletのService対応

PostListServletを、PostServiceを使用するように修正します。

#### PostListServletの修正

```java
public class PostListServlet extends HttpServlet {
    
    private PostService postService = new PostService();
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        try {
            // Serviceレイヤーを使用して投稿を取得
            List<Post> posts = postService.getAllPosts();
            
            request.setAttribute("posts", posts);
            request.setAttribute("totalPosts", posts.size());
                    
            request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
            
        } catch (SQLException e) {
            // エラー処理
            request.setAttribute("posts", new ArrayList<Post>());
            request.setAttribute("totalPosts", 0);
            request.setAttribute("errorMessage", "投稿データの取得中に問題が発生しました。");
            
            request.getRequestDispatcher("/jsp/postList.jsp").forward(request, response);
        }
    }
}
```

#### 改善のポイント

* **単純化**: サーブレットからビジネスロジックが除去され、よりシンプルに
* **責務の明確化**: サーブレットはHTTPリクエスト/レスポンスの処理に集中
* **テスト容易性**: PostServiceを単体でテスト可能

### 3.10 PostCreateServletのService対応

PostCreateServletをPostServiceを使用するように修正します。

#### PostCreateServletの修正

```java
public class PostCreateServlet extends HttpServlet {
    
    private PostService postService = new PostService();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        request.setCharacterEncoding("UTF-8");
        
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        try {
            // Serviceレイヤーを使用して投稿を作成
            postService.createPost(title, content);
            
            response.sendRedirect(request.getContextPath() + "/posts");
            
        } catch (IllegalArgumentException e) {
            // ビジネスロジックエラー（入力値の問題）
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.setAttribute("errorMessage", e.getMessage());
            
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
            
        } catch (SQLException e) {
            // データベースエラー
            request.setAttribute("title", title);
            request.setAttribute("content", content);
            request.setAttribute("errorMessage", "投稿の保存中に問題が発生しました。");
            
            request.getRequestDispatcher("/jsp/postForm.jsp").forward(request, response);
        }
    }
}
```

#### エラーハンドリングの改善

* **IllegalArgumentException**: ビジネスルール違反（具体的なエラーメッセージをユーザーに表示）
* **SQLException**: システムエラー（技術的詳細を隠した汎用メッセージを表示）

> 📝 **ポイント**: 例外の種類により、ユーザーに表示するメッセージを使い分けることで、適切なフィードバックを提供できます。

### 3.11 動作確認

Serviceレイヤーを導入したアプリケーションの動作を確認します。

#### ビルドとテスト

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. 各機能の動作確認

#### テストケース

1. **正常な投稿作成**: 適切なタイトルと内容での投稿作成
2. **入力値エラー**: 空のタイトルや長すぎる内容での投稿作成
3. **投稿一覧表示**: ログにメッセージが出力されることを確認
4. **存在しない投稿へのアクセス**: 適切なエラーハンドリングの確認

#### ログ出力の確認

コンソールに以下のようなログが出力されることを確認してください：

```
投稿を 3 件取得しました
投稿を取得しました: データベース連携の開始
新しい投稿を作成しました: テスト投稿
投稿を更新しました: 更新されたタイトル (ID: 4)
投稿を削除しました: 不要な投稿 (ID: 5)
```

### 3.12 完成したPostServiceクラス

Serviceレイヤーを完全実装したPostServiceクラスの完全なコードは以下のようになります。

```java
package com.example.blog.service;

import java.sql.SQLException;
import java.util.List;

import com.example.blog.dao.PostDAO;
import com.example.blog.model.Post;

public class PostService {
    
    private PostDAO postDAO;
    
    public PostService() {
        this.postDAO = new PostDAO();
    }
    
    public List<Post> getAllPosts() throws SQLException {
        try {
            List<Post> posts = postDAO.getAllPosts();
            
            if (posts.isEmpty()) {
                System.out.println("投稿が一件もありません");
            } else {
                System.out.println("投稿を " + posts.size() + " 件取得しました");
            }
            
            return posts;
            
        } catch (SQLException e) {
            System.err.println("投稿一覧取得でエラーが発生しました: " + e.getMessage());
            throw e;
        }
    }
    
    public Post getPostById(int id) throws SQLException {
        if (id <= 0) {
            throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
        }
        
        try {
            Post post = postDAO.getById(id);
            
            if (post == null) {
                System.out.println("投稿ID " + id + " は見つかりませんでした");
            } else {
                System.out.println("投稿を取得しました: " + post.getTitle());
            }
            
            return post;
            
        } catch (SQLException e) {
            System.err.println("投稿詳細取得でエラーが発生しました (ID: " + id + "): " + e.getMessage());
            throw e;
        }
    }
    
    public void createPost(String title, String content) throws SQLException {
        validatePostInput(title, content);
        
        String trimmedTitle = title.trim();
        String trimmedContent = content.trim();
        
        try {
            postDAO.save(trimmedTitle, trimmedContent);
            System.out.println("新しい投稿を作成しました: " + trimmedTitle);
            
        } catch (SQLException e) {
            System.err.println("投稿作成でエラーが発生しました: " + e.getMessage());
            throw e;
        }
    }
    
    public void updatePost(int id, String title, String content) throws SQLException {
        if (id <= 0) {
            throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
        }
        
        validatePostInput(title, content);
        
        Post existingPost = postDAO.getById(id);
        if (existingPost == null) {
            throw new IllegalArgumentException("投稿ID " + id + " は存在しません");
        }
        
        String trimmedTitle = title.trim();
        String trimmedContent = content.trim();
        
        try {
            postDAO.update(id, trimmedTitle, trimmedContent);
            System.out.println("投稿を更新しました: " + trimmedTitle + " (ID: " + id + ")");
            
        } catch (SQLException e) {
            System.err.println("投稿更新でエラーが発生しました (ID: " + id + "): " + e.getMessage());
            throw e;
        }
    }
    
    public void deletePost(int id) throws SQLException {
        if (id <= 0) {
            throw new IllegalArgumentException("投稿IDは1以上である必要があります: " + id);
        }
        
        Post existingPost = postDAO.getById(id);
        if (existingPost == null) {
            throw new IllegalArgumentException("投稿ID " + id + " は存在しません");
        }
        
        try {
            postDAO.delete(id);
            System.out.println("投稿を削除しました: " + existingPost.getTitle() + " (ID: " + id + ")");
            
        } catch (SQLException e) {
            System.err.println("投稿削除でエラーが発生しました (ID: " + id + "): " + e.getMessage());
            throw e;
        }
    }
    
    private void validatePostInput(String title, String content) {
        if (title == null || title.trim().isEmpty()) {
            throw new IllegalArgumentException("タイトルは必須です");
        }
        
        if (content == null || content.trim().isEmpty()) {
            throw new IllegalArgumentException("内容は必須です");
        }
        
        if (title.trim().length() > 200) {
            throw new IllegalArgumentException("タイトルは200文字以内で入力してください");
        }
        
        if (content.trim().length() > 10000) {
            throw new IllegalArgumentException("内容は10000文字以内で入力してください");
        }
    }
}
```

これでServiceレイヤーの導入が完了しました。アーキテクチャがより明確に分離され、保守性とテスト容易性が大幅に向上しました。次のセクションでは、ファイル構成の整理を行います。
