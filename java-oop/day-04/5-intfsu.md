# 5. インターフェース

#### 5.1 インターフェースの基本概念

**5.1.1 複数の役割を持つオブジェクト**

現実世界では、一つのものが複数の役割を持つことがよくあります。例えば、「スマートフォン」は「電話」としても「カメラ」としても「音楽プレイヤー」としても機能します。

プログラムでも同様に、一つのクラスが複数の機能を提供したい場合があります。しかし、Javaは単一継承のため、一つのクラスしか継承できません。

```java
// 抽象クラスだけでは限界がある例
public abstract class Device {
    public abstract void powerOn();
}

// スマートフォンは Device を継承
public class Smartphone extends Device {
    @Override
    public void powerOn() {
        System.out.println("スマートフォンの電源をオンにします");
    }
    
    // 電話機能が欲しいが、Phone クラスを継承できない
    // カメラ機能が欲しいが、Camera クラスを継承できない
}
```

このような場合に**インターフェース**が役立ちます。インターフェースは「契約」のようなもので、「このメソッドを必ず実装してください」という約束を定義します。

> 📝 **ポイント** インターフェースは「〜ができる」という能力を表現します。Phoneable（電話できる）、Photographable（写真が撮れる）といった具合です。

**5.1.2 インターフェースとは**

**インターフェース**は、クラスが実装すべきメソッドの仕様を定義する仕組みです。抽象クラスと似ていますが、以下の違いがあります：

| 特徴    | 抽象クラス      | インターフェース                |
| ----- | ---------- | ----------------------- |
| 継承数   | 1つのみ       | 複数可能                    |
| メソッド  | 抽象・具象両方可能  | 抽象メソッドのみ（Java 8以降は例外あり） |
| フィールド | 通常のフィールド可能 | publicstaticfinal のみ    |
| 用途    | is-a関係     | can-do関係                |

```java
// 電話機能のインターフェース
public interface Callable {
    void makeCall(String number);
    void receiveCall();
}

// カメラ機能のインターフェース
public interface Photographable {
    void takePhoto();
    void viewPhotos();
}

// 複数のインターフェースを実装
public class Smartphone implements Callable, Photographable {
    private String model;
    
    public Smartphone(String model) {
        this.model = model;
    }
    
    // Callable インターフェースの実装
    @Override
    public void makeCall(String number) {
        System.out.println(model + "で" + number + "に電話をかけます");
    }
    
    @Override
    public void receiveCall() {
        System.out.println(model + "で電話を受けます");
    }
    
    // Photographable インターフェースの実装
    @Override
    public void takePhoto() {
        System.out.println(model + "で写真を撮影します");
    }
    
    @Override
    public void viewPhotos() {
        System.out.println(model + "で写真を表示します");
    }
}
```

> 📝 **ポイント** インターフェースを使うことで、継承の制限を超えて複数の「機能」を一つのクラスに持たせることができます。

#### 5.2 インターフェースの実装と活用

**5.2.1 基本的な実装方法**

インターフェースを実装するクラスは、`implements` キーワードを使い、すべての抽象メソッドを実装する必要があります。

```java
// 決済機能のインターフェース
public interface Payable {
    boolean processPayment(double amount);
    String getPaymentMethod();
}

// クレジットカード決済
public class CreditCard implements Payable {
    private String cardNumber;
    private double balance;
    
    public CreditCard(String cardNumber, double balance) {
        this.cardNumber = cardNumber;
        this.balance = balance;
    }
    
    @Override
    public boolean processPayment(double amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println("クレジットカードで" + amount + "円決済しました");
            return true;
        }
        System.out.println("残高不足です");
        return false;
    }
    
    @Override
    public String getPaymentMethod() {
        return "クレジットカード";
    }
}

// 電子マネー決済
public class ElectronicMoney implements Payable {
    private String serviceName;
    private double balance;
    
    public ElectronicMoney(String serviceName, double balance) {
        this.serviceName = serviceName;
        this.balance = balance;
    }
    
    @Override
    public boolean processPayment(double amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println(serviceName + "で" + amount + "円決済しました");
            return true;
        }
        System.out.println("チャージ残高不足です");
        return false;
    }
    
    @Override
    public String getPaymentMethod() {
        return serviceName;
    }
}
```

**5.2.2 ポリモーフィズムの活用**

インターフェースを使うことで、異なるクラスのオブジェクトを同じ型として扱えます。

```java
import java.util.ArrayList;
import java.util.List;

public class PaymentDemo {
    public static void main(String[] args) {
        // 異なる決済方法のオブジェクトを作成
        Payable creditCard = new CreditCard("1234-5678", 50000);
        Payable eMoney = new ElectronicMoney("PayPay", 10000);
        
        // 同じインターフェース型として扱える
        List<Payable> paymentMethods = new ArrayList<>();
        paymentMethods.add(creditCard);
        paymentMethods.add(eMoney);
        
        // どの決済方法でも同じように処理できる
        double purchaseAmount = 3000;
        
        for (Payable payment : paymentMethods) {
            System.out.println("決済方法: " + payment.getPaymentMethod());
            payment.processPayment(purchaseAmount);
            System.out.println("---");
        }
    }
}
```

<details>

<summary>実行結果</summary>

```
決済方法: クレジットカード
クレジットカードで3000.0円決済しました
---
決済方法: PayPay
PayPayで3000.0円決済しました
---
```

</details>

> 📝 **ポイント** インターフェースを使うことで、具体的な実装に依存しない柔軟なプログラムを作成できます。新しい決済方法を追加する場合も、既存のコードを変更せずに済みます。

#### 5.3 実用的なインターフェース設計

**5.3.1 Webアプリケーションでの活用**

実際のWebアプリケーション開発では、インターフェースを使って機能を分離します。

```java
// データアクセス層のインターフェース
public interface UserRepository {
    void save(User user);
    User findById(int id);
    List<User> findAll();
    void delete(int id);
}

// データベース実装
public class DatabaseUserRepository implements UserRepository {
    @Override
    public void save(User user) {
        System.out.println("データベースにユーザーを保存: " + user.getName());
    }
    
    @Override
    public User findById(int id) {
        System.out.println("データベースからユーザーID " + id + " を検索");
        return new User(id, "データベースユーザー");
    }
    
    @Override
    public List<User> findAll() {
        System.out.println("データベースから全ユーザーを取得");
        return new ArrayList<>();
    }
    
    @Override
    public void delete(int id) {
        System.out.println("データベースからユーザーID " + id + " を削除");
    }
}

// ファイル実装（テスト用など）
public class FileUserRepository implements UserRepository {
    @Override
    public void save(User user) {
        System.out.println("ファイルにユーザーを保存: " + user.getName());
    }
    
    @Override
    public User findById(int id) {
        System.out.println("ファイルからユーザーID " + id + " を検索");
        return new User(id, "ファイルユーザー");
    }
    
    @Override
    public List<User> findAll() {
        System.out.println("ファイルから全ユーザーを取得");
        return new ArrayList<>();
    }
    
    @Override
    public void delete(int id) {
        System.out.println("ファイルからユーザーID " + id + " を削除");
    }
}

// サービス層（ビジネスロジック）
public class UserService {
    private UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public void registerUser(String name) {
        User user = new User(0, name);
        userRepository.save(user);
        System.out.println("ユーザー登録完了: " + name);
    }
    
    public void displayUser(int id) {
        User user = userRepository.findById(id);
        System.out.println("ユーザー情報: " + user.getName());
    }
}
```

<details>

<summary>使用例</summary>

```java
public class RepositoryDemo {
    public static void main(String[] args) {
        // データベース実装を使用
        UserRepository dbRepo = new DatabaseUserRepository();
        UserService dbService = new UserService(dbRepo);
        
        dbService.registerUser("田中太郎");
        dbService.displayUser(1);
        
        System.out.println("---");
        
        // ファイル実装に切り替え
        UserRepository fileRepo = new FileUserRepository();
        UserService fileService = new UserService(fileRepo);
        
        fileService.registerUser("佐藤花子");
        fileService.displayUser(2);
    }
}
```

</details>

> 📝 **ポイント** インターフェースを使うことで、データの保存方法（データベース、ファイル、メモリなど）を後から変更できる柔軟な設計になります。テスト時には軽量な実装、本番では本格的な実装を使い分けることができます。

**5.3.2 機能の組み合わせ**

複数のインターフェースを実装することで、柔軟な機能の組み合わせが可能です。

```java
// 読み取り可能
public interface Readable {
    String read();
}

// 書き込み可能
public interface Writable {
    void write(String content);
}

// 読み書き両方可能なファイル
public class TextFile implements Readable, Writable {
    private String filename;
    private String content;
    
    public TextFile(String filename) {
        this.filename = filename;
        this.content = "";
    }
    
    @Override
    public String read() {
        System.out.println(filename + "からデータを読み取り");
        return content;
    }
    
    @Override
    public void write(String content) {
        System.out.println(filename + "にデータを書き込み");
        this.content = content;
    }
}

// 読み取り専用ファイル
public class ReadOnlyFile implements Readable {
    private String filename;
    private String content;
    
    public ReadOnlyFile(String filename, String content) {
        this.filename = filename;
        this.content = content;
    }
    
    @Override
    public String read() {
        System.out.println(filename + "からデータを読み取り（読み取り専用）");
        return content;
    }
}
```

> 📝 **ポイント** インターフェースを組み合わせることで、必要な機能だけを持つクラスを設計できます。セキュリティや用途に応じて、読み取り専用、書き込み専用、読み書き両対応などを使い分けられます。

***

#### 演習問題

**問題 5-1: 基本的なインターフェースの実装**

動物の鳴き声と移動方法を表現するインターフェースを作成してください。

1. `Soundable` インターフェース:
   * メソッド: `makeSound()` (鳴き声を出す)
2. `Movable` インターフェース:
   * メソッド: `move()` (移動する)
3. `Cat` クラス:
   * 両方のインターフェースを実装
   * 鳴き声: "ニャー！"
   * 移動: "四本足で歩きます"
4. `Fish` クラス:
   * `Movable` インターフェースのみ実装
   * 移動: "ひれで泳ぎます"

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>使用例</summary>

```java
public class AnimalInterfaceDemo {
    public static void main(String[] args) {
        Cat cat = new Cat();
        Fish fish = new Fish();
        
        // 猫は鳴くことも移動することもできる
        if (cat instanceof Soundable) {
            ((Soundable) cat).makeSound();
        }
        if (cat instanceof Movable) {
            ((Movable) cat).move();
        }
        
        System.out.println("---");
        
        // 魚は移動のみ
        if (fish instanceof Movable) {
            ((Movable) fish).move();
        }
    }
}
```

</details>

<details>

<summary>ヒント</summary>

* インターフェースは `interface` キーワードで定義します
* 複数のインターフェースを実装する場合は、カンマで区切ります: `implements Soundable, Movable`
* すべての抽象メソッドを実装する必要があります

</details>

**問題 5-2: 計算機インターフェース**

異なる種類の計算機を表現するインターフェースを作成してください。

1. `Calculator` インターフェース:
   * メソッド: `add(double a, double b)`, `subtract(double a, double b)`, `getCalculatorType()`
2. `BasicCalculator` クラス:
   * 基本的な計算機能
   * タイプ: "基本計算機"
3. `ScientificCalculator` クラス:
   * 基本計算に加えて、`multiply(double a, double b)`, `divide(double a, double b)` を追加
   * タイプ: "関数電卓"

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>ヒント</summary>

* `ScientificCalculator` では追加のメソッドを独自に定義できます
* 除算では0で割る場合の処理を考慮してください
* インターフェースのメソッドは自動的に `public abstract` になります

</details>

**問題 5-3: 通知システム**

異なる方法で通知を送るシステムを作成してください。

1. `Notifiable` インターフェース:
   * メソッド: `sendNotification(String message)`, `getNotificationType()`
2. `EmailNotification` クラス:
   * メールアドレスを受け取るコンストラクター
   * メール通知を実装
3. `SMSNotification` クラス:
   * 電話番号を受け取るコンストラクター
   * SMS通知を実装
4. `NotificationManager` クラス:
   * 複数の通知方法を管理
   * `addNotifier(Notifiable notifier)` と `sendToAll(String message)` メソッド

```java
import java.util.ArrayList;
import java.util.List;

// ここに通知システムを実装してください

```

<details>

<summary>使用例</summary>

```java
public class NotificationDemo {
    public static void main(String[] args) {
        NotificationManager manager = new NotificationManager();
        
        // 異なる通知方法を登録
        manager.addNotifier(new EmailNotification("user@example.com"));
        manager.addNotifier(new SMSNotification("090-1234-5678"));
        
        // 全ての方法で通知を送信
        manager.sendToAll("重要なお知らせがあります");
    }
}
```

</details>

<details>

<summary>ヒント</summary>

* `NotificationManager` では `List<Notifiable>` を使って通知方法を管理します
* 各通知クラスのコンストラクターで宛先情報を保存します
* `sendToAll` メソッドではリスト内の全ての通知方法を使用します

</details>

**問題 5-4: 図形描画システム**

図形を描画するシステムをインターフェースで設計してください。

1. `Drawable` インターフェース:
   * メソッド: `draw()`, `getArea()`
2. `Colorable` インターフェース:
   * メソッド: `setColor(String color)`, `getColor()`
3. `Circle` クラス:
   * 両方のインターフェースを実装
   * フィールド: radius (半径), color
4. `Rectangle` クラス:
   * 両方のインターフェースを実装
   * フィールド: width (幅), height (高さ), color
5. `DrawingCanvas` クラス:
   * 図形のリストを管理
   * `addShape(Drawable shape)` と `drawAll()` メソッド

```java
import java.util.ArrayList;
import java.util.List;

// ここに図形描画システムを実装してください

```

<details>

<summary>ヒント</summary>

* 円の面積: `Math.PI * radius * radius`
* 長方形の面積: `width * height`
* `DrawingCanvas` では `List<Drawable>` を使って図形を管理します
* 色を変更できる図形は `Colorable` インターフェースでチェックしてから操作します

</details>

**問題 5-5: eコマースシステム**

オンラインショップの商品と決済システムを作成してください。

1. `Purchasable` インターフェース:
   * メソッド: `getPrice()`, `getName()`, `isAvailable()`
2. `Shippable` インターフェース:
   * メソッド: `calculateShippingCost()`, `getWeight()`
3. `PhysicalProduct` クラス:
   * 両方のインターフェースを実装
   * フィールド: name, price, stock, weight
4. `DigitalProduct` クラス:
   * `Purchasable` インターフェースのみ実装
   * フィールド: name, price, downloadSize
5. `ShoppingCart` クラス:
   * 商品のリストを管理
   * `addProduct(Purchasable product)`, `calculateTotal()`, `calculateShipping()` メソッド

```java
import java.util.ArrayList;
import java.util.List;

// ここにeコマースシステムを実装してください

```

<details>

<summary>使用例</summary>

```java
public class ECommerceDemo {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        
        // 異なる種類の商品をカートに追加
        cart.addProduct(new PhysicalProduct("ノートパソコン", 80000, 10, 2.5));
        cart.addProduct(new DigitalProduct("画像編集ソフト", 15000, 500));
        cart.addProduct(new PhysicalProduct("マウス", 3000, 20, 0.2));
        
        // 合計金額と送料を計算
        System.out.println("商品合計: " + cart.calculateTotal() + "円");
        System.out.println("送料: " + cart.calculateShipping() + "円");
        System.out.println("総合計: " + (cart.calculateTotal() + cart.calculateShipping()) + "円");
    }
}
```

</details>

<details>

<summary>ヒント</summary>

* 物理商品は在庫管理と重量による送料計算が必要です
* デジタル商品は送料不要で、ダウンロードサイズの情報を持ちます
* `ShoppingCart` では商品の種類に関係なく価格計算し、配送可能な商品のみ送料を計算します
* `instanceof` を使って商品の種類を判定できます

</details>
