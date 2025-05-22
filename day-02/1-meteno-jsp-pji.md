# 1. 初めての JSP ページ作成

### 1.1 JSP の基本概念

JSP (JavaServer Pages) は、HTML コードの中に Java コードを埋め込むことができる技術です。モジュール 1 で学んだサーブレットでは、Java コードから HTML を生成していましたが、JSP では HTML を直接記述でき、その中に必要に応じて Java コードを埋め込むことができます。

このセクションでは、最初の JSP ファイルを作成し、動的なコンテンツを含む Web ページを作成する方法を学びます。

### 1.2 JSP ファイルの作成と基本構造

#### JSP ファイルを作成する

まず、JSP ファイルを格納するディレクトリを作成します。`src/main/webapp/jsp` フォルダを作成し、その中に `hello.jsp` ファイルを作成します。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>My First JSP</title>
</head>
<body>
    <h1>Hello from JSP!</h1>
    <p>これは最初のJSPページです。</p>
</body>
</html>
```
{% endcode %}

> 📝 **ポイント**: JSP ファイルの先頭に記述されている `<%@ page ... %>` はページディレクティブと呼ばれ、JSP ページの基本設定を行います。ここでは、言語として 「java」 、コンテンツタイプとして 「text/html」 、文字エンコーディングとして 「UTF-8」 を指定しています。

#### 動的な要素を追加する

次に、現在の日時を表示する動的な要素を JSP に追加します。`hello.jsp` を以下のように修正します。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>My First JSP</title>
</head>
<body>
    <h1>Hello from JSP!</h1>
    <p>これは最初のJSPページです。</p>
    <p>現在の時刻: <%= new java.util.Date() %></p>
</body>
</html>
```
{% endcode %}

> 📝 **ポイント**: `<%= ... %>` は式 (Expression) タグと呼ばれ、Java の式を評価してその結果を HTML 内に出力します。ここでは、現在の日時 ( `new java.util.Date()` ) が表示されます。

### 1.3 JSP の主要な構文要素

JSP では、以下の特殊なタグを使って Java コードを埋め込むことができます。

#### ディレクティブ: `<%@ ... %>`

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
```
{% endcode %}

ページの設定を指定します。インポートするクラスや文字コードなどを設定できます。

#### 宣言: `<%! ... %>`

```html
<%! private int accessCount = 0; %>
```

クラスレベルの変数やメソッドを宣言します。ページの複数回のリクエストにわたって値が保持されます。

#### スクリプトレット: `<% ... %>`

```html
<% 
  String name = "Guest";
  if (request.getParameter("name") != null) {
    name = request.getParameter("name");
  }
%>
```

Java コードを直接記述します。変数の宣言や条件分岐など、処理ロジックを記述できます。

#### 式: `<%= ... %>`

```jsp
<%= name %>
```

Java の式を評価し、その結果を出力します。変数の値や計算結果などを表示するのに使用します。

### 1.4 クエリーパラメーターを処理する JSP

JSP でもサーブレットと同様に、クエリーパラメーターを受け取って処理することができます。パラメーターを処理する JSP ファイルを作成してみましょう。

`src/main/webapp/jsp` フォルダに `greeting.jsp` ファイルを作成します。

{% code overflow="wrap" %}
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Greeting JSP</title>
</head>
<body>
    <% 
    String name = request.getParameter("name");
    if (name == null || name.isEmpty()) {
        name = "Guest";
    }
    %>
    <h1>こんにちは、<%= name %>さん！</h1>
    <p>JSPからの挨拶です。</p>
    <p><a href="<%= request.getContextPath() %>/hello">Servletのサンプルへ</a></p>
</body>
</html>
```
{% endcode %}

> 📝 **ポイント**: サーブレットと同様に、`request.getParameter("name")` を使用してクエリーパラメーターを取得できます。また、`request.getContextPath()` を使用すると、アプリケーションのコンテキストパス (例: `/blog-app` ) を取得できます。これにより、アプリケーションがどのコンテキストにデプロイされても正しくリンクが機能します。

### 1.5 JSP の動作確認

作成した JSP ページにアクセスして動作を確認します。

#### 基本ページの確認

以下の URL でアクセスします。

{% code overflow="wrap" %}
```html
http://localhost:8080/blog-app/jsp/hello.jsp
```
{% endcode %}

「Hello from JSP!」 というメッセージと現在の日時が表示されるはずです。

#### パラメーターを使ったページの確認

以下の URL でアクセスします。

{% code overflow="wrap" %}
```html
http://localhost:8080/blog-app/jsp/greeting.jsp?name=田中
```
{% endcode %}

「こんにちは、田中さん！」 というメッセージが表示されるはずです。パラメーターーを指定しない場合は、「こんにちは、Guestさん！」 と表示されます。

> 📝 **ポイント**: JSP ファイルは、最初のアクセス時に Tomcat によってサーブレットにコンパイルされます。そのため、初回アクセス時は少し時間がかかることがありますが、2回目以降のアクセスは高速に処理されます。また、JSP ファイルを修正すると、次のアクセス時に自動的に再コンパイルされます。
