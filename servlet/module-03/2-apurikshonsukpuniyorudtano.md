# 2. アプリケーションスコープによる投稿データの管理

### 2.1 スコープについて

前のモジュールでは、`PostCreateServlet` クラスの static フィールドを使用して投稿データを保持していました。この方法ではアプリケーション再起動時にデータが失われ、サーブレット間でデータを共有するには直接クラスを参照する必要がありました。

JSP/サーブレットでは、データを一時的に保存して共有するための 「スコープ」 という仕組みがあります。主なスコープには以下があります。

<table><thead><tr><th width="246.44140625">スコープ名</th><th>説明</th></tr></thead><tbody><tr><td><strong>ページスコープ</strong></td><td>1つの JSP ページ内でのみ有効</td></tr><tr><td><strong>リクエストスコープ</strong></td><td>1つのリクエスト処理内でのみ有効 (これまで使用してきた)</td></tr><tr><td><strong>セッションスコープ</strong></td><td>1人のユーザーのセッション中で有効</td></tr><tr><td><strong>アプリケーションスコープ</strong></td><td>アプリケーション全体で有効</td></tr></tbody></table>

#### アプリケーションスコープの特徴

今回使用するアプリケーションスコープは最も広い範囲を持ち、以下の特徴があります。

* サーバーの起動からシャットダウンまで保持される
* すべてのユーザー、すべてのページで共有される
* 共有データの管理に適している

> 📝 **ポイント**: アプリケーションスコープは `ServletContext` オブジェクトによって管理されます。これは Web アプリケーションの起動時に自動的に作成され、アプリケーション全体で共有されるオブジェクトです。

### 2.2 ServletContextListener によるアプリケーションの初期化

アプリケーションスコープにデータを初期化するために、ServletContextListener を使用します。これは、Web アプリケーションの起動時と終了時に通知を受け取るリスナーインターフェースです。

#### ServletContextListener とは

ServletContextListener は、以下のタイミングで呼び出されるメソッドを持つインターフェースです。

* `contextInitialized()` : アプリケーション起動時に呼び出される
* `contextDestroyed()` : アプリケーション終了時に呼び出される

これらを使うことで、アプリケーションの起動時に必要なデータの初期化を行うことができます。

#### AppInitializer クラスの作成

リスナーを実装するためのクラスを作成します。`src/main/java/com/example/blog/listener` パッケージを作成し、その中に `AppInitializer.java` ファイルを作成します。

```java
package com.example.blog.listener;

import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

@WebListener
public class AppInitializer implements ServletContextListener {
    
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        // ここにアプリケーションの初期化コードを追加します
    }
    
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        // アプリケーション終了時の処理 (必要に応じて実装)
        System.out.println("アプリケーションが終了しました");
    }
}
```

> 📝 **ポイント**: `@WebListener` アノテーションを使用することで、このクラスがリスナーであることをコンテナーに伝えます。別の方法として、`web.xml` ファイルで設定することもできます。

#### 投稿リストの初期化

次に、`contextInitialized` メソッド内でアプリケーションスコープに投稿リストを初期化するコードを追加します。

```java
@Override
public void contextInitialized(ServletContextEvent sce) {
    // アプリケーションスコープを取得
    ServletContext context = sce.getServletContext();
    
    // 投稿を保存するリストを作成（Post オブジェクトのリスト）
    List<Post> posts = new ArrayList<>();
    
    // アプリケーションスコープに保存
    context.setAttribute("posts", posts);
    
    System.out.println("アプリケーションが初期化され、投稿リストが作成されました");
}
```

> 📝 **ポイント**: `ServletContextEvent` から `ServletContext` オブジェクト (アプリケーションスコープ) を取得し、そこにデータを保存します。今回は `List<Post>` を使用して、Post オブジェクトのリストを管理します。

#### サンプル投稿データの追加

アプリケーション起動時に表示するためのサンプルデータを追加します。

```java
@Override
public void contextInitialized(ServletContextEvent sce) {
    // アプリケーションスコープを取得
    ServletContext context = sce.getServletContext();
    
    // 投稿を保存するリストを作成（Post オブジェクトのリスト）
    List<Post> posts = new ArrayList<>();
    
    // サンプル投稿を追加
    Post post1 = new Post(
        "最初の投稿", 
        "これは最初の投稿の内容です。アプリケーション初期化時に作成されたサンプル投稿です。LocalDateTimeを使用して現在の日時が自動的に設定されます。"
    );
    post1.setId(1);
    posts.add(post1);
    
    Post post2 = new Post(
        "Javaプログラミング入門", 
        "Javaはオブジェクト指向プログラミング言語です。このブログではJavaの基本から応用までを解説していきます。ServletとJSPを使用したWebアプリケーション開発の基礎も学んでいきましょう。"
    );
    post2.setId(2);
    posts.add(post2);
    
    Post post3 = new Post(
        "LocalDateTimeの活用",
        "Java 8以降で導入されたLocalDateTimeクラスは、従来のDateクラスよりも直感的で安全な日時処理を提供します。このブログアプリケーションでも、すべての日時処理にLocalDateTimeを使用しています。"
    );
    post3.setId(3);
    posts.add(post3);
    
    // アプリケーションスコープに保存
    context.setAttribute("posts", posts);
    context.setAttribute("nextId", 4); // 次のID値も保存
    
    System.out.println("アプリケーションが初期化され、" + posts.size() + "件のサンプル投稿が作成されました");
}
```

#### 完成した AppInitializer クラス

上記のコードを組み合わせた、完成版の `AppInitializer.java` は以下のようになります。

```java
package com.example.blog.listener;

import java.util.ArrayList;
import java.util.List;

import com.example.blog.model.Post;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

@WebListener
public class AppInitializer implements ServletContextListener {
    
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        // アプリケーションスコープを取得
        ServletContext context = sce.getServletContext();
        
        // 投稿を保存するリストを作成（Post オブジェクトのリスト）
        List<Post> posts = new ArrayList<>();
        
        // サンプル投稿を追加
        Post post1 = new Post(
            "最初の投稿",
            "これは最初の投稿の内容です。アプリケーション初期化時に作成されたサンプル投稿です。LocalDateTimeを使用して現在の日時が自動的に設定されます。"
        );
        post1.setId(1);
        posts.add(post1);
        
        Post post2 = new Post(
            "Javaプログラミング入門",
            "Javaはオブジェクト指向プログラミング言語です。このブログではJavaの基本から応用までを解説していきます。ServletとJSPを使用したWebアプリケーション開発の基礎も学んでいきましょう。"
        );
        post2.setId(2);
        posts.add(post2);
        
        Post post3 = new Post(
            "LocalDateTimeの活用",
            "Java 8以降で導入されたLocalDateTimeクラスは、従来のDateクラスよりも直感的で安全な日時処理を提供します。このブログアプリケーションでも、すべての日時処理にLocalDateTimeを使用しています。"
        );
        post3.setId(3);
        posts.add(post3);
        
        // アプリケーションスコープに保存
        context.setAttribute("posts", posts);
        context.setAttribute("nextId", 4); // 次のID 値も保存
        
        System.out.println("アプリケーションが初期化され、" + posts.size() + "件のサンプル投稿が作成されました");
    }
    
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        // アプリケーション終了時の処理 (必要に応じて実装)
        System.out.println("アプリケーションが終了しました");
    }
}
```

> 📝 **ポイント**: ServletContextListener を使うことで、特定の URL にアクセスすることなく、アプリケーションの起動時に自動的に初期化処理が実行されます。今回は3つのサンプル投稿を作成し、次のIDとして4を設定しています。

### 2.3 アプリケーションスコープの基本的な使い方

アプリケーションスコープの基本的な使い方について学びます。これは後のセクションで PostListServlet や PostCreateServlet を改良する際のベースとなる知識です。

#### アプリケーションスコープからデータを取得する

サーブレットからアプリケーションスコープのデータを取得するには、以下のようにします。

```java
// アプリケーションスコープからデータを取得
ServletContext context = getServletContext();
List<Post> posts = (List<Post>) context.getAttribute("posts");

// または直接
List<Post> posts = (List<Post>) getServletContext().getAttribute("posts");
```

> 📝 **ポイント**: `getAttribute` メソッドは Object 型を返すため、適切な型にキャストする必要があります。今回は `List<Post>` にキャストしています。

#### アプリケーションスコープにデータを設定する

アプリケーションスコープにデータを設定するには、以下のようにします。

```java
// アプリケーションスコープにデータを設定
getServletContext().setAttribute("attributeName", value);
```

#### アプリケーションスコープのデータを削除する

アプリケーションスコープからデータを削除するには、以下のようにします。

```java
// アプリケーションスコープからデータを削除
getServletContext().removeAttribute("attributeName");
```

### 2.4 既存のサーブレットの更新

モジュール2で作成した `PostCreateServlet` を、新しいPost クラスとアプリケーションスコープを使用するように更新する必要があります。

#### PostCreateServlet の改良

`PostCreateServlet.java` を以下のように修正します：

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

> 📝 **ポイント**: 新しい実装では、文字列配列ではなくPostオブジェクトを作成し、アプリケーションスコープから投稿リストを取得しています。また、ID の自動採番機能も実装されています。静的フィールドやgetPosts()メソッドは不要になりました。

### 2.5 アプリケーションスコープと他のスコープの使い分け

各スコープの特性を理解し、適切に使い分けることが重要です。

#### アプリケーションスコープの適切な使用場面

アプリケーションスコープは、アプリケーション全体で共有される静的なデータや、すべてのユーザー間で共有すべきデータを保存するのに適しています。例えば：

* 設定データ
* マスターデータ (カテゴリーリスト、商品リストなど)
* アクセスカウンターなどの共有カウンター

今回の実装では、投稿データをアプリケーションスコープに保存していますが、これは学習のための簡易的な方法です。実際のアプリケーションでは、投稿データのようなユーザーが作成するデータはデータベースに保存するのが一般的です。

#### 他のスコープとの使い分け

| スコープ名            | 説明                                     |
| ---------------- | -------------------------------------- |
| **リクエストスコープ**    | 1つのリクエスト内で必要なデータ (例：検索結果、フォームからの入力値など) |
| **セッションスコープ**    | 特定のユーザーに関連するデータ (例：ログイン情報、ショッピングカートなど) |
| **アプリケーションスコープ** | 全ユーザー共通のデータ (例：設定情報、共有カウンターなど)         |

> 📝 **ポイント**: アプリケーションスコープに大量のデータを保存すると、サーバーのメモリ消費が増加する可能性があります。また、複数のサーバーで動作するクラスター環境では、アプリケーションスコープのデータは自動的に共有されないため注意が必要です。

### 2.6 動作確認

以上の実装で、アプリケーションスコープを使用した基本的なデータ管理の仕組みが構築できました。動作を確認するには、以下の手順を実行します。

#### プロジェクトのビルドとデプロイ

1. プロジェクトをビルド： `mvn clean package`
2. Tomcat サーバーを再起動

#### 初期化の確認

アプリケーションが起動すると、`AppInitializer` の `contextInitialized` メソッドが実行され、サンプル投稿が作成されます。コンソールに以下のメッセージが表示されるはずです。

```
アプリケーションが初期化され、3件のサンプル投稿が作成されました
```

#### 新規投稿機能の確認

1. ブラウザで投稿フォームにアクセス： `http://localhost:8080/blog-app/jsp/postForm.jsp`
2. 新しい投稿を作成
3. 投稿後、一覧ページにリダイレクトされることを確認

> 📝 **ポイント**: ServletContextListener はアプリケーションの起動時に自動的に実行されるため、特定の URL にアクセスする必要はありません。アプリケーションを起動するだけで初期化処理が実行されます。

以上でアプリケーションスコープの基本的な使い方とリスナーによる初期化の仕組みを実装しました。次のセクションでは、この仕組みを利用して投稿一覧表示機能を強化していきます。

> 📝 **注意**: 現時点では、アプリケーションスコープに保存したデータを表示する機能はまだ実装していないため、画面上で確認することはできません。次のセクションで、これらのデータを表示する機能を実装します。
