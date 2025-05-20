# 2. 最初のサーブレット作成

### 2.1 HelloServlet クラスとは

最初のサーブレットとして、シンプルな `HelloServlet` クラスを作成します。このサーブレットはブラウザーに「Hello, World!」というメッセージを表示する、最も基本的な Web アプリケーションの一部です。

このサーブレットの役割：

* `/hello` という URL パスに対応する
* ブラウザーからの GET リクエストを処理する
* 「Hello, World!」というメッセージを含む HTML ページを返す

これによって、サーブレットの基本的な動作を理解することができます。

> 📝 **ポイント**: サーブレットとは、Web リクエストを処理しレスポンスを生成する Java クラスです。コンソールアプリケーションと異なり、ブラウザーからのリクエストに応じて実行されます。

### 2.2 HelloServletクラスの実装

`HelloServlet` クラスには、データを保持するフィールド、リクエストを処理するためのメソッド、そして実際の HTML を生成する処理を実装します。

まずは、サーブレットクラスの基本構造を作成します。`src/main/java/com/example/blog/servlet/` に `HelloServlet.java` ファイルを作成し、以下のコードを入力します。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// URLパターン「/hello」に対応するサーブレットクラスを定義
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    
    // GETリクエストを処理するメソッド
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // ここにHTMLレスポンスを生成するコードを追加
    }
}
```

> 📝 **ポイント**: `@WebServlet` アノテーションを使うと、URL パターンとサーブレットクラスの対応付けを簡単に実装することができます。

作成したサーブレットクラスの重要な部分を詳しく説明します。

1.  **@WebServlet アノテーション**:

    ```java
    @WebServlet("/hello")
    ```

    このアノテーションは、このサーブレットが `/hello` という URL パスに対応することを指定します。つまり、`http://localhost:8080/memo-app/hello` にアクセスすると、このサーブレットが実行されます。
2.  **HttpServlet クラスの継承**:

    ```java
    public class HelloServlet extends HttpServlet {

    }
    ```

    すべてのサーブレットは `HttpServlet` クラスを継承する必要があります。このクラスには、HTTP 通信を処理するための基本的な機能が提供されています。
3.  **doGet メソッドのオーバーライド**:

    ```java
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
            
    }
    ```

    ブラウザからの GET リクエスト (通常の URL アクセス) を処理するためのメソッドです。`request` パラメーターにはリクエスト情報、`response` パラメーターにはレスポンス情報が含まれています。

> 📝 **ポイント**: サーブレットのライフサイクルは以下の通りです。
>
> 1. サーブレットクラスのロード (アプリケーション起動時)
> 2. サーブレットインスタンスの作成 (最初のリクエスト時)
> 3. `init()` メソッドの呼び出し (初期化)
> 4. `service()` メソッドの呼び出し (各リクエスト時) - 内部で doGet や doPost などを呼び出す
> 5. `destroy()` メソッドの呼び出し (終了時)
>
> 通常は`doGet`や`doPost`メソッドの実装だけを行えば、基本的なサーブレットは動作します。

### 2.3 HTMLレスポンスの生成

次に、`doGet` メソッド内にブラウザーに送信するデータの種類を設定します。

```java
// レスポンスのコンテントタイプを HTML に設定
response.setContentType("text/html;charset=UTF-8");
```

> 📝 **ポイント**: `setContentType` メソッドでは、ブラウザーにどのようなデータが送信されるかを伝えています。ここでは「text/html」という形式で、文字コードは「UTF-8」であることを指定しています。これにより、ブラウザーは受信したデータを HTML として解釈し、日本語も正しく表示できます。

最後に、ブラウザーに表示する HTML コンテンツを生成する処理を実装します。

```java
// レスポンス出力用のライターを取得
PrintWriter out = response.getWriter();

// HTMLを出力
out.println("<!DOCTYPE html>");
out.println("<html>");
out.println("<head>");
out.println("<title>Hello Servlet</title>");
out.println("</head>");
out.println("<body>");
out.println("<h1>Hello, World!</h1>");
out.println("<p>これは最初のサーブレットです。</p>");
out.println("</body>");
out.println("</html>");
```

> 📝 **ヒント**: `PrintWriter` の `println` メソッドを使うと、HTML を1行ずつ出力できます。これは通常のコンソール出力 (`System.out.println`) と似ていますが、出力先がブラウザに返される HTTP レスポンスになっている点が異なります。

#### 3.4 完成した HelloServlet.java ファイル

完成した HelloServlet.java ファイルは以下のようになります。

```java
package com.example.blog.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// URLパターン「/hello」に対応するサーブレットクラスを定義
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    
    // GETリクエストを処理するメソッド
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // レスポンスの内容タイプをHTMLに設定
        response.setContentType("text/html;charset=UTF-8");
        
        // レスポンス出力用のライターを取得
        PrintWriter out = response.getWriter();
        
        // HTMLを出力
        out.println("<!DOCTYPE html>");
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Hello Servlet</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello, World!</h1>");
        out.println("<p>これは最初のサーブレットです。</p>");
        out.println("</body>");
        out.println("</html>");
    }
}
```
