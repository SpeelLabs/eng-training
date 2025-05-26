# 5. インターフェース

#### 5.1 インターフェースの基本概念

**5.1.1 なぜインターフェースが必要か**

プログラムを作っていると、「同じ機能を持つが、実装方法が違う」クラスを作ることがよくあります。例えば、データを保存する機能を考えてみましょう。

```java
// 問題のある設計例
public class FileDataSaver {
    public void save(String data) {
        System.out.println("ファイルにデータを保存: " + data);
    }
}

public class DatabaseSaver {
    public void saveToDatabase(String data) {  // メソッド名が違う！
        System.out.println("データベースにデータを保存: " + data);
    }
}
```

上記の例では、どちらも「データを保存する」機能ですが、メソッド名が異なります。これでは、保存方法を切り替えたい時にコードを大幅に変更する必要があります。

> 📝 **ポイント** 同じ機能を持つクラス群で、メソッド名や使い方がバラバラだと、コードの変更が困難になります。

**5.1.2 インターフェースによる統一**

**インターフェース**は、「どんなメソッドを持つべきか」の設計図です。クラスとは違い、実装は含まず、メソッドの名前と引数だけを定義します。

```java
// インターフェースの定義
public interface DataSaver {
    void save(String data);  // 実装は書かない
}
```

このインターフェースを実装すると：

```java
// ファイル保存クラス
public class FileDataSaver implements DataSaver {
    @Override
    public void save(String data) {
        System.out.println("ファイルにデータを保存: " + data);
    }
}

// データベース保存クラス
public class DatabaseSaver implements DataSaver {
    @Override
    public void save(String data) {
        System.out.println("データベースにデータを保存: " + data);
    }
}
```

これで両方のクラスが同じ `save()` メソッドを持つようになりました。

> 📝 **ポイント** インターフェースを使うことで、異なるクラスでも同じ方法で使えるようになります。

#### 5.2 インターフェースの実装

**5.2.1 基本的な実装方法**

インターフェースは `interface` キーワードで定義し、クラスでは `implements` キーワードで実装します。

```java
// 支払い処理のインターフェース
public interface PaymentProcessor {
    boolean processPayment(double amount);
    String getPaymentMethod();
}
```

このインターフェースを実装するクラスを作成：

```java
// クレジットカード支払い
public class CreditCardPayment implements PaymentProcessor {
    private String cardNumber;
    
    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }
    
    @Override
    public boolean processPayment(double amount) {
        System.out.println("クレジットカードで" + amount + "円を支払い");
        // 実際の処理はここに書く
        return true;
    }
    
    @Override
    public String getPaymentMethod() {
        return "クレジットカード";
    }
}

// 銀行振込支払い
public class BankTransferPayment implements PaymentProcessor {
    private String bankAccount;
    
    public BankTransferPayment(String bankAccount) {
        this.bankAccount = bankAccount;
    }
    
    @Override
    public boolean processPayment(double amount) {
        System.out.println("銀行振込で" + amount + "円を支払い");
        return true;
    }
    
    @Override
    public String getPaymentMethod() {
        return "銀行振込";
    }
}
```

<details>

<summary>使用例</summary>

```java
public class PaymentDemo {
    public static void main(String[] args) {
        // インターフェース型の変数で受け取る
        PaymentProcessor payment1 = new CreditCardPayment("1234-5678-9012-3456");
        PaymentProcessor payment2 = new BankTransferPayment("12345678");
        
        // 同じ方法で使用可能
        payment1.processPayment(1000);
        System.out.println("支払い方法: " + payment1.getPaymentMethod());
        
        payment2.processPayment(2000);
        System.out.println("支払い方法: " + payment2.getPaymentMethod());
    }
}
```

</details>

> 📝 **ポイント** インターフェースを実装するクラスは、インターフェースで定義されたすべてのメソッドを実装する必要があります。

**5.2.2 複数のインターフェースの実装**

一つのクラスは複数のインターフェースを実装できます。

```java
public interface Printable {
    void print();
}

public interface Saveable {
    void save();
}

// 複数のインターフェースを実装
public class Document implements Printable, Saveable {
    private String content;
    
    public Document(String content) {
        this.content = content;
    }
    
    @Override
    public void print() {
        System.out.println("文書を印刷: " + content);
    }
    
    @Override
    public void save() {
        System.out.println("文書を保存: " + content);
    }
}
```

> 📝 **ポイント** クラスは複数のインターフェースを実装できるため、柔軟な設計が可能です。

#### 5.3 インターフェースの実用例

**5.3.1 通知システムの実装**

様々な方法で通知を送るシステムを作ってみましょう。

```java
// 通知インターフェース
public interface NotificationSender {
    void sendNotification(String message, String recipient);
}
```

```java
// メール通知
public class EmailNotification implements NotificationSender {
    @Override
    public void sendNotification(String message, String recipient) {
        System.out.println("📧 メール送信");
        System.out.println("宛先: " + recipient);
        System.out.println("内容: " + message);
    }
}

// SMS通知
public class SmsNotification implements NotificationSender {
    @Override
    public void sendNotification(String message, String recipient) {
        System.out.println("📱 SMS送信");
        System.out.println("電話番号: " + recipient);
        System.out.println("内容: " + message);
    }
}

// Slack通知
public class SlackNotification implements NotificationSender {
    @Override
    public void sendNotification(String message, String recipient) {
        System.out.println("💬 Slack送信");
        System.out.println("チャンネル: " + recipient);
        System.out.println("内容: " + message);
    }
}
```

通知システムを使うクラス：

```java
public class NotificationManager {
    public void sendAlert(NotificationSender sender, String message, String recipient) {
        System.out.println("=== 緊急通知 ===");
        sender.sendNotification(message, recipient);
        System.out.println("================");
    }
}
```

<details>

<summary>使用例</summary>

```java
public class NotificationDemo {
    public static void main(String[] args) {
        NotificationManager manager = new NotificationManager();
        
        // 異なる通知方法を同じように使用
        manager.sendAlert(
            new EmailNotification(),
            "システムエラーが発生しました",
            "admin@company.com"
        );
        
        manager.sendAlert(
            new SmsNotification(),
            "サーバーダウンです",
            "090-1234-5678"
        );
        
        manager.sendAlert(
            new SlackNotification(),
            "メンテナンス開始",
            "#general"
        );
    }
}
```

</details>

> 📝 **ポイント** インターフェースを使うことで、新しい通知方法を追加する際も既存のコードを変更する必要がありません。

**5.3.2 Webアプリケーションでの活用**

実際のWebアプリケーション開発でもインターフェースは重要です。

```java
// ユーザー情報を取得するインターフェース
public interface UserRepository {
    User findById(int id);
    List<User> findAll();
    void save(User user);
    void delete(int id);
}
```

```java
// データベース実装
public class DatabaseUserRepository implements UserRepository {
    @Override
    public User findById(int id) {
        System.out.println("データベースからユーザーID " + id + " を検索");
        // 実際のデータベース処理
        return new User(id, "データベースユーザー");
    }
    
    @Override
    public List<User> findAll() {
        System.out.println("データベースから全ユーザーを取得");
        return Arrays.asList(new User(1, "ユーザー1"), new User(2, "ユーザー2"));
    }
    
    @Override
    public void save(User user) {
        System.out.println("データベースにユーザーを保存: " + user.getName());
    }
    
    @Override
    public void delete(int id) {
        System.out.println("データベースからユーザーID " + id + " を削除");
    }
}

// テスト用の実装
public class MockUserRepository implements UserRepository {
    @Override
    public User findById(int id) {
        System.out.println("テスト用ユーザーを返却");
        return new User(id, "テストユーザー");
    }
    
    @Override
    public List<User> findAll() {
        return Arrays.asList(new User(99, "テストユーザー"));
    }
    
    @Override
    public void save(User user) {
        System.out.println("テスト: ユーザー保存をシミュレート");
    }
    
    @Override
    public void delete(int id) {
        System.out.println("テスト: ユーザー削除をシミュレート");
    }
}
```

> 📝 **ポイント** 実際の開発では、本番用とテスト用で異なる実装を使い分けることがあります。インターフェースがあることで、実装を簡単に切り替えられます。

***

#### 演習問題

**問題 5-1: 基本的なインターフェースの実装**

図形の面積を計算するインターフェース `Shape` を作成し、それを実装する `Rectangle` (長方形) と `Circle` (円) クラスを作成してください。

* Shape インターフェース:
  * メソッド: `double calculateArea()`, `String getShapeName()`
* Rectangle クラス:
  * フィールド: width, height
  * コンストラクター: 幅と高さを受け取る
* Circle クラス:
  * フィールド: radius
  * コンストラクター: 半径を受け取る

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>使用例</summary>

```java
public class ShapeDemo {
    public static void main(String[] args) {
        Shape rectangle = new Rectangle(5.0, 3.0);
        Shape circle = new Circle(4.0);
        
        System.out.println(rectangle.getShapeName() + "の面積: " + rectangle.calculateArea());
        System.out.println(circle.getShapeName() + "の面積: " + circle.calculateArea());
    }
}
```

</details>

<details>

<summary>ヒント</summary>

* 長方形の面積: width × height
* 円の面積: π × radius² (Math.PIを使用)
* インターフェースのメソッドはすべて実装する必要があります

</details>

**問題 5-2: 複数インターフェースの実装**

以下の2つのインターフェースを作成し、両方を実装する `SmartPhone` クラスを作成してください。

* Phone インターフェース:
  * メソッド: `void makeCall(String number)`, `void receiveCall(String number)`
* Camera インターフェース:
  * メソッド: `void takePicture()`, `void recordVideo()`
* SmartPhone クラス:
  * フィールド: model (機種名)
  * 両方のインターフェースを実装

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 複数のインターフェースは `implements Interface1, Interface2` のように実装します
* すべてのメソッドに適切なメッセージを表示する実装を行ってください

</details>

**問題 5-3: 計算機システムの実装**

電卓の機能を表すインターフェース `Calculator` を作成し、異なる種類の計算機クラスを実装してください。

* Calculator インターフェース:
  * メソッド: `double add(double a, double b)`, `double subtract(double a, double b)`, `double multiply(double a, double b)`, `double divide(double a, double b)`
* BasicCalculator クラス:
  * 基本的な四則演算を実装
* ScientificCalculator クラス:
  * 基本演算に加えて、power（べき乗）メソッドを追加
  * 除算時にゼロ除算チェックを実装

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>ヒント</summary>

* べき乗は `Math.pow(base, exponent)` を使用
* ゼロ除算の場合は適切なメッセージを表示し、0を返すか例外をスローしてください

</details>

**問題 5-4: ファイル処理システム**

ファイル操作を表すインターフェース `FileProcessor` を作成し、異なるファイル形式に対応するクラスを実装してください。

* FileProcessor インターフェース:
  * メソッド: `void readFile(String fileName)`, `void writeFile(String fileName, String content)`, `String getFileType()`
* TextFileProcessor クラス:
  * テキストファイル（.txt）の処理
* CsvFileProcessor クラス:
  * CSVファイル（.csv）の処理
* JsonFileProcessor クラス:
  * JSONファイル（.json）の処理

各クラスでは、それぞれのファイル形式に応じたメッセージを表示してください。

```java
// ここにインターフェースとクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 実際のファイル処理は実装せず、コンソールにメッセージを表示するだけで構いません
* 各ファイル形式の特徴を活かしたメッセージを考えてみてください

</details>

**問題 5-5: オンラインショップ決済システム**

オンラインショップの決済システムを、インターフェースを使って実装してください。

* PaymentMethod インターフェース:
  * メソッド: `boolean processPayment(double amount)`, `String getPaymentType()`, `double getTransactionFee(double amount)`
* 実装クラス:
  * CreditCardPayment: 手数料3%
  * BankTransferPayment: 手数料200円固定
  * DigitalWalletPayment: 手数料1%
* PaymentSystem クラス:
  * メソッド: `void processOrder(PaymentMethod payment, double amount)` - 支払い処理と手数料を含めた総額の表示

```java
import java.util.ArrayList;
import java.util.List;

// ここにインターフェースとクラスを実装してください

```

<details>

<summary>使用例</summary>

```java
public class PaymentDemo {
    public static void main(String[] args) {
        PaymentSystem system = new PaymentSystem();
        
        system.processOrder(new CreditCardPayment("1234-****-****-5678"), 10000);
        system.processOrder(new BankTransferPayment("みずほ銀行"), 5000);
        system.processOrder(new DigitalWalletPayment("PayPay"), 3000);
    }
}
```

</details>

<details>

<summary>ヒント</summary>

* 手数料計算は各決済方法で異なります
* PaymentSystemクラスでは、決済処理→手数料計算→総額表示の流れを実装
* 実際の決済処理はシミュレートで構いません

</details>
