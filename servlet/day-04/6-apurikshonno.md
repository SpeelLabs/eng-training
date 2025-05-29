# 6. アプリケーションの外観改善

### 6.1 外部 CSS ファイルの作成と活用

これまでは、スタイルをヘッダーのスタイルタグ内に直接記述していましたが、より効率的な管理のために外部 CSS ファイルを作成します。これにより、スタイルの一元管理とキャッシュの恩恵を受けることができます。

#### style.css の作成

まず、`src/main/webapp/css` ディレクトリが存在することを確認し、存在しない場合は作成します。その中に `style.css` ファイルを作成し、これまでヘッダーで定義していたスタイルをコピーします。

```css
/* 全体のスタイル */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f5f5f5;
    line-height: 1.6;
}
.container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    background-color: white;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    min-height: 80vh;
}

/* ヘッダーとナビゲーション */
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

/* 投稿関連のスタイル */
.post {
    border: 1px solid #ddd;
    padding: 15px;
    margin-bottom: 15px;
    border-radius: 5px;
    transition: transform 0.2s;
}
.post:hover {
    transform: translateY(-2px);
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}
.post h2 {
    margin-top: 0;
    color: #444;
}
.post h2 a {
    text-decoration: none;
    color: #333;
}
.post h2 a:hover {
    color: #4a89dc;
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
.post-content {
    line-height: 1.6;
}

/* ボタンのスタイル */
.button {
    display: inline-block;
    background-color: #4CAF50;
    color: white;
    padding: 8px 16px;
    text-decoration: none;
    border-radius: 4px;
    border: none;
    cursor: pointer;
    transition: background-color 0.3s ease;
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

/* フォームのスタイル */
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
    transition: border-color 0.3s ease;
}
input[type="text"]:focus, textarea:focus {
    outline: none;
    border-color: #4a89dc;
    box-shadow: 0 0 5px rgba(74, 137, 220, 0.3);
}
textarea {
    resize: vertical;
    min-height: 100px;
}
small {
    display: block;
    margin-top: 5px;
    color: #666;
    font-size: 0.9em;
}
h1 {
    border-bottom: 1px solid #eee;
    padding-bottom: 10px;
    margin-top: 0;
}

/* 投稿詳細ページのスタイル */
.post-navigation {
    margin-bottom: 20px;
}
.post-detail {
    background-color: white;
    border-radius: 5px;
    padding: 20px;
    border: 1px solid #ddd;
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

/* 一般的なスタイル */
.no-posts {
    color: #666;
    font-style: italic;
    text-align: center;
    padding: 40px 20px;
    background-color: #f9f9f9;
    border-radius: 5px;
}
a {
    color: #337ab7;
    text-decoration: none;
}
a:hover {
    text-decoration: underline;
}

/* フッターのスタイル */
.footer {
    text-align: center;
    padding: 20px;
    margin-top: 30px;
    background-color: #f8f9fa;
    border-top: 1px solid #e7e7e7;
    color: #777;
    font-size: 0.9em;
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
    .button {
        display: block;
        margin: 5px 0;
        text-align: center;
    }
}
```

#### header.jsp の修正

次に、`header.jsp` を修正して、インラインスタイルを削除し、外部 CSS ファイルを参照するようにします。

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.time.Year" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>${param.pageTitle != null ? param.pageTitle : 'ブログアプリ'}</title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/css/style.css">
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

> 📝 **ポイント**: 外部 CSS ファイルを使用することで、スタイルが一元管理され、アプリケーション全体での一貫性が確保されます。また、ブラウザのキャッシュ機能により、パフォーマンスも向上します。`${pageContext.request.contextPath}` はJSPでコンテキストパスを取得する方法の一つです。

### 6.2 投稿一覧ページのレイアウト改善

外部CSSファイルを活用して、投稿一覧ページのレイアウトをさらに改善します。

#### 投稿タイトルのリンク化

投稿一覧ページで、投稿タイトルをクリックして詳細ページに移動できるように `postList.jsp` を修正します。

```html
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
```

#### 投稿一覧の空状態改善

投稿がない場合の表示を改善します。

```html
<% 
if (posts != null && !posts.isEmpty()) {
    for (Post post : posts) {
        // 投稿表示コード
    }
} else {
%>
    <div class="no-posts">
        <h3>まだ投稿がありません</h3>
        <p>最初の投稿を作成してみましょう！</p>
        <a href="<%= request.getContextPath() %>/jsp/postForm.jsp" class="button">新規投稿を作成</a>
    </div>
<% } %>
```

### 6.3 投稿フォームの視覚的改善

投稿フォームの見た目をさらに改善します。

#### フォーカス効果の確認

外部CSSファイルに追加したフォーカス効果が正しく動作することを確認します。フォーム要素にフォーカスしたときに、境界線の色が変わり、ボックスシャドウが表示されるはずです。

#### ボタンの配置改善

ボタンの配置をより見やすく改善します。

```html
<div style="margin-top: 20px; text-align: left;">
    <button type="submit" class="button">投稿する</button>
    <a href="<%= request.getContextPath() %>/posts" class="button button-secondary" style="margin-left: 10px;">キャンセル</a>
</div>
```

### 6.4 アクティブなナビゲーションリンクの強調

現在表示しているページのナビゲーションリンクを強調表示する機能を追加します。

#### CSSクラスの追加

`style.css` に以下のスタイルを追加します。

```css
/* アクティブなナビゲーションリンクの強調 */
.nav a.active {
    background-color: #337ab7;
    color: white;
    padding: 5px 10px;
    border-radius: 3px;
}
```

#### JSPでのアクティブ状態の管理

各JSPページでアクティブなナビゲーションを示すために、header.jspを少し修正します。

```html
<div class="nav">
    <a href="<%= request.getContextPath() %>/posts" 
       class="<%= request.getRequestURI().contains("/posts") || request.getRequestURI().contains("/post") ? "active" : "" %>">
       投稿一覧</a>
    <a href="<%= request.getContextPath() %>/jsp/postForm.jsp" 
       class="<%= request.getRequestURI().contains("/postForm") ? "active" : "" %>">
       新規投稿</a>
</div>
```

### 6.5 レスポンシブデザインの確認

外部CSSファイルに追加したモバイル対応のスタイルが正しく動作することを確認します。

#### ブラウザでの確認

1. ブラウザの開発者ツールを使用してモバイル表示に切り替え
2. 画面幅を変更して、レスポンシブデザインが適用されることを確認
3. ボタンやナビゲーションが小さな画面でも使いやすいことを確認

### 6.6 パフォーマンスの向上

外部CSSファイルの使用により、以下のパフォーマンス向上が期待できます。

#### キャッシュの効果

1. **初回アクセス**: CSSファイルがダウンロードされてブラウザにキャッシュ
2. **2回目以降**: キャッシュされたCSSファイルが使用され、高速表示
3. **複数ページ**: 同じCSSファイルを複数ページで共有するため、効率的

#### ファイルサイズの削減

1. **重複の排除**: 各JSPで重複していたスタイル定義を一元化
2. **圧縮の可能性**: 本番環境ではCSSファイルを圧縮して配信可能
3. **CDN対応**: 将来的にCDNを使用した配信も可能

### 6.7 動作確認

以上の実装で、アプリケーションの外観が大幅に改善されました。動作を確認するには、以下の手順を実行します。

#### プロジェクトのビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### ブラウザでの確認

1. ブラウザーで投稿一覧ページにアクセス： `http://localhost:8080/blog-app/posts`
2. 外部CSSファイルが正しく読み込まれていることを確認
3. 各ページの見た目が統一されていることを確認
4. ナビゲーションのアクティブ状態が正しく表示されることを確認
5. フォーム要素のフォーカス効果が動作することを確認
6. レスポンシブデザインが適用されることを確認

#### 機能面での確認

1. **投稿一覧**: タイトルクリックで詳細ページに移動
2. **投稿詳細**: 適切なレイアウトで表示
3. **投稿フォーム**: 視覚的に改善されたフォーム
4. **ナビゲーション**: アクティブ状態の表示
5. **空状態**: 投稿がない場合の適切な表示

> 📝 **ポイント**: CSS の変更は、ブラウザーにキャッシュされることがあります。変更が反映されない場合は、ブラウザのキャッシュをクリアするか、「ハードリロード」 ( `Ctrl`+`Shift`+`R` または `Shift`+`F5`) を実行してみてください。

### 6.8 まとめ

このセクションでは、アプリケーションの外観を大幅に改善しました。主な改善点：

#### 技術的改善

* **外部CSSファイル**: スタイルの一元管理と保守性向上
* **レスポンシブデザイン**: 様々な画面サイズへの対応
* **パフォーマンス向上**: キャッシュ効果による高速化

#### ユーザー体験の向上

* **視覚的改善**: 統一されたデザインと洗練された外観
* **インタラクション**: ホバー効果やフォーカス効果
* **ナビゲーション**: アクティブ状態の明確化

#### 保守性の向上

* **コードの整理**: スタイルとロジックの分離
* **再利用性**: 共通スタイルの活用
* **拡張性**: 新しいページ追加時の効率化

これでモジュール 3 の全セクションが完了しました。パート 1 の目標である 「 MVC パターンの基礎を実装したブログアプリケーション」 が完成しました。次のステップでは、パート 2 に進んでデータベースを使用したデータの永続化を学んだり、発展モジュールに取り組んでより高度な機能を追加したりすることができます。
