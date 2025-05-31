# Page 2

## 2. プロジェクト設定とMaven依存関係

#### 2.1 Maven依存関係の追加

データベースに接続するために、MySQL Connector/J をプロジェクトに追加します。この JDBC ドライバーにより、Java アプリケーションから MySQL データベースにアクセスできるようになります。

**pom.xml の修正**

パート 1 で作成した `pom.xml` ファイルに、MySQL Connector/J の依存関係を追加します。

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
        
        <!-- MySQL Connector/J -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.33</version>
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

> 📝 **ポイント**: MySQL Connector/J のスコープは `provided` ではなくデフォルト（compile）です。これは、WAR ファイルにドライバーを含める必要があるためです。Servlet API とは異なり、Tomcat にはデフォルトで MySQL ドライバーが含まれていません。

**依存関係の更新**

pom.xml を保存した後、依存関係を更新します。

**VS Code の場合**:

1. VS Code 下部の「Java Projects」パネルを開く
2. プロジェクトを展開し、「Dependencies」で右クリック
3. 「Refresh Dependencies」を選択

**コマンドラインの場合**:

```bash
mvn clean compile
```

#### 2.2 データベース接続設定の準備

**設定ファイルの作成**

データベース接続情報を外部ファイルで管理するため、設定ファイルを作成します。

`src/main/resources/database.properties` ファイルを作成：

```properties
# データベース接続設定
db.url=jdbc:mysql://localhost:3306/blog_app
db.username=root
db.password=your_password
db.driver=com.mysql.cj.jdbc.Driver

# 接続プール設定（将来の拡張用）
db.maxConnections=10
db.connectionTimeout=30000
```

> 📝 **ポイント**:
>
> * `your_password` を実際のMySQLパスワードに変更してください
> * 設定ファイルの分離により、環境ごとに異なる設定を管理できます
> * パスワードなどの機密情報は、本番環境では環境変数で管理するのが理想的です

**.gitignore の更新**

データベースパスワードなどの機密情報をバージョン管理から除外するため、`.gitignore` ファイルを更新します。

```gitignore
# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties

# Eclipse
.metadata
.classpath
.project
.settings/

# IntelliJ IDEA
.idea/
*.iws
*.iml
*.ipr

# VS Code
.vscode/

# データベース設定（機密情報）
src/main/resources/database.properties

# ログファイル
*.log

# OS
.DS_Store
Thumbs.db
```

#### 2.3 データベース接続テスト

**接続テストクラスの作成**

データベース接続が正常に動作するかテストするクラスを作成します。

`src/main/java/com/example/blog/util/DatabaseConnectionTest.java` を作成：

```java
package com.example.blog.util;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class DatabaseConnectionTest {
    
    public static void main(String[] args) {
        testDatabaseConnection();
    }
    
    public static void testDatabaseConnection() {
        System.out.println("=== データベース接続テスト開始 ===");
        
        Properties props = loadDatabaseProperties();
        if (props == null) {
            System.err.println("❌ データベース設定ファイルの読み込みに失敗しました");
            return;
        }
        
        String url = props.getProperty("db.url");
        String username = props.getProperty("db.username");
        String password = props.getProperty("db.password");
        String driver = props.getProperty("db.driver");
        
        try {
            // JDBC ドライバーの読み込み
            Class.forName(driver);
            System.out.println("✅ JDBC ドライバーの読み込み成功");
            
            // データベース接続
            try (Connection connection = DriverManager.getConnection(url, username, password)) {
                System.out.println("✅ データベース接続成功");
                System.out.println("   URL: " + url);
                System.out.println("   ユーザー: " + username);
                
                // 簡単なクエリの実行
                testSimpleQuery(connection);
                
                // posts テーブルの確認
                testPostsTable(connection);
                
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("❌ JDBC ドライバーが見つかりません: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("❌ データベース接続エラー: " + e.getMessage());
            System.err.println("   エラーコード: " + e.getErrorCode());
            System.err.println("   SQL状態: " + e.getSQLState());
        }
        
        System.out.println("=== データベース接続テスト終了 ===");
    }
    
    private static Properties loadDatabaseProperties() {
        Properties props = new Properties();
        try (InputStream input = DatabaseConnectionTest.class
                .getClassLoader()
                .getResourceAsStream("database.properties")) {
            
            if (input == null) {
                System.err.println("database.properties ファイルが見つかりません");
                return null;
            }
            
            props.load(input);
            System.out.println("✅ データベース設定ファイル読み込み成功");
            return props;
            
        } catch (IOException e) {
            System.err.println("設定ファイル読み込みエラー: " + e.getMessage());
            return null;
        }
    }
    
    private static void testSimpleQuery(Connection connection) throws SQLException {
        String sql = "SELECT NOW() AS current_time, VERSION() AS mysql_version";
        
        try (Statement statement = connection.createStatement();
             ResultSet resultSet = statement.executeQuery(sql)) {
            
            if (resultSet.next()) {
                String currentTime = resultSet.getString("current_time");
                String version = resultSet.getString("mysql_version");
                
                System.out.println("✅ クエリ実行成功");
                System.out.println("   現在時刻: " + currentTime);
                System.out.println("   MySQL バージョン: " + version);
            }
        }
    }
    
    private static void testPostsTable(Connection connection) throws SQLException {
        String sql = "SELECT COUNT(*) AS post_count FROM posts";
        
        try (Statement statement = connection.createStatement();
             ResultSet resultSet = statement.executeQuery(sql)) {
            
            if (resultSet.next()) {
                int postCount = resultSet.getInt("post_count");
                System.out.println("✅ posts テーブル確認成功");
                System.out.println("   投稿数: " + postCount + " 件");
            }
        }
    }
}
```

**テストの実行**

作成したテストクラスを実行して、データベース接続が正常に動作することを確認します。

**実行方法**:

1. `DatabaseConnectionTest.java` ファイルを開く
2. `main` メソッドの上にある「Run」ボタンをクリック
3. または、`Ctrl + F5` で実行

**成功時の出力例**:

```
=== データベース接続テスト開始 ===
✅ データベース設定ファイル読み込み成功
✅ JDBC ドライバーの読み込み成功
✅ データベース接続成功
   URL: jdbc:mysql://localhost:3306/blog_app
   ユーザー: root
✅ クエリ実行成功
   現在時刻: 2024-01-15 14:30:25
   MySQL バージョン: 8.0.33
✅ posts テーブル確認成功
   投稿数: 5 件
=== データベース接続テスト終了 ===
```

#### 2.4 トラブルシューティング

**よくあるエラーと対処法**

**エラー1: ClassNotFoundException**

```
JDBC ドライバーが見つかりません: com.mysql.cj.jdbc.Driver
```

**対処法**:

* pom.xml の依存関係を確認
* Maven の依存関係を更新（`mvn clean compile`）
* VS Code で依存関係を再読み込み

**エラー2: SQLException - 接続拒否**

```
データベース接続エラー: Connection refused
```

**対処法**:

* MySQL サーバーが起動しているか確認
* ポート番号（通常3306）が正しいか確認
* ファイアウォールの設定を確認

**エラー3: SQLException - 認証失敗**

```
データベース接続エラー: Access denied for user 'root'@'localhost'
```

**対処法**:

* ユーザー名とパスワードを確認
* database.properties の設定を見直し
* MySQL ユーザーの権限を確認

**エラー4: SQLException - データベースが存在しない**

```
データベース接続エラー: Unknown database 'blog_app'
```

**対処法**:

* データベース名が正しいか確認
* セクション1でデータベースが作成されているか確認
* `SHOW DATABASES;` でデータベース一覧を確認

#### 2.5 設定の確認チェックリスト

次のセクションに進む前に、以下の項目を確認してください：

**Maven 設定**

* \[ ] pom.xml に MySQL Connector/J が追加されている
* \[ ] 依存関係が正常に更新されている
* \[ ] プロジェクトのコンパイルが成功している

**データベース設定**

* \[ ] database.properties ファイルが作成されている
* \[ ] 正しい接続情報が設定されている
* \[ ] パスワードが実際の値に更新されている

**接続テスト**

* \[ ] DatabaseConnectionTest が正常に実行できる
* \[ ] データベース接続が成功している
* \[ ] posts テーブルにアクセスできている
* \[ ] サンプルデータが確認できている

**セキュリティ**

* \[ ] .gitignore でパスワードファイルが除外されている
* \[ ] 機密情報がバージョン管理に含まれていない

> 📝 **ポイント**: この段階で接続テストが成功していることが重要です。問題がある場合は、次のセクションに進む前に必ず解決してください。データベース接続は、以降の全ての機能の基盤となります。
