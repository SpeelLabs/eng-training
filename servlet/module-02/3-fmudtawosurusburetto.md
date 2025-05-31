# 3. フォームデータを処理するサーブレット

## 3. フォームデータを処理するサーブレット

### 3.1 フォームデータの受け取りと処理

前のセクションで作成したフォームからデータを受け取り、処理するサーブレットを実装します。フォームからのデータは、POST リクエストとして送信されるため、サーブレットでは `doPost` メソッドを実装する必要があります。

このセクションでは、フォームから送信されたタイトルと内容を受け取り、一時的に保存するサーブレットを作成します。

### 3.2 PostCreateServlet クラスの基本構造

まず、サーブレットの基本構造を作成します。`src/main/java/com/example/blog/servlet` に `PostCreateServlet.java` ファイルを作成し、以下のコードを入力します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    // 投稿を一時的に保存するリスト (アプリケーション再起動で消えます)
    private static List<String[]> posts = new ArrayList<>();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // ここにフォームデータを処理するコードを追加していきます
    }
}
```

> 📝 **ポイント**: `doPost` メソッドは POST リクエストを処理するために使用されます。フォームの `method="post"` と対応しています。また、投稿データを一時的に保存するために、`posts` という静的フィールドを用意しています。これはすべてのユーザー間で共有される一時的なデータストアになります。LocalDateTimeをインポートして、現代的な日時処理に対応しています。

### 3.3 フォームデータの処理を実装する

次に、`doPost` メソッド内にフォームデータを処理するコードを追加します。処理の流れは以下の通りです。

#### リクエストパラメータの取得

まず、フォームから送信されたデータを取得します。

```java
// リクエストパラメーターの文字コードを設定
request.setCharacterEncoding("UTF-8");

// フォームから送信されたデータを取得
String title = request.getParameter("title");
String content = request.getParameter("content");
```

> 📝 **ポイント**: `request.getParameter()` メソッドを使用して、フォームから送信されたデータを取得します。フォームのフィールドの `name` 属性の値がパラメーター名となります。また、文字化けを防ぐため、`request.setCharacterEncoding("UTF-8")` を呼び出してリクエストの文字コードを設定しています。

#### 投稿データの保存

取得したデータをリストに追加します。

```java
// 現在の日時を取得
LocalDateTime now = LocalDateTime.now();

// 投稿データをリストに追加（タイトル、内容、日時の順）
posts.add(new String[] { title, content, now.toString() });
```

> 📝 **ポイント**: 現時点では入力値の検証は行わず、HTML フォームの `required` 属性による基本的なクライアント側のバリデーションに依存しています。サーバー側でのより高度な入力検証は発展モジュール 1-A で詳細に実装します。投稿日時もLocalDateTimeを使って記録し、配列に含めています。

#### 完了メッセージの表示

最後に、処理が完了したことをユーザーに知らせるレスポンスを生成します。

```java
// 処理が終わったら、完了メッセージを表示
response.setContentType("text/html;charset=UTF-8");

java.io.PrintWriter out = response.getWriter();

out.println("<!DOCTYPE html>");
out.println("<html><head><title>投稿完了</title>");

out.println("<style>");
out.println("body { font-family: Arial, sans-serif; margin: 20px; }");
out.println("h1 { color: #333; }");
out.println("p { margin: 10px 0; }");
out.println("a { color: #337ab7; text-decoration: none; }");
out.println("a:hover { text-decoration: underline; }");
out.println("</style>");

out.println("</head><body>");
out.println("<h1>投稿が完了しました</h1>");
out.println("<p>タイトル: " + title + "</p>");
out.println("<p>内容: " + content.replace("\n", "<br>") + "</p>");
out.println("<p>投稿日時: " + now.format(java.time.format.DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss")) + "</p>");
out.println("<p><a href=\"jsp/postForm.jsp\">新規投稿へ戻る</a></p>");
out.println("</body></html>");
```

> 📝 **ポイント**: ここでは、サーブレットから直接 HTML を生成しています。これは一時的な実装で、後のセクションでJSPを使用した表示に置き換えます。また、投稿が成功したことを確認できるように、入力内容と投稿日時を表示しています。LocalDateTimeのフォーマット機能を使って、日時を読みやすい形式で表示しています。

### 3.4 投稿データにアクセスするためのメソッドを追加する

他のサーブレットからも投稿データにアクセスできるように、`getPosts()` メソッドを追加します。クラス内に以下のコードを追加します。

```java
// 投稿リストを取得するメソッド (後で他のサーブレットから使用)
public static List<String[]> getPosts() {
    return posts;
}
```

> 📝 **ポイント**: このメソッドは `static` 修飾子を持っており、クラスのインスタンスを作成せずに直接呼び出すことができます。これにより、別のサーブレット ( `PostListServlet` など) からもこのメソッドを使って投稿データにアクセスできるようになります。この方法は一時的なもので、実際のアプリケーションではデータベースなどを使用した永続化が必要です。

### 3.5 完成した PostCreateServlet クラス

上記の処理を組み合わせた完成版の `PostCreateServlet.java` の全体コードは以下の通りです。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/createPost")
public class PostCreateServlet extends HttpServlet {
    
    // 投稿を一時的に保存するリスト (アプリケーション再起動で消えます)
    private static List<String[]> posts = new ArrayList<>();
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // リクエストパラメーターの文字コードを設定
        request.setCharacterEncoding("UTF-8");
        
        // フォームから送信されたデータを取得
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        
        // 現在の日時を取得
        LocalDateTime now = LocalDateTime.now();
        
        // 投稿データをリストに追加（タイトル、内容、日時の順）
        posts.add(new String[] { title, content, now.toString() });
        
        // 処理が終わったら、完了メッセージを表示
        response.setContentType("text/html;charset=UTF-8");
        
        java.io.PrintWriter out = response.getWriter();
        
        out.println("<!DOCTYPE html>");
        out.println("<html><head><title>投稿完了</title>");
        
        out.println("<style>");
        out.println("body { font-family: Arial, sans-serif; margin: 20px; }");
        out.println("h1 { color: #333; }");
        out.println("p { margin: 10px 0; }");
        out.println("a { color: #337ab7; text-decoration: none; }");
        out.println("a:hover { text-decoration: underline; }");
        out.println("</style>");
        
        out.println("</head><body>");
        out.println("<h1>投稿が完了しました</h1>");
        out.println("<p>タイトル: " + title + "</p>");
        out.println("<p>内容: " + content.replace("\n", "<br>") + "</p>");
        
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
        out.println("<p>投稿日時: " + now.format(formatter) + "</p>");
        out.println("<p><a href=\"jsp/postForm.jsp\">新規投稿へ戻る</a></p>");
        out.println("</body></html>");
    }
    
    // 投稿リストを取得するメソッド (後で他のサーブレットから使用)
    public static List<String[]> getPosts() {
        return posts;
    }
}
```

### 3.6 サーブレットの動作確認

作成したサーブレットの動作を確認するには、以下の手順を実行します。

1. プロジェクトをビルド： `mvn clean package`
2. Tomcatサーバーを再起動
3. ブラウザで投稿フォームにアクセス： `http://localhost:8080/blog-app/jsp/postForm.jsp`
4. フォームにタイトルと内容を入力して「投稿」 ボタンをクリック

フォームから投稿を送信すると、「投稿が完了しました」 というメッセージと、入力したタイトル、内容、投稿日時が表示されるはずです。これで、基本的なフォーム処理ができました。

> 📝 **ポイント**: 現時点では、投稿データはアプリケーションのメモリーに一時的に保存されるだけで、サーバーが再起動されると消えてしまいます。後のモジュールでデータベースに永続化する方法を学びます。LocalDateTimeを使用することで、より正確で操作しやすい日時管理が可能になります。
