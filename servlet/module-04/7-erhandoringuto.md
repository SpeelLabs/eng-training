# 7. エラーハンドリングと動作確認

### 7.1 アプリケーション全体のエラーハンドリング強化

これまでのセクションで、各サーブレットをデータベース対応に修正してきました。このセクションでは、アプリケーション全体のエラーハンドリングを強化し、最終的な動作確認を行います。

#### 現在のエラーハンドリング状況

現在実装済みのエラーハンドリング：

| サーブレット                | 実装済みエラー処理               |
| --------------------- | ----------------------- |
| **PostCreateServlet** | 入力検証、データベース保存エラー        |
| **PostListServlet**   | データベース接続エラー             |
| **PostDetailServlet** | ID検証、投稿存在チェック、データベースエラー |

### 7.2 ValidationUtilクラスの作成

入力検証を一元化するためのユーティリティクラスを作成します。`src/main/java/com/example/blog/util` パッケージに `ValidationUtil.java` ファイルを作成します。

#### 基本的な検証メソッドの実装

```java
package com.example.blog.util;

import java.util.ArrayList;
import java.util.List;

/**
 * 入力値検証を行うユーティリティクラス
 */
public class ValidationUtil {
    
    // 投稿タイトルの最大長
    public static final int MAX_TITLE_LENGTH = 255;
    
    // 投稿内容の最大長（TEXT型の制限を考慮）
    public static final int MAX_CONTENT_LENGTH = 65535;
    
    /**
     * 投稿データの検証を行う
     * 
     * @param title 投稿タイトル
     * @param content 投稿内容
     * @return 検証エラーのリスト（エラーがない場合は空のリスト）
     */
    public static List<String> validatePost(String title, String content) {
        List<String> errors = new ArrayList<>();
        
        // タイトルの検証
        errors.addAll(validateTitle(title));
        
        // 内容の検証
        errors.addAll(validateContent(content));
        
        return errors;
    }
    
    /**
     * タイトルの検証
     */
    public static List<String> validateTitle(String title) {
        List<String> errors = new ArrayList<>();
        
        if (title == null || title.trim().isEmpty()) {
            errors.add("タイトルは必須です。");
        } else {
            if (title.trim().length() > MAX_TITLE_LENGTH) {
                errors.add("タイトルは" + MAX_TITLE_LENGTH + "文字以内で入力してください。");
            }
        }
        
        return errors;
    }
    
    /**
     * 内容の検証
     */
    public static List<String> validateContent(String content) {
        List<String> errors = new ArrayList<>();
        
        if (content == null || content.trim().isEmpty()) {
            errors.add("内容は必須です。");
        } else {
            if (content.trim().length() > MAX_CONTENT_LENGTH) {
                errors.add("内容は" + MAX_CONTENT_LENGTH + "文字以内で入力してください。");
            }
        }
        
        return errors;
    }
    
    /**
     * IDパラメーターの検証
     */
    public static Integer validateId(String idParam) {
        if (idParam == null || idParam.trim().isEmpty()) {
            return null;
        }
        
        try {
            int id = Integer.parseInt(idParam.trim());
            return (id > 0) ? id : null;
        } catch (NumberFormatException e) {
            return null;
        }
    }
    
    /**
     * 文字列の安全性チェック（基本的なXSS対策）
     */
    public static String sanitizeString(String input) {
        if (input == null) {
            return null;
        }
        
        return input.replace("<", "&lt;")
                   .replace(">", "&gt;")
                   .replace("\"", "&quot;")
                   .replace("'", "&#x27;")
                   .replace("&", "&amp;");
    }
}
```

> 📝 **ポイント**: 検証ロジックを一元化することで、コードの重複を避け、保守性を向上させています。また、基本的なXSS対策のためのサニタイズ機能も含めています。

### 7.3 PostCreateServletの改善

`ValidationUtil` を使用して `PostCreateServlet` をより堅牢にします：

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // リクエストパラメーターの文字コードを設定
    request.setCharacterEncoding("UTF-8");
    
    // フォームから送信されたデータを取得
    String title = request.getParameter("title");
    String content = request.getParameter("content");
    
    // 入力値検証
    List<String> errors = ValidationUtil.validatePost(title, content);
    
    if (!errors.isEmpty()) {
        // エラーがある場合はフォームに戻る
        request.setAttribute("errors", errors);
        request.setAttribute("title", title);
        request.setAttribute("content", content);
        request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
        return;
    }
    
    // 新しい投稿オブジェクトを作成（サニタイズ済み）
    Post newPost = new Post(
        ValidationUtil.sanitizeString(title.trim()), 
        ValidationUtil.sanitizeString(content.trim())
    );
    
    // データベースに保存
    boolean success = DatabaseUtil.savePost(newPost);
    
    if (success) {
        // 保存成功時は投稿一覧ページにリダイレクト
        response.sendRedirect(request.getContextPath() + "/posts");
    } else {
        // 保存失敗時はエラーメッセージを表示してフォームに戻る
        List<String> dbErrors = new ArrayList<>();
        dbErrors.add("投稿の保存に失敗しました。もう一度お試しください。");
        request.setAttribute("errors", dbErrors);
        request.setAttribute("title", title);
        request.setAttribute("content", content);
        request.getRequestDispatcher("/jsp/post/create.jsp").forward(request, response);
    }
}
```

#### create.jspの改善

複数エラーメッセージに対応するため、`create.jsp` も修正します：

```html
<%-- エラーメッセージの表示（複数対応） --%>
<%
@SuppressWarnings("unchecked")
List<String> errors = (List<String>) request.getAttribute("errors");

if (errors != null && !errors.isEmpty()) {
%>
    <div style="color: red; background-color: #ffebee; padding: 10px; margin-bottom: 15px; border: 1px solid #ffcdd2; border-radius: 4px;">
        <strong>エラー:</strong>
        <ul style="margin: 5px 0 0 20px;">
        <% for (String error : errors) { %>
            <li><%= error %></li>
        <% } %>
        </ul>
    </div>
<%
}

// 旧式のエラーメッセージも対応（後方互換性）
String errorMessage = (String) request.getAttribute("errorMessage");
if (errorMessage != null && (errors == null || errors.isEmpty())) {
%>
    <div style="color: red; background-color: #ffebee; padding: 10px; margin-bottom: 15px; border: 1px solid #ffcdd2; border-radius: 4px;">
        <strong>エラー:</strong> <%= errorMessage %>
    </div>
<%
}
%>
```

### 7.4 PostDetailServletの改善

`ValidationUtil` を使用して `PostDetailServlet` も改善します：

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // ID パラメーターの検証
    Integer postId = ValidationUtil.validateId(request.getParameter("id"));
    
    if (postId == null) {
        // 無効なIDの場合は投稿一覧ページにリダイレクト
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
```

### 7.5 エラーページの作成

アプリケーション全体で使用する共通エラーページを作成します。`src/main/webapp/jsp/common/` ディレクトリに `error.jsp` ファイルを作成します：

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page isErrorPage="true" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="エラー" />
</jsp:include>

<div style="text-align: center; padding: 50px 20px;">
    <h1 style="color: #d32f2f;">申し訳ありません</h1>
    <p style="font-size: 1.1em; margin-bottom: 30px;">
        処理中にエラーが発生しました。
    </p>
    
    <%-- 開発時のエラー詳細表示（本番では削除） --%>
    <% if (exception != null) { %>
        <div style="background-color: #ffebee; padding: 15px; margin: 20px 0; border-radius: 4px; text-align: left;">
            <h3 style="color: #d32f2f;">エラー詳細（開発用）:</h3>
            <p><strong>エラータイプ:</strong> <%= exception.getClass().getSimpleName() %></p>
            <p><strong>エラーメッセージ:</strong> <%= exception.getMessage() %></p>
        </div>
    <% } %>
    
    <div style="margin-top: 30px;">
        <a href="<%= request.getContextPath() %>/posts" class="button">投稿一覧に戻る</a>
        <a href="javascript:history.back()" class="button button-secondary" style="margin-left: 10px;">前のページに戻る</a>
    </div>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

#### web.xmlでのエラーページ設定

`src/main/webapp/WEB-INF/web.xml` にエラーページの設定を追加します：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <display-name>Blog Application</display-name>
    
    <!-- エラーページの設定 -->
    <error-page>
        <exception-type>java.lang.Exception</exception-type>
        <location>/jsp/common/error.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>404</error-code>
        <location>/jsp/common/error.jsp</location>
    </error-page>
    
    <error-page>
        <error-code>500</error-code>
        <location>/jsp/common/error.jsp</location>
    </error-page>
    
    <welcome-file-list>
        <welcome-file>posts</welcome-file>
    </welcome-file-list>

</web-app>
```

> 📝 **ポイント**: ウェルカムファイルを `/posts` に設定することで、アプリケーションのルートURLにアクセスした際に投稿一覧ページが表示されます。

### 7.6 DatabaseUtilの最終調整

`DatabaseUtil` クラスにもエラーハンドリングを強化します：

```java
/**
 * データベース接続のテスト（改善版）
 * 
 * @return 接続成功の場合 true
 */
public static boolean testConnection() {
    try (Connection connection = getConnection()) {
        // 簡単なクエリでデータベースが正常に動作しているかテスト
        try (java.sql.Statement stmt = connection.createStatement();
             java.sql.ResultSet rs = stmt.executeQuery("SELECT 1")) {
            
            System.out.println("✓ データベースに正常に接続し、クエリが実行できました");
            System.out.println("接続URL: " + URL);
            return true;
        }
    } catch (SQLException e) {
        System.err.println("✗ データベース接続または操作に失敗しました");
        System.err.println("エラー: " + e.getMessage());
        return false;
    }
}

/**
 * アプリケーション起動時の初期化チェック
 */
public static boolean initializeApplication() {
    System.out.println("=== アプリケーション初期化チェック ===");
    
    // データベース接続テスト
    if (!testConnection()) {
        return false;
    }
    
    // テーブル存在チェック
    try (Connection connection = getConnection();
         java.sql.Statement stmt = connection.createStatement();
         java.sql.ResultSet rs = stmt.executeQuery("SELECT COUNT(*) FROM posts")) {
        
        if (rs.next()) {
            int count = rs.getInt(1);
            System.out.println("✓ postsテーブルが存在し、" + count + "件のデータがあります");
        }
        
        return true;
        
    } catch (SQLException e) {
        System.err.println("✗ テーブルの確認に失敗しました: " + e.getMessage());
        return false;
    }
}
```

### 7.7 総合的な動作確認

#### 機能テストチェックリスト

以下のテストを順番に実行して、アプリケーション全体が正しく動作することを確認します：

**基本機能テスト**

1. **アプリケーション起動**:
   * Tomcat サーバーを起動
   * `http://localhost:8080/blog-app/` にアクセス
   * 投稿一覧ページが表示されることを確認
2. **投稿一覧表示**:
   * データベースの投稿が新しい順で表示される
   * 投稿数が正しく表示される
   * ナビゲーションリンクが正しく動作する
3. **投稿作成**:
   * 新規投稿フォームにアクセス
   * 正常な投稿を作成し、一覧に表示されることを確認
4. **投稿詳細表示**:
   * 一覧から投稿をクリックして詳細ページを表示
   * 投稿内容が正しく表示される

**エラーハンドリングテスト**

1. **投稿作成のエラーテスト**:
   * 空のタイトルで投稿を試行
   * 空の内容で投稿を試行
   * 長すぎるタイトル（256文字以上）で投稿を試行
2. **投稿詳細のエラーテスト**:
   * 存在しないID: `/post?id=9999`
   * 無効なID: `/post?id=abc`
   * 負のID: `/post?id=-1`
3. **データベースエラーテスト**（オプション）:
   * MySQL サーバーを停止してアクセス
   * エラーページが表示されることを確認

#### パフォーマンステスト

大量データでのテストを行います：

```sql
-- MySQL で大量データを生成（テスト用）
INSERT INTO posts (title, content) 
SELECT 
    CONCAT('テスト投稿 ', n.num) as title,
    CONCAT('これはテスト用の投稿です。投稿番号: ', n.num, 
           '\n\n大量データでのパフォーマンステストを行っています。') as content
FROM (
    SELECT a.N + b.N * 10 + c.N * 100 + 1 AS num
    FROM 
        (SELECT 0 AS N UNION ALL SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL SELECT 6 UNION ALL SELECT 7 UNION ALL SELECT 8 UNION ALL SELECT 9) a,
        (SELECT 0 AS N UNION ALL SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL SELECT 6 UNION ALL SELECT 7 UNION ALL SELECT 8 UNION ALL SELECT 9) b,
        (SELECT 0 AS N UNION ALL SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4) c
) n
LIMIT 100;
```

このクエリで100件のテストデータを生成し、以下を確認します：

* 投稿一覧の表示速度
* 投稿詳細の表示速度
* 新規投稿の保存速度

### 7.8 パート1からパート2への完全移行

#### 移行完了チェック

以下の項目をチェックして、パート1からパート2への移行が完了していることを確認します：

| 項目         | パート1          | パート2         | 移行状況 |
| ---------- | ------------- | ------------ | ---- |
| **データ保存**  | アプリケーションスコープ  | MySQL データベース | ✅ 完了 |
| **投稿作成**   | 手動ID、メモリ保存    | 自動ID、DB保存    | ✅ 完了 |
| **投稿一覧**   | メモリ検索、Javaソート | DB検索、SQLソート  | ✅ 完了 |
| **投稿詳細**   | 線形探索          | インデックス検索     | ✅ 完了 |
| **エラー処理**  | 基本的           | 堅牢なハンドリング    | ✅ 完了 |
| **ファイル構造** | 平坦な構造         | 整理された構造      | ✅ 完了 |

#### 不要ファイルの削除

パート1で使用していた以下のファイルは削除できます：

```
src/main/webapp/jsp/
├── postForm.jsp (削除可能 - create.jspに移行済み)
├── postList.jsp (削除可能 - list.jspに移行済み)
└── postDetail.jsp (削除可能 - detail.jspに移行済み)
```

#### 削除されたパート1の機能

以下のパート1特有の機能は削除されています：

1. **AppInitializer**: ServletContextListener（データベース初期化に変更）
2. **アプリケーションスコープ管理**: 投稿データの管理
3. **手動ID管理**: nextIdの管理機能
4. **Stream API ソート**: データベースソートに置換

### 7.9 モジュール4の完成

これで、モジュール4「データベース連携」が完了しました。

#### 実装された機能

1. **データベース設計**: MySQL テーブル設計とスキーマ作成
2. **JDBC接続**: DatabaseUtil による接続管理
3. **データ永続化**: 投稿データのデータベース保存・取得
4. **サーブレット移行**: 全サーブレットのデータベース対応
5. **エラーハンドリング**: 堅牢なエラー処理とユーザーフィードバック
6. **パフォーマンス向上**: データベースインデックスによる高速検索

#### 技術的成果

| 分野          | 習得技術                        |
| ----------- | --------------------------- |
| **データベース**  | MySQL設計、SQL、インデックス          |
| **Java**    | JDBC、PreparedStatement、例外処理 |
| **アーキテクチャ** | データアクセス層の分離、ユーティリティクラス設計    |
| **セキュリティ**  | SQLインジェクション対策、基本的なXSS対策     |
| **エラー処理**   | 例外処理、ユーザーフレンドリーなエラー表示       |

### 7.10 次のステップ

モジュール4の完了により、本格的なWebアプリケーションの基盤が完成しました。次は以下の選択肢があります：

#### モジュール5: DAOパターンと投稿管理

* DAO パターンの実装
* 投稿の編集・削除機能
* より洗練されたアーキテクチャ

#### モジュール6: コメントと検索機能

* コメント機能の実装
* 検索機能とページング
* パフォーマンス最適化

> 📝 **ポイント**: モジュール4で構築したデータベース連携の基盤は、以降のモジュールでも継続して使用されます。この基盤がしっかりしていることで、新しい機能の追加がスムーズに行えるようになります。データベースによる永続化により、真の意味での実用的なWebアプリケーションが完成しました。
