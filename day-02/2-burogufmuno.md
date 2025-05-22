# 2. ブログ投稿フォームの作成

### 2.1 HTML フォームの基本概念

HTML フォームは、ユーザーから情報を収集するための重要な手段です。フォームを使うことで、テキスト入力、選択肢の選択、ボタンのクリックなど、様々なユーザー操作を処理できます。

このセクションでは、ブログ投稿用の HTML フォームを作成し、ユーザーがタイトルと内容を入力して送信できるようにします。

### 2.2 投稿フォームの作成

#### 基本的なフォームの構造

まず、シンプルな HTML フォームを含むJSPファイルを作成します。`src/main/webapp/jsp` フォルダに `postForm.jsp` ファイルを作成します。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>新規投稿</title>
</head>
<body>
    <h1>新規ブログ投稿</h1>
    <form action="<%= request.getContextPath() %>/createPost" method="post">
        <div>
            <label for="title">タイトル:</label>
            <input type="text" id="title" name="title" required>
        </div>
        <div style="margin-top: 10px;">
            <label for="content">内容:</label>
            <textarea id="content" name="content" rows="5" cols="40" required></textarea>
        </div>
        <div style="margin-top: 10px;">
            <button type="submit">投稿</button>
        </div>
    </form>
</body>
</html>
```
{% endcode %}

> 📝 **ポイント**: `form` タグの重要な属性:
>
> * `action` : フォームデータを送信する先の URL (サーブレットのパス)
> * `method` : HTTP メソッド (GET または POST) 。POST はデータを URL に表示せず、大量のデータを送信できます。
> * 入力フィールドの `name` 属性は、サーバーでデータを取得する際のパラメーター名になります。

#### フォーム要素について

このフォームには、以下の主要な要素が含まれています。

1.  **フォームタグ**:

    {% code overflow="wrap" %}
    ```html
    <form action="createPost" method="post">
    ```
    {% endcode %}

    フォームの範囲を定義し、データの送信先や送信方法を指定します。
2.  **テキスト入力フィールド**:

    {% code overflow="wrap" %}
    ```html
    <input type="text" id="title" name="title" required>
    ```
    {% endcode %}

    一行のテキストを入力するためのフィールドです。

    * `id` : JavaScript/CSSでの要素の参照に使用
    * `name` : サーバーに送信される際のパラメーター名
    * `required` : 必須入力フィールドを示す
3.  **テキストエリア**:

    {% code overflow="wrap" %}
    ```html
    <textarea id="content" name="content" rows="5" cols="40" required></textarea>
    ```
    {% endcode %}

    複数行のテキストを入力するためのフィールドです。

    * `rows` : 表示する行数
    * `cols` : 表示する列数 (文字数)
4.  **送信ボタン**:

    {% code overflow="wrap" %}
    ```html
    <button type="submit">投稿</button>
    ```
    {% endcode %}

    フォームデータを送信するためのボタンです。

### 2.3 フォームのスタイル改善

基本的なフォームができたら、次に CSS を使ってフォームの見た目を改善します。`postForm.jsp` の `<head>` セクション内に次のスタイルを追加します。

{% code overflow="wrap" %}
```html
<style>
    body {
        font-family: Arial, sans-serif;
        margin: 20px;
    }
    h1 {
        color: #333;
    }
    form {
        max-width: 600px;
        margin-top: 20px;
    }
    div {
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
    button {
        background-color: #4CAF50;
        color: white;
        padding: 10px 15px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }
    button:hover {
        background-color: #45a049;
    }
</style>
```
{% endcode %}

> 📝 **ポイント**: CSS を使用することで、フォームの見た目や使いやすさを大幅に改善できます。ここでは、フォント、色、サイズ、マージン、パディングなどを調整して、より洗練された外観にしています。モジュール 3 では、これらのスタイルを外部 CSS ファイルに移動します。

### 2.4 完成した投稿フォーム

スタイルを適用した完成版の `postForm.jsp` ファイルは次のようになります。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>新規投稿</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #333;
        }
        form {
            max-width: 600px;
            margin-top: 20px;
        }
        div {
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
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <h1>新規ブログ投稿</h1>
    <form action="createPost" method="post">
        <div>
            <label for="title">タイトル:</label>
            <input type="text" id="title" name="title" required>
        </div>
        <div>
            <label for="content">内容:</label>
            <textarea id="content" name="content" rows="5" required></textarea>
        </div>
        <div>
            <button type="submit">投稿</button>
        </div>
    </form>
</body>
</html>
```
{% endcode %}

### 2.5 フォームのアクセスと動作確認

作成したフォームページにアクセスするには、以下の URL を使用します。

{% code overflow="wrap" %}
```html
http://localhost:8080/blog-app/jsp/postForm.jsp
```
{% endcode %}

ブラウザでこの URL にアクセスすると、タイトルと内容を入力するための整形されたフォームが表示されます。

> 📝 **ポイント**: 現時点では、フォームの送信先である `createPost` サーブレットはまだ実装していないため、フォームを送信するとエラーが発生します。次のセクションでこのサーブレットを作成します。また、URL パスが相対パスで指定されているため、現在のページの場所に依存します。後のセクションで絶対パスを使用するように改善します。
