# 4. クエリーパラメーターを受け取るサーブレット

### 4.1 GreetingServlet クラスとは

GreetingServlet はブラウザからのリクエストを処理し、クエリー パラメーターに応じて異なる挨拶メッセージを表示するサーブレットです。例えば 「田中」 という名前をパラメーターで受け取ると、「こんにちは、田中さん！」 というメッセージを表示します。

<mark style="color:purple;">`GreetingServlet`</mark> は次の機能を持ちます：

* <mark style="color:purple;">`/greeting`</mark> という URL パスに対応
* クエリーパラメーター <mark style="color:purple;">`name`</mark> の値を取得 (例：<mark style="color:purple;">`/greeting?name=田中`</mark> )
* パラメーターの値を使って、個別の挨拶メッセージを表示
* パラメーターがない場合はデフォルト値 「Guest」 を使用

これにより、ユーザーごとにカスタマイズされたメッセージを表示することができます。

### 4.2 GreetingServlet クラスの作成

まず、サーブレットクラスの基本構造を作成します。 <mark style="color:purple;">`src/main/java/com/example/blog/servlet/`</mark> に <mark style="color:purple;">`GreetingServlet.java`</mark> ファイルを作成し、以下のコードを入力します。

{% code title="GreetingServlet.java" %}
```java
package com.example.blog.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/greeting")
public class GreetingServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // ここに処理を実装していきます
    }
}
```
{% endcode %}

次に、ブラウザーから送られてきた名前のパラメーターを取得して処理します。\
ユーザーが <mark style="color:purple;">`/greeting?name=田中`</mark> のような URL でアクセスした場合、「田中」という名前を取得し、挨拶文に使用します。パラメーターが指定されていない場合は、「Guest」 というデフォルト名を使用します。

<mark style="color:purple;">`doGet`</mark> メソッド内に以下のコードを追加します。

{% code title="GreetingServlet.java" %}
```java
// URLパラメーター「name」の値を取得
String name = request.getParameter("name");

// nameパラメーターが指定されていない場合のデフォルト値
if (name == null || name.isEmpty()) {
    name = "Guest";
}

// レスポンスの設定
response.setContentType("text/html;charset=UTF-8"); 
```
{% endcode %}

{% hint style="warning" %}
<mark style="color:purple;">`request.getParameter()`</mark> メソッドを使うと、クエリーパラメーターの値を取得できます。このメソッドは指定したパラメーターが存在しない場合は <mark style="color:purple;">`null`</mark> を返すため、必ず存在チェックを行うことが重要です。

例えば <mark style="color:purple;">`http://localhost:8080/blog-app/greeting?name=田中`</mark> という URL の場合、<mark style="color:purple;">`name`</mark> の値は 「田中」 になります。
{% endhint %}



最後に、取得した名前を使用して HTML コンテンツを生成する処理を実装します。

{% code title="GreetingServlet.java" %}
```java
// レスポンス出力用のライターを取得
java.io.PrintWriter out = response.getWriter();

// HTMLを出力
out.println("<!DOCTYPE html>");
out.println("<html>");
out.println("<head>");
out.println("<title>Greeting Servlet</title>");
out.println("</head>");
out.println("<body>");
out.println("<h1>こんにちは、" + name + "さん！</h1>");
out.println("<p>Servletからの挨拶です。</p>");
out.println("<p><a href=\"hello\">Hello Servletへ戻る</a></p>");
out.println("</body>");
out.println("</html>");
```
{% endcode %}

{% hint style="info" %}
取得した名前 (またはデフォルト値) を使って、パーソナライズされた挨拶メッセージを表示します。また、HelloServlet へのリンクも追加して、サーブレット間の移動を可能にします。
{% endhint %}



### 4.3 完成した GreetingServlet.java ファイル

{% code title="" %}
```java
package com.example.blog.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/greeting")
public class GreetingServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // URLパラメーター「name」の値を取得
        String name = request.getParameter("name");

        // nameパラメーターが指定されていない場合のデフォルト値
        if (name == null || name.isEmpty()) {
            name = "Guest";
        }

        // レスポンスの設定
        response.setContentType("text/html;charset=UTF-8");
        
        // レスポンス出力用のライターを取得
        java.io.PrintWriter out = response.getWriter();

        // HTMLを出力
        out.println("<!DOCTYPE html>");
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Greeting Servlet</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>こんにちは、" + name + "さん！</h1>");
        out.println("<p>Servletからの挨拶です。</p>");
        out.println("<p><a href=\"hello\">Hello Servletへ戻る</a></p>");
        out.println("</body>");
        out.println("</html>");
    }
}
```
{% endcode %}

### 4.4 動作確認と検証

ブラウザーで以下のような URL にアクセスして、動作を確認します：

1. パラメーターありの場合:
   * <mark style="color:purple;">`http://localhost:8080/blog-app/greeting?name=田中`</mark>
   * 表示:  「こんにちは、田中さん！」
2. パラメーターなしの場合:
   * <mark style="color:purple;">`http://localhost:8080/blog-app/greeting`</mark>
   * 表示:  「こんにちは、Guestさん！」
3. 別の名前を試す:
   * <mark style="color:purple;">`http://localhost:8080/blog-app/greeting?name=鈴木`</mark>
   * 表示:  「こんにちは、鈴木さん！」

このように、クエリーパラメーターによって動的にコンテンツを変更することができました。これが Web アプリケーションの基本的な動作の一つです。

{% hint style="info" %}
クエリーパラメーターは、<mark style="color:purple;">`?パラメーター名=値`</mark> の形式で指定します。複数のパラメーターは <mark style="color:purple;">`&`</mark> で区切ります (例：<mark style="color:purple;">`?name=山田&age=30`</mark> ) 。日本語などの特殊文字を含む場合は、URL エンコーディングが必要な場合があります。
{% endhint %}
