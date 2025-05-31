# 6. スタイルとレイアウトの適用

### 6.1 共通レイアウト要素の必要性

これまでの実装では、各 JSP ページに独立した HTML とスタイルを記述していました。これは、共通のヘッダーやフッター、スタイルなどを更新する際に、すべてのファイルを変更する必要があり、メンテナンス性が低下します。

このセクションでは、JSP のインクルード機能を使って、共通のレイアウト要素を作成し、それを各ページで再利用する方法を学びます。これにより、アプリケーション全体の一貫性とメンテナンス性が向上します。

### 6.2 共通ヘッダーとフッターを作成する

#### 共通ディレクトリの作成

まず、共通のヘッダーとフッターを格納するディレクトリを作成します。`src/main/webapp/jsp/common` ディレクトリを作成します。

#### 共通ヘッダーファイルの作成

`src/main/webapp/jsp/common` ディレクトリーに、`header.jsp` ファイルを作成し、以下のコードを入力します。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.time.Year" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>${param.pageTitle != null ? param.pageTitle : 'ブログアプリ'}</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: white;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            min-height: 80vh;
        }
        .header {
            background-color: #4a89dc;
            color: white;
            padding: 1em;
            text-align: center;
            margin-bottom: 20px;
        }
        .nav {
            background-color: #f8f8f8;
            padding: 10px;
            margin-bottom: 20px;
            border-bottom: 1px solid #e7e7e7;
        }
        .nav a {
            margin-right: 15px;
            text-decoration: none;
            color: #337ab7;
        }
        .nav a:hover {
            text-decoration: underline;
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
        .post-count {
            font-size: 0.8em;
            color: #777;
            font-weight: normal;
        }
        .button {
            display: inline-block;
            background-color: #4CAF50;
            color: white;
            padding: 8px 16px;
            text-decoration: none;
            border-radius: 4px;
            border: none;
            cursor: pointer;
        }
        .button:hover {
            background-color: #45a049;
        }
        .button-secondary {
            background-color: #f0f0f0;
            color: #333;
        }
        .button-secondary:hover {
            background-color: #e0e0e0;
        }
        form div {
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
        textarea {
            resize: vertical;
            min-height: 100px;
        }
        .no-posts {
            color: #666;
            font-style: italic;
        }
        a {
            color: #337ab7;
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
        
        /* 基本的なモバイル対応 */
        @media (max-width: 768px) {
            .container {
                padding: 15px;
            }
            .nav a {
                margin: 0 5px;
            }
            .post {
                padding: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>シンプルブログアプリ</h1>
    </div>
    <div class="nav">
        <a href="<%= request.getContextPath() %>/posts">投稿一覧</a>
        <a href="<%= request.getContextPath() %>/jsp/postForm.jsp">新規投稿</a>
    </div>
    <div class="container">
```

> 📝 **ポイント**: ヘッダーファイルには、HTML の開始部分とナビゲーション、そして共通のスタイル定義が含まれています。`${param.pageTitle}` を使用すると、インクルード時にパラメーターとしてページタイトルを指定でき、各ページでタイトルをカスタマイズできます。また、モバイル対応のためのビューポート設定も含めています。

#### 共通フッターファイルの作成

同様に `footer.jsp` ファイルを作成し、以下のコードを入力します。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.time.Year" %>
    </div>
    <div style="text-align: center; padding: 20px; font-size: 12px; color: #777;">
        <p>&copy; <%= Year.now().getValue() %> シンプルブログアプリ - Servlet実践演習</p>
    </div>
</body>
</html>
```

> 📝 **ポイント**: フッターファイルには、コンテナの終了タグとフッター要素、そして HTML の終了タグが含まれています。また、LocalDateTimeに対応してYear.now().getValue()を使って現在の年を動的に表示しています。

### 6.3 投稿フォームページを共通レイアウトに対応させる

次に、既存の `postForm.jsp` を修正して、共通のヘッダーとフッターを使用するようにします。修正のステップは以下の通りです。

#### 共通のヘッダーを使用する

ファイルの先頭部分にある既存の HTML ヘッダー部分を削除し、共通ヘッダーをインクルードします。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="新規投稿" />
</jsp:include>

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
        <button type="submit" class="button">投稿</button>
    </div>
</form>

<div style="margin-top: 20px;">
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary">
        投稿一覧に戻る
    </a>
</div>

<jsp:include page="/jsp/common/footer.jsp" />
```

> 📝 **ポイント**: 共通ヘッダーとフッターを使用することで、スタイル定義の重複がなくなり、一貫したデザインが適用されます。元々定義していたスタイルはすべて共通ヘッダーに含まれているため、削除しても問題ありません。ボタンも共通のCSSクラスを使用するように変更しています。

### 6.4 投稿一覧ページを共通レイアウトに対応させる

同様に、`postList.jsp` も修正します。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.List" %>
<%@ page import="java.time.LocalDateTime" %>
<%@ page import="java.time.format.DateTimeFormatter" %>
<jsp:include page="/jsp/common/header.jsp">
    <jsp:param name="pageTitle" value="投稿一覧" />
</jsp:include>

<h1>投稿一覧 <span class="post-count">(全${totalPosts}件)</span></h1>

<a href="<%= request.getContextPath() %>/jsp/postForm.jsp" class="button">新規投稿</a>

<div style="margin-top: 20px;">
<% 
List<String[]> posts = (List<String[]>) request.getAttribute("posts");
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm");

if (posts != null && !posts.isEmpty()) {
    for (String[] post : posts) {
        String title = post[0];
        String content = post[1];
        String dateTimeStr = post[2];
        
        // LocalDateTimeに変換してフォーマット
        LocalDateTime dateTime = LocalDateTime.parse(dateTimeStr);
%>
    <div class="post">
        <h2><%= title %></h2>
        <p class="post-date">投稿日時: <%= dateTime.format(formatter) %></p>
        <p><%= content.replace("\n", "<br>") %></p>
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

### 6.5 共通レイアウト使用の利点

共通レイアウト要素を使用することで、以下のようなメリットがあります。

1. **コードの重複削減**:\
   同じ HTML 構造や CSS を複数のファイルに記述する必要がなくなります。
2. **メンテナンス性向上**:\
   デザイン変更時に共通ファイルだけを修正すれば、すべてのページに反映されます。
3. **一貫性の確保**:\
   すべてのページで同じヘッダー、フッター、スタイルが使用されるため、アプリケーション全体で一貫した外観になります。
4. **開発効率の向上**:\
   新しいページを作成する際に、共通部分を書く必要がなく、コンテンツ部分に集中できます。
5. **モバイル対応**:\
   共通ヘッダーでメディアクエリを定義することで、アプリケーション全体でモバイル対応が統一されます。

### 6.6 動作確認と次のステップ

修正したアプリケーションの動作を確認します。

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動
3. ブラウザーで投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`
4. 共通ヘッダー、フッター、スタイルが適切に表示されていることを確認
5. ナビゲーションリンクが正しく機能することを確認
6. 異なる画面サイズでのレスポンシブ動作を確認

### 6.7 次のステップでの改善点

モジュール3では、さらに以下の改善を行います：

1. **外部CSSファイルの作成**:\
   現在はヘッダー内にスタイルを記述していますが、外部CSSファイルに分離してより管理しやすくします。
2. **より高度なレイアウト**:\
   CSS Grid や Flexbox を使用したより柔軟なレイアウトを実装します。
3. **JavaScript による動的機能**:\
   フォームバリデーションやインタラクティブな要素を追加します。

これでモジュール 2 の全セクションが完了しました。JSP、フォーム処理、データの表示、共通レイアウト要素の活用など、Web アプリケーション開発の基本的な要素を学びました。次のモジュール 3 では、Post クラスを導入してより構造化されたアプローチでアプリケーションを拡張していきます。

> 📝 **ポイント**: 実際のプロジェクトでは、CSS をさらに外部ファイル ( `style.css` ) に分離するのが一般的です。これは次のモジュールで実装します。また、より大規模なアプリケーションでは、テンプレートエンジンや JSTL などの技術を使って、さらに洗練されたビュー層を構築することもあります。
