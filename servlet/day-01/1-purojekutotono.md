# 1. プロジェクト構成と開発の準備

### 1.1 プロジェクト構造の作成

最初に、開発するアプリケーションのためのプロジェクト構造を作成します。VS Code などのエディタを使って、以下の構造になるようにフォルダとファイルを準備しましょう。

```
blog-app/
├─ src/
│   └─ main/
│       ├─ java/              # Javaコードを格納するフォルダ
│       │   └─ com/
│       │       └─ example/
│       │           └─ blog/
│       │               └─ servlet/
│       └─ webapp/           # Webリソースを格納するフォルダ
│           └─ WEB-INF/
│               └─ web.xml    # Webアプリの設定ファイル
├─ pom.xml                    # Mavenプロジェクト設定ファイル
```

> 📝 **ヒント**: VS Code の拡張機能 「Maven for Java」 や 「Project Manager for Java」 を使うと、このようなフォルダ構造を簡単に作成できます。これらの拡張機能をインストールして、新しい Maven プロジェクトを作成すると便利です。

### 1.2 pom.xml ファイルの作成

Maven の設定ファイルとして、プロジェクトのルートフォルダに `pom.xml` ファイルを作成します。このファイルには、プロジェクトの基本情報や依存するライブラリなどを記述します。段階的に必要な要素を見ていきましょう。

まず、XML ヘッダーとプロジェクトの基本情報を定義します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>blog-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
</project>
```

> 📝 **ポイント**: `packaging` 要素を `war` に設定することで、WAR ファイル (Web アプリケーションアーカイブ) 形式でパッケージングすることを指定します。これは Java の Web アプリケーションを配布する標準形式です。

次に、プロジェクトのプロパティを設定します。Java のバージョンや文字コードなどを指定します。

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
</properties>
```

> 📝 **ポイント**: `maven.compiler.source` と `maven.compiler.target` は、コンパイルに使用する Java のバージョンを指定します。JDK 21 を使用していることを前提としていますが、使用する JDK のバージョンに合わせて変更できます。

続いて、プロジェクトの依存関係 (使用するライブラリ) を指定します。

```xml
<dependencies>
    <!-- Jakarta Servlet API -->
    <dependency>
        <groupId>jakarta.servlet</groupId>
        <artifactId>jakarta.servlet-api</artifactId>
        <version>5.0.0</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

> 📝 **ポイント**: `scope` 要素を `provided` に設定しているのは、このライブラリが実行時に Tomcat サーバーから提供されるため、WAR ファイルに含める必要がないことを示しています。これにより、最終的な WAR ファイルのサイズを小さくできます。

最後に、ビルド設定を指定します：

```xml
<build>
    <finalName>blog-app</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.3.2</version>
        </plugin>
    </plugins>
</build>
```

> 📝 **ポイント**: `finalName` 要素は、生成される WAR ファイルの名前を指定します。ここでは `blog-app.war` となります。これにより、URL が `http://localhost:8080/blog-app/...` という形式になります。

これらの要素をまとめた完全な `pom.xml` ファイルは以下の通りです：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>blog-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Jakarta Servlet API -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>5.0.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>blog-app</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### 1.3 Tomcat サーバーの設定

サーブレットを実行するためには、Web アプリケーションサーバーが必要です。ここでは Apache Tomcat を使用します。VS Code に 「Community Server Connector」 拡張機能をインストールし、Tomcat サーバーを設定します。

1. VS Code の左側のメニューから拡張機能アイコンをクリック
2. 検索欄に 「Community Server Connector」 と入力し、インストール (すでにインストールされている場合はスキップ)
3. VS Code 左側メニューのサーバーアイコンをクリック
4. 「+」 ボタンをクリックしてサーバーを追加
5. 「Tomcat 10.x」 を選択
6. Tomcat のインストールディレクトリを選択 (通常は「C:\Program Files\Apache Software Foundation\Tomcat 10.0」など)

> ⚠️ **注意**: Tomcat 10 は JakartaEE 9 以降に対応しています。パッケージ名が `javax.servlet` から `jakarta.servlet` に変更されているため、コードでは `jakarta.servlet` を使用します。
