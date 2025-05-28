# 2. JDBCによるデータ取得

### 2.1 JDBCとは何か

\*\*JDBC（Java Database Connectivity）\*\*は、JavaプログラムからデータベースにアクセスするためのAPIです。前回作成した接続テストでは「接続できるかどうか」を確認しましたが、今回は実際にデータを取得してみましょう。

#### 2.1.1 JDBCの主要なクラス

JDBCでデータを取得する際に使用する主要なクラスを理解しておきましょう：

| クラス/インターフェース | 役割                 |
| ------------ | ------------------ |
| `Connection` | データベースとの接続を表現      |
| `Statement`  | SQL文を実行するためのオブジェクト |
| `ResultSet`  | クエリの実行結果を表現        |

#### 2.1.2 JDBCの基本的な流れ

データを取得する際の基本的な手順は以下の通りです：

```java
1. データベースに接続
2. SQL文を準備
3. SQL文を実行
4. 結果を処理
5. 接続を閉じる
```

この流れを理解することで、どのようなデータベース操作でも対応できるようになります。

> 📝 **ポイント**: JDBCでは、接続 → SQL準備 → 実行 → 結果処理 → クローズという流れが基本になります。この順序を覚えておくと、どのような操作でも迷わずにコードを書けるようになります。

### 2.2 全データの取得

まず最もシンプルな例として、商品テーブルから全データを取得してみましょう。

`src/main/java/com/example/ProductList.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ProductList {
    
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password"; // 実際のパスワードに変更
    
    public static void main(String[] args) {
        System.out.println("=== 商品一覧 ===");
        
        try {
            // JDBCドライバの読み込み
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // try-with-resources で自動的にリソースを閉じる
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 Statement statement = connection.createStatement();
                 ResultSet resultSet = statement.executeQuery("SELECT * FROM products")) {
                
                // ResultSetから1行ずつデータを取得
                while (resultSet.next()) {
                    int id = resultSet.getInt("id");
                    String name = resultSet.getString("name");
                    int price = resultSet.getInt("price");
                    int stock = resultSet.getInt("stock");
                    
                    System.out.printf("ID: %d, 商品名: %s, 価格: %d円, 在庫: %d個%n",
                                    id, name, price, stock);
                }
                
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("JDBCドライバが見つかりません: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("データベースエラー: " + e.getMessage());
        }
    }
}
```

> 📝 **ポイント**: `try-with-resources` を使用すると、例外が発生してもリソースが確実に閉じられます。これにより、メモリリークやデータベース接続の枯渇を防ぐことができます。

**実行結果例**:

```
=== 商品一覧 ===
ID: 1, 商品名: スマートフォン, 価格: 89000円, 在庫: 25個
ID: 2, 商品名: ノートパソコン, 価格: 120000円, 在庫: 15個
ID: 3, 商品名: Java入門書, 価格: 3500円, 在庫: 30個
ID: 4, 商品名: データベース設計本, 価格: 4200円, 在庫: 20個
ID: 5, 商品名: オフィスチェア, 価格: 25000円, 在庫: 10個
ID: 6, 商品名: デスク, 価格: 35000円, 在庫: 8個
```

### 2.3 ResultSetの理解

`ResultSet`は、SQLクエリの結果を表現するオブジェクトです。データベースから取得した行と列の集合体と考えてください。

#### 2.3.1 ResultSetの基本操作

ResultSetは**カーソル**という概念で動作します。最初はデータの前にカーソルがあり、`next()`を呼ぶことで次の行に移動します。

```java
// カーソルの動き
[開始位置] <- カーソルはここから始まる
[1行目のデータ] <- next() を呼ぶとここに移動
[2行目のデータ] <- もう一度 next() を呼ぶとここに移動
[3行目のデータ]
...
```

> 📝 **ポイント**: ResultSetは「カーソル」で動作します。最初はデータの前にあり、`next()` を呼ぶたびに次の行に移動します。データがなくなると `next()` は `false` を返し、ループが終了します。

**主要なメソッド**：

| メソッド            | 説明     | 戻り値                   |
| --------------- | ------ | --------------------- |
| `next()`        | 次の行に移動 | データがあればtrue、なければfalse |
| `getInt(列名)`    | 整数値を取得 | int型の値                |
| `getString(列名)` | 文字列を取得 | String型の値             |

#### 2.3.2 別のテーブルでの練習

今度はカテゴリテーブルからデータを取得してみましょう。

`src/main/java/com/example/CategoryList.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class CategoryList {
    
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password";
    
    public static void main(String[] args) {
        System.out.println("=== カテゴリ一覧 ===");
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 Statement statement = connection.createStatement();
                 ResultSet resultSet = statement.executeQuery("SELECT * FROM categories")) {
                
                // カテゴリデータの処理
                while (resultSet.next()) {
                    int id = resultSet.getInt("id");
                    String name = resultSet.getString("name");
                    String description = resultSet.getString("description");
                    
                    System.out.printf("ID: %d, カテゴリ名: %s%n", id, name);
                    System.out.printf("    説明: %s%n", description);
                    System.out.println(); // 空行を追加
                }
                
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
}
```

> 📝 **ポイント**: Java 7以降では、複数の例外を `|` で区切って一度にキャッチできます。同じ処理をする場合は、コードが簡潔になります。

**実行結果例**:

```
=== カテゴリ一覧 ===
ID: 1, カテゴリ名: 電子機器
    説明: スマートフォン、パソコンなどの電子製品

ID: 2, カテゴリ名: 書籍
    説明: 本、雑誌、電子書籍など

ID: 3, カテゴリ名: 家具
    説明: テーブル、椅子、収納用品など
```

### 2.4 データの並び替え

SQLの`ORDER BY`句を使って、データを並び替えて取得してみましょう。

`src/main/java/com/example/ProductSorted.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ProductSorted {
    
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password";
    
    public static void main(String[] args) {
        // 価格の安い順に表示
        showProductsByPrice();
    }
    
    public static void showProductsByPrice() {
        System.out.println("=== 価格の安い順 ===");
        
        // ORDER BY price で価格順に並び替え
        String sql = "SELECT * FROM products ORDER BY price";
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 Statement statement = connection.createStatement();
                 ResultSet resultSet = statement.executeQuery(sql)) {
                
                while (resultSet.next()) {
                    String name = resultSet.getString("name");
                    int price = resultSet.getInt("price");
                    
                    System.out.printf("商品名: %s, 価格: %d円%n", name, price);
                }
                
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
}
}
```

> 📝 **ポイント**: ORDER BYを使うことで、Javaプログラム側でソートする必要がなくなります。データベースに並び替えを任せることで、大量のデータでも効率的な処理が可能になります。

**実行結果例**:

```
=== 価格の安い順 ===
商品名: Java入門書, 価格: 3500円
商品名: データベース設計本, 価格: 4200円
商品名: オフィスチェア, 価格: 25000円
商品名: デスク, 価格: 35000円
商品名: スマートフォン, 価格: 89000円
商品名: ノートパソコン, 価格: 120000円
```

> 📝 **ポイント**: ORDER BYを使うことで、Javaプログラム側でソートする必要がなくなります。データベースに並び替えを任せることで、効率的な処理が可能になります。

***

### 演習問題

#### 問題 2-1: 基本的なデータ取得

categories テーブルから全てのカテゴリを取得し、ID順に表示するプログラムを作成してください。

```java
// 期待される出力例
=== カテゴリ一覧（ID順） ===
ID: 1, カテゴリ名: 電子機器
ID: 2, カテゴリ名: 書籍
ID: 3, カテゴリ名: 家具
```

<details>

<summary>ヒント</summary>

* `SELECT * FROM categories ORDER BY id` を使用してID順に並び替え
* `ResultSet` の `getInt("id")` と `getString("name")` でデータを取得
* `while (resultSet.next())` でループ処理

</details>

#### 問題 2-2: データの並び替え

products テーブルから商品名（name）をアルファベット順に表示するプログラムを作成してください。

```java
// 期待される出力例（商品名がアルファベット順になる）
=== 商品名順一覧 ===
商品名: Java入門書
商品名: スマートフォン
商品名: データベース設計本
...
```

<details>

<summary>ヒント</summary>

* `ORDER BY name` で商品名順に並び替え
* 必要な列だけを取得する場合は `SELECT name FROM products` も可能
* 日本語の並び替えも自動的に行われます

</details>

#### 問題 2-3: エラーハンドリング

存在しないテーブル名（例: `unknown_table`）を指定したクエリを実行し、適切なエラーメッセージを表示するプログラムを作成してください。

<details>

<summary>ヒント</summary>

* `SELECT * FROM unknown_table` のようなクエリを実行
* `SQLException` をキャッチして、わかりやすいエラーメッセージを表示
* プログラムが異常終了しないように適切な例外処理を実装

</details>

#### 問題 2-4: 複数テーブルの表示

products テーブルと categories テーブルの両方からデータを取得し、それぞれ別々に表示するプログラムを作成してください。

```java
// 期待される出力例
=== 商品一覧 ===
（商品の一覧を表示）

=== カテゴリ一覧 ===
（カテゴリの一覧を表示）
```

<details>

<summary>ヒント</summary>

* 2つのメソッドを作成して、それぞれで異なるテーブルにアクセス
* `main` メソッドから両方のメソッドを呼び出し
* 同じ接続処理のコードが重複する場合は、適切に整理してみましょう

</details>

#### 問題 2-5: データの集計

products テーブルから全商品数と総在庫数を表示するプログラムを作成してください。

```java
// 期待される出力例
=== 商品統計 ===
全商品数: 6個
総在庫数: 108個
```

<details>

<summary>ヒント</summary>

* `SELECT COUNT(*), SUM(stock) FROM products` で集計
* `COUNT(*)`で行数、`SUM(列名)`で合計を取得
* 集計関数の結果は通常の列と同様に `getInt()` で取得可能

</details>
