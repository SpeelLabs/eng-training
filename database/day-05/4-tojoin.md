# 4. 条件検索とJOIN

### 4.1 WHERE句による条件検索

これまでの学習で基本的なデータ取得とPreparedStatementを学びました。今回は、条件を指定してデータを絞り込む方法と、複数のテーブルを結合する方法を学習します。

#### 4.1.1 引数を使った検索メソッド

条件を引数として受け取り、様々な検索ができるメソッドを作成してみましょう。

`src/main/java/com/example/ProductSearch.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class ProductSearch {
    
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password";
    
    public static void main(String[] args) {
        // 様々な条件で商品を検索
        searchByMaxPrice(10000);
        
        System.out.println();
        
        searchByMinStock(15);
        
        System.out.println();
        
        searchByPriceRange(20000, 100000);
    }
    
    // 指定価格以下の商品を検索
    public static void searchByMaxPrice(int maxPrice) {
        System.out.println("=== " + maxPrice + "円以下の商品 ===");
        
        String sql = "SELECT * FROM products WHERE price <= ? ORDER BY price";
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 PreparedStatement pstmt = connection.prepareStatement(sql)) {
                
                pstmt.setInt(1, maxPrice);
                
                try (ResultSet resultSet = pstmt.executeQuery()) {
                    displayResults(resultSet);
                }
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
    
    // 指定在庫数未満の商品を検索
    public static void searchByMinStock(int minStock) {
        System.out.println("=== 在庫" + minStock + "個未満の商品 ===");
        
        String sql = "SELECT * FROM products WHERE stock < ? ORDER BY stock";
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 PreparedStatement pstmt = connection.prepareStatement(sql)) {
                
                pstmt.setInt(1, minStock);
                
                try (ResultSet resultSet = pstmt.executeQuery()) {
                    displayResults(resultSet);
                }
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
    
    // 価格範囲での検索
    public static void searchByPriceRange(int minPrice, int maxPrice) {
        System.out.println("=== " + minPrice + "円～" + maxPrice + "円の商品 ===");
        
        String sql = "SELECT * FROM products WHERE price >= ? AND price <= ? ORDER BY price";
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 PreparedStatement pstmt = connection.prepareStatement(sql)) {
                
                pstmt.setInt(1, minPrice);
                pstmt.setInt(2, maxPrice);
                
                try (ResultSet resultSet = pstmt.executeQuery()) {
                    displayResults(resultSet);
                }
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
    
    // 結果表示用の共通メソッド
    private static void displayResults(ResultSet resultSet) throws SQLException {
        boolean found = false;
        
        while (resultSet.next()) {
            String name = resultSet.getString("name");
            int price = resultSet.getInt("price");
            int stock = resultSet.getInt("stock");
            
            System.out.printf("商品名: %s, 価格: %d円, 在庫: %d個%n", 
                            name, price, stock);
            found = true;
        }
        
        if (!found) {
            System.out.println("該当する商品が見つかりませんでした。");
        }
    }
}
```

**実行結果例**:

```
=== 10000円以下の商品 ===
商品名: Java入門書, 価格: 3500円, 在庫: 30個
商品名: データベース設計本, 価格: 4200円, 在庫: 20個

=== 在庫15個未満の商品 ===
商品名: デスク, 価格: 35000円, 在庫: 8個
商品名: オフィスチェア, 価格: 25000円, 在庫: 10個

=== 20000円～100000円の商品 ===
商品名: オフィスチェア, 価格: 25000円, 在庫: 10個
商品名: デスク, 価格: 35000円, 在庫: 8個
商品名: スマートフォン, 価格: 89000円, 在庫: 25個
```

> 📝 **ポイント**: 条件を引数として受け取ることで、同じメソッドを様々な条件で再利用できます。`displayResults()`のような共通メソッドを作ることで、コードの重複を避けることができます。

### 4.2 JOIN操作の基本

#### 4.2.1 JOINとは

**JOIN**は、複数のテーブルを結合してデータを取得する操作です。商品テーブルとカテゴリテーブルを結合することで、商品名とカテゴリ名を一緒に表示できます。

> 📝 **ポイント**: JOINを使うことで、関連するテーブルの情報を一度に取得できます。今回は最も基本的な`INNER JOIN`を使用します。

#### 4.2.2 基本的なJOIN操作

商品とカテゴリを結合して表示するプログラムを作成してみましょう。

`src/main/java/com/example/ProductCategoryJoin.java` を作成：

```java
package com.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ProductCategoryJoin {
    
    private static final String URL = "jdbc:mysql://localhost:3306/online_shop";
    private static final String USER = "root";
    private static final String PASSWORD = "your_password";
    
    public static void main(String[] args) {
        // 全商品をカテゴリ別に表示
        showAllProductsWithCategory();
        
        System.out.println();
        
        // 特定カテゴリの商品を表示
        showProductsByCategory("電子機器");
    }
    
    // 全商品をカテゴリ別に表示
    public static void showAllProductsWithCategory() {
        System.out.println("=== 全商品（カテゴリ別） ===");
        
        String sql = """
            SELECT 
                p.name AS product_name,
                p.price,
                c.name AS category_name
            FROM products p
            INNER JOIN categories c ON p.category_id = c.id
            ORDER BY c.name, p.name
            """;
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 Statement statement = connection.createStatement();
                 ResultSet resultSet = statement.executeQuery(sql)) {
                
                String currentCategory = "";
                
                while (resultSet.next()) {
                    String categoryName = resultSet.getString("category_name");
                    String productName = resultSet.getString("product_name");
                    int price = resultSet.getInt("price");
                    
                    // カテゴリが変わったら見出しを表示
                    if (!categoryName.equals(currentCategory)) {
                        System.out.println("\n【" + categoryName + "】");
                        currentCategory = categoryName;
                    }
                    
                    System.out.printf("  %s - %d円%n", productName, price);
                }
                
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
    
    // 特定カテゴリの商品を表示
    public static void showProductsByCategory(String categoryName) {
        System.out.println("=== " + categoryName + "の商品一覧 ===");
        
        String sql = """
            SELECT 
                p.name AS product_name,
                p.price,
                p.stock
            FROM products p
            INNER JOIN categories c ON p.category_id = c.id
            WHERE c.name = ?
            ORDER BY p.name
            """;
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            try (Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
                 PreparedStatement pstmt = connection.prepareStatement(sql)) {
                
                pstmt.setString(1, categoryName);
                
                try (ResultSet resultSet = pstmt.executeQuery()) {
                    boolean found = false;
                    
                    while (resultSet.next()) {
                        String productName = resultSet.getString("product_name");
                        int price = resultSet.getInt("price");
                        int stock = resultSet.getInt("stock");
                        
                        System.out.printf("商品名: %s, 価格: %d円, 在庫: %d個%n", 
                                        productName, price, stock);
                        found = true;
                    }
                    
                    if (!found) {
                        System.out.println("該当するカテゴリの商品が見つかりませんでした。");
                    }
                }
                
            }
            
        } catch (ClassNotFoundException | SQLException e) {
            System.err.println("エラー: " + e.getMessage());
        }
    }
}
```

**実行結果例**:

```
=== 全商品（カテゴリ別） ===

【家具】
  オフィスチェア - 25000円
  デスク - 35000円

【書籍】
  Java入門書 - 3500円
  データベース設計本 - 4200円

【電子機器】
  スマートフォン - 89000円
  ノートパソコン - 120000円

=== 電子機器の商品一覧 ===
商品名: スマートフォン, 価格: 89000円, 在庫: 25個
商品名: ノートパソコン, 価格: 120000円, 在庫: 15個
```

> 📝 **ポイント**: `INNER JOIN categories c ON p.category_id = c.id`により、商品のcategory\_idとカテゴリのidが一致するデータを結合しています。WHERE句とJOINを組み合わせることで、特定の条件を満たすデータを取得できます。

***

### 演習問題

#### 問題 4-1: 条件検索の実装

在庫数が指定した値以下の商品を検索するメソッド`searchByMaxStock(int maxStock)`を作成してください。

<details>

<summary>ヒント</summary>

* `WHERE stock <= ?`で在庫数の条件を指定
* PreparedStatementでパラメータを設定
* 結果が見つからない場合のメッセージも含める

</details>

#### 問題 4-2: 価格範囲検索の応用

価格範囲を指定し、その範囲内の商品をカテゴリ情報と一緒に表示するメソッド`searchByPriceWithCategory(int minPrice, int maxPrice)`を作成してください。

<details>

<summary>ヒント</summary>

* JOINでproductsとcategoriesテーブルを結合
* `WHERE p.price >= ? AND p.price <= ?`で価格範囲を指定
* 商品名、価格、カテゴリ名を表示

</details>

#### 問題 4-3: カテゴリ別在庫検索

指定したカテゴリで、指定した在庫数以上の商品を検索するメソッド`searchByCategoryAndStock(String categoryName, int minStock)`を作成してください。

<details>

<summary>ヒント</summary>

* JOINでproductsとcategoriesテーブルを結合
* `WHERE c.name = ? AND p.stock >= ?`で複数条件を指定
* 2つのパラメータを順番に設定

</details>

#### 問題 4-4: 商品名検索（カテゴリ付き）

商品名のキーワードで部分検索を行い、商品名、価格、カテゴリ名を表示するメソッド`searchProductsWithCategory(String keyword)`を作成してください。

<details>

<summary>ヒント</summary>

* JOINでproductsとcategoriesテーブルを結合
* `WHERE p.name LIKE ?`で部分一致検索
* `setString(1, "%" + keyword + "%")`でパラメータを設定

</details>

#### 問題 4-5: カテゴリ別統計

各カテゴリの商品数と平均価格を表示するメソッド`showCategoryStatistics()`を作成してください。

<details>

<summary>ヒント</summary>

* JOINとGROUP BYを組み合わせ
* `COUNT(p.id)` AS product\_count, `AVG(p.price)` AS avg\_priceで集計
* `GROUP BY c.id, c.name`でカテゴリ別に集計

</details>
