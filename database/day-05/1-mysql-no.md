# 1. MySQL 接続環境の構築

### 1.1 データベースとテーブルの作成

#### 1.1.1 データベースの作成

Java プログラムから接続するためのデータベースを作成します。

1. **VS Code でMySQLに接続**
   * MySQL Shell for VS Code 拡張機能を使用
   * 左側の MySQL パネルから接続を作成
   * `localhost:3306` に `root` ユーザーで接続
2.  **新しいデータベースを作成**

    ```sql
    -- オンラインショップ用のデータベースを作成
    CREATE DATABASE IF NOT EXISTS online_shop;

    -- 作成したデータベースを選択
    USE online_shop;

    -- データベースが作成されたことを確認
    SHOW DATABASES;
    ```

> 📝 **ポイント**: `IF NOT EXISTS` を使用することで、同じ名前のデータベースが既に存在していてもエラーになりません。

#### 1.1.2 サンプルテーブルの作成

学習用のサンプルテーブルを作成します。

```sql
-- カテゴリテーブル
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    description TEXT
);

-- 商品テーブル
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price INT NOT NULL,
    stock INT DEFAULT 0,
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

**テーブル構造の説明**:

**categories テーブル**:

* `id`: 主キー（自動採番）
* `name`: カテゴリ名（必須）
* `description`: カテゴリの説明

**products テーブル**:

* `id`: 主キー（自動採番）
* `name`: 商品名（必須）
* `price`: 価格（整数、円単位）
* `stock`: 在庫数（デフォルト0）
* `category_id`: カテゴリテーブルとの外部キー
* `created_at`: 作成日時（自動設定）

#### 1.1.3 サンプルデータの投入

学習用のサンプルデータを投入します。

```sql
-- カテゴリデータ
INSERT INTO categories (name, description) VALUES
('電子機器', 'スマートフォン、パソコンなどの電子製品'),
('書籍', '本、雑誌、電子書籍など'),
('家具', 'テーブル、椅子、収納用品など');

-- 商品データ
INSERT INTO products (name, price, stock, category_id) VALUES
('スマートフォン', 89000, 25, 1),
('ノートパソコン', 120000, 15, 1),
('Java入門書', 3500, 30, 2),
('データベース設計本', 4200, 20, 2),
('オフィスチェア', 25000, 10, 3),
('デスク', 35000, 8, 3);
```

#### 1.1.4 データの確認

作成したテーブルとデータを確認します。

```sql
-- テーブル一覧の確認
SHOW TABLES;

-- データの確認
SELECT * FROM categories;
SELECT * FROM products;

-- 結合クエリの例
SELECT 
    p.name AS 商品名,
    p.price AS 価格,
    p.stock AS 在庫,
    c.name AS カテゴリ
FROM products p
JOIN categories c ON p.category_id = c.id
ORDER BY c.name, p.name;
```

> 📝 **ポイント**: データが正しく作成されていることを確認してから、Javaプログラムの作成に進みましょう。

### 1.2 Mavenプロジェクトの作成

#### 1.2.1 新しいプロジェクトの作成

VS Code でMavenプロジェクトを作成します。

1. **プロジェクト作成手順**:
   * VS Code を開く
   * `Ctrl + Shift + P` でコマンドパレットを開く
   * `Java: Create Java Project` を選択
   * `Maven` を選択
   * `maven-archetype-quickstart` を選択
   * Group Id: `com.example`
   * Artifact Id: `mysql-demo`
   * プロジェクトの保存場所を選択
2.  **プロジェクト構造の確認**:

    ```
    mysql-demo/
    ├── pom.xml
    ├── src/
    │   └── main/
    │       └── java/
    │           └── com/
    │               └── example/
    │                   └── App.java
    └── target/
    ```

#### 1.2.2 pom.xmlの設定

MySQL Connector/J を使用するために、pom.xmlを編集します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>mysql-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    
    <dependencies>
        <!-- MySQL Connector/J -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.4.0</version>
        </dependency>
    </dependencies>
</project>
```

#### 1.2.3 依存関係の更新

pom.xml を保存した後、依存関係を更新します。

**方法1**: VS Code の機能を使用

* VS Code 下部の「Java Projects」パネルを開く
* プロジェクトを展開し、「Dependencies」で右クリック
* 「Refresh Dependencies」を選択

**方法2**: ターミナルコマンドを使用

```bash
# プロジェクトディレクトリで実行
mvn clean compile
```

> 📝 **ポイント**: pom.xml を変更した後は、必ず依存関係を更新してください。更新が完了すると、MySQL Connector/J がプロジェクトで使用できるようになります。

### 1.3 簡単な接続テスト

#### 1.3.1 接続テストプログラムの作成

データベースに正常に接続できるかテストするプログラムを作成します。

`src/main/java/com/example/ConnectionTest.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConnectionTest {
    
    // データベース接続情報
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password"; // 実際のパスワードに変更
    
    public static void main(String[] args) {
        System.out.println("=== MySQL接続テスト ===");
        
        try {
            // JDBCドライバの明示的な読み込み
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD)) {
                
                System.out.println("✓ データベースに正常に接続しました！");
                System.out.println("接続先: " + URL);
                
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("✗ JDBCドライバが見つかりません");
            System.err.println("エラー: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("✗ 接続に失敗しました");
            System.err.println("エラー: " + e.getMessage());
        }
        
        System.out.println("=== テスト終了 ===");
    }
}
```

#### 1.3.2 プログラムの実行

作成したプログラムを実行します。

**実行方法**:

1. `ConnectionTest.java` ファイルを開く
2. `main` メソッドの上にある「Run」ボタンをクリック
3. または、`Ctrl + F5` で実行

**成功時の実行結果**:

```
=== MySQL接続テスト ===
✓ データベースに正常に接続しました！
接続先: jdbc:mysql://localhost:3306/online_shop
=== テスト終了 ===
```

**接続情報の設定**:

* `your_password` の部分を実際のMySQLのrootパスワードに変更してください
* 異なるユーザー名を使用する場合は、`USER` の値も変更してください

> 📝 **ポイント**: `try-with-resources` 構文を使用することで、接続が自動的に閉じられます。これにより、リソースの管理が安全に行われます。

### 1.4 トラブルシューティング

#### 1.4.1 よくあるエラーと対処法

**エラー1: 「Access denied for user」**

```
java.sql.SQLException: Access denied for user 'root'@'localhost'
```

**対処法**:

* パスワードが正しいか確認
* MySQLサーバーが起動しているか確認

**エラー2: 「Unknown database」**

```
java.sql.SQLException: Unknown database 'online_shop'
```

**対処法**:

* データベース名が正しいか確認
* データベースが作成されているか確認

**エラー3: 「ClassNotFoundException」**

```
java.lang.ClassNotFoundException: com.mysql.cj.jdbc.Driver
```

**対処法**:

* MySQL Connector/J がpom.xmlに正しく記述されているか確認
* 依存関係が更新されているか確認

**エラー4: 「No suitable driver found」**

```
java.sql.SQLException: No suitable driver found
```

**対処法**:

* MySQL Connector/J がpom.xmlに正しく記述されているか確認
* 依存関係が更新されているか確認

#### 1.4.2 接続情報の確認方法

接続情報が正しいか確認するためのチェックリスト：

1. **MySQLサーバーの起動確認**
   * VS Code の MySQL Shell で接続できるか確認
2.  **データベースの存在確認**

    ```sql
    SHOW DATABASES;
    ```
3.  **ユーザー権限の確認**

    ```sql
    SELECT user, host FROM mysql.user WHERE user = 'root';
    ```

> 📝 **ポイント**: エラーが発生した場合は、エラーメッセージをよく読んで、上記のチェックリストで順番に確認してください。

***

### 演習問題

#### 問題 1-1: データベースとテーブルの作成

新しいデータベース `practice_db` を作成し、以下のテーブルを作成してください。

```sql
-- customersテーブル
-- id (主キー, 自動採番)
-- name (VARCHAR(100), 必須)
-- email (VARCHAR(100), 必須)
-- created_at (TIMESTAMP, デフォルト値は現在時刻)
```

作成後、3件のサンプルデータを投入してください。

<details>

<summary>ヒント</summary>

* `CREATE DATABASE` でデータベースを作成
* `USE` でデータベースを選択
* `CREATE TABLE` でテーブルを作成
* `INSERT INTO` でデータを投入
* 価格は整数型（円単位）で設定

</details>

#### 問題 1-2: 新しいMavenプロジェクトの作成

新しいMavenプロジェクト `database-practice` を作成し、MySQL Connector/J を設定してください。

要件：

* Group Id: `com.practice`
* Artifact Id: `database-practice`
* MySQL Connector/J のバージョン: 8.0.33

<details>

<summary>ヒント</summary>

* VS Code のコマンドパレットから「Java: Create Java Project」を使用
* pom.xml にMySQL Connector/J の dependency を追加
* 依存関係を更新することを忘れずに

</details>

#### 問題 1-3: 接続テストプログラムの作成

問題1-1で作成した `practice_db` データベースに接続するテストプログラムを作成してください。

要件：

* 接続成功時: 「practice\_db に正常に接続しました」と表示
* 接続失敗時: エラーメッセージを表示
* try-with-resources を使用してリソースを適切に管理

<details>

<summary>ヒント</summary>

* `DriverManager.getConnection()` で接続
* 接続URL: `jdbc:mysql://localhost:3306/practice_db`
* try-catch-resources 構文を使用

</details>

#### 問題 1-4: エラー対処の練習

以下の状況を意図的に作成し、エラーメッセージを確認してください：

1. 存在しないデータベース名を指定して接続
2. 間違ったパスワードを指定して接続
3. MySQL Connector/J をpom.xmlから削除して実行

それぞれどのようなエラーメッセージが表示されるか記録してください。

<details>

<summary>ヒント</summary>

* エラーメッセージをよく読んで、原因を理解しましょう
* 元の設定に戻すことを忘れずに

</details>

#### 問題 1-5: 設定の分離

接続情報をソースコードから分離する方法を考えてください。以下のファイルを作成し、設定ファイルから接続情報を読み込むプログラムを作成してください。

`src/main/resources/db.properties`:

```properties
db.url=jdbc:mysql://localhost:3306/practice_db
db.user=root
db.password=your_password
```

<details>

<summary>ヒント</summary>

* `Properties` クラスを使用してプロパティファイルを読み込み
* `getResourceAsStream()` でクラスパスからファイルを読み込み
* try-with-resources でファイルストリームを管理

</details>
