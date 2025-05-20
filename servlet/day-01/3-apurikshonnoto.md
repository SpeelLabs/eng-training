# 3. アプリケーションの設定と実行

### 3.1 web.xmlの役割と実装

<mark style="color:purple;">`web.xml`</mark> は、Web アプリケーションの設定ファイルです。このファイルに、サーブレットやフィルター、セッション管理などの設定を記述します。最近の Jakarta EE では、アノテーションで多くの設定ができるようになり、<mark style="color:purple;">`web.xml`</mark> は簡素化されています。

<mark style="color:purple;">`web.xml`</mark> の主な役割：

* Web アプリケーションの基本情報の定義
* デフォルトページ (ウェルカムファイル) の指定
* エラーページの設定
* セッションのタイムアウト時間の設定
* その他、アプリケーション全体に関わる設定

この設定ファイルは、<mark style="color:purple;">`src/main/webapp/WEB-INF/web.xml`</mark> に作成されています。 <mark style="color:purple;">`web.xml`</mark> の基本構造と実装を見ていきましょう。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <display-name>Blog Application</display-name>
    
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>

</web-app>
```

{% hint style="info" %}
<mark style="color:purple;">`@WebServlet`</mark> アノテーションを使用している場合、サーブレットのマッピングを <mark style="color:purple;">`web.xml`</mark> に記述する必要はありません。コードと URLのマッピングが一目で分かるため、アノテーション方式の方が分かりやすいでしょう。
{% endhint %}



### 3.2 プロジェクトのビルド

作成したコードをコンパイルして、Web アプリケーションアーカイブ (WAR) を生成します。VS Code でターミナルを開き (\`Ctrl+\`\` または上部メニューの「ターミナル」→「新しいターミナル」)、以下のコマンドを実行します。

```bash
mvn clean package
```

このコマンドは次の処理を実行します。

* <mark style="color:purple;">`clean`</mark>: 以前のビルド結果を削除して、クリーンな状態から始めます
* <mark style="color:purple;">`package`</mark>:ソースコードをコンパイルし、WAR ファイルを作成します

ビルドが成功すると、<mark style="color:purple;">`target/blog-app.war`</mark> ファイルが生成されます。このファイルが Tomcat にデプロイする Web アプリケーションパッケージです。

{% hint style="info" %}
初めて Maven コマンドを実行する場合、必要な依存関係をダウンロードするため時間がかかることがあります。ターミナルに表示される 「BUILD SUCCESS」 メッセージを確認して、ビルドが成功したことを確認しましょう。
{% endhint %}



### 3.3 Tomcat へのデプロイ

生成した WAR ファイルを Tomcat サーバーにデプロイ (配置) します。

1. VS Code 左側のサーバーアイコンをクリック
2. Tomcat サーバーを右クリックし、「Add Deployment」を選択
3. ビルドで生成された <mark style="color:purple;">`target/blog-app.war`</mark> ファイルを選択
4. コンテキストパスを <mark style="color:purple;">`/blog-app`</mark> に設定 (これは Web アプリの URL パスとなります)

{% hint style="info" %}
&#x20;コンテキストパスは、ブラウザーからアクセスする際の URL パスの一部になります。例えば、コンテキストパスが <mark style="color:purple;">`/blog-app`</mark> の場合、アプリケーションへのアクセス URL は <mark style="color:purple;">`http://localhost:8080/blog-app/...`</mark> となります。
{% endhint %}



### 3.4 アプリケーションへのアクセスと動作確認

Tomcat サーバーを起動し、作成したアプリケーションにアクセスします：

1. Tomcat サーバーを右クリックし、「Start」 をクリック (すでに起動している場合は「Restart」)
2. サーバーが起動したら、ブラウザーで以下の URL にアクセス:
   * <mark style="color:purple;">`http://localhost:8080/blog-app/hello`</mark>

「Hello, World!」と表示されたページが表示されれば成功です！これで最初のサーブレットアプリケーションが正常に動作していることを確認できました。

> 📝 **ポイント**: サーバーの起動状態や、起動中のログを確認するには、VS Code の出力タブ（「表示」→「出力」）で「Tomcat」や「Community Server Connector」を選択します。
