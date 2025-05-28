# 3. カプセル化

### 3.1 カプセル化の基本概念

#### 3.1.1 データ保護の必要性

プログラムの規模が大きくなるにつれて、データの安全性と整合性を保つことが重要になります。例えば、銀行口座の残高や口座番号などは、不正な変更から保護する必要があります。

以下のようなコードは問題を引き起こす可能性があります。

```java
public class BankAccount {
    // 誰でもアクセスできる状態
    public String accountNumber;
    public double balance;
    
    public void deposit( double amount ) {
        balance += amount;
    }
}
```

```java
// 使用例
public class BankDemo {
    public static void main( String[] args ) {
        BankAccount account = new BankAccount();
        account.accountNumber = "12345-67890";
        account.deposit(1000);  // 正規の方法での入金
        
        // 直接口座番号を変更 (これは望ましくない)
        account.accountNumber = "99999-99999";  // 口座番号の不正な変更！
        
        // 直接残高をマイナスに変更 (これも望ましくない)
        account.balance = -50000;  // 不正な残高操作！
    }
}
```

上記の例では、`accountNumber` と `balance` フィールドが `public` になっているため、誰でも直接アクセスして変更できてしまいます。これにより、口座番号の不正な変更や残高のマイナス化など、データの整合性を損なう危険性があります。

> 📝 **ポイント** 外部からの直接アクセスを許可すると、データの不正な変更が可能になるだけでなく、内部実装の変更が困難になります。例えば残高の計算方法や保存形式を変更したい場合、そのフィールドを直接使用しているすべてのコードを修正しなければなりません。

#### 3.1.2 カプセル化とは何か

**カプセル化** (encapsulation) とは、データ (フィールド) と、それを操作するメソッドをひとつの単位にまとめ、外部からの不正なアクセスからデータを保護する仕組みです。

カプセル化の主要な要素：

1. データを`private` (非公開) にする
2. データへのアクセスは`public`メソッドを通じてのみ許可する
3. データの検証や加工をメソッド内で行う

```java
public class BankAccount {
    // privateにすることでデータを保護
    private String accountNumber;
    private double balance;
    
    // コンストラクター
    public BankAccount( String accountNumber ) {
        this.accountNumber = accountNumber;
        this.balance = 0.0;
    }
    
    // 入金メソッド - データ検証を含む
    public void deposit( double amount ) {
        if (amount > 0) {
            this.balance += amount;
            System.out.println(amount + "円を入金しました。残高: " + this.balance + "円");
        } else {
            System.out.println("無効な入金額です。");
        }
    }
    
    // 引き出しメソッド - データ検証を含む
    public void withdraw( double amount ) {
        if (amount > 0 && this.balance >= amount) {
            this.balance -= amount;
            System.out.println(amount + "円を引き出しました。残高: " + this.balance + "円");
        } else {
            System.out.println("引き出しできません。残高不足または無効な金額です。");
        }
    }
    
    // 残高照会メソッド - データへの読み取りアクセスを提供
    public double getBalance() {
        return this.balance;
    }
    
    // 口座番号照会メソッド - データの一部を隠した形で返す
    public String getAccountNumber() {
        // 下4桁以外を*で隠す
        return "******" + this.accountNumber.substring(this.accountNumber.length() - 4);
    }
}
```

この例では、`accountNumber` と `balance` フィールドを `private` にし、入金や引き出しなどの操作は専用のメソッドを通じてのみ行えるようにしています。これにより、口座番号の変更を防止し、残高が負の値になることを防ぎます。さらに、`getAccountNumber` メソッドでは、セキュリティのために口座番号の一部だけを返しています。

> 📝 **ポイント** カプセル化は 「情報隠蔽」 (information hiding) とも呼ばれ、クラスの内部実装の詳細を利用者から隠します。これにより、内部実装を変更しても、外部のコードに影響を与えることなく改善や修正ができます。

### 3.2 アクセス修飾子の使い方

#### 3.2.1 アクセス修飾子の種類と範囲

Javaには、クラス、フィールド、メソッドのアクセス範囲を制御するための4種類のアクセス修飾子があります。

| アクセス修飾子     | クラス内 | 同じパッケージ | サブクラス | 他のパッケージ |
| ----------- | ---- | ------- | ----- | ------- |
| `private`   | ✓    | ✗       | ✗     | ✗       |
| (デフォルト)     | ✓    | ✓       | ✗     | ✗       |
| `protected` | ✓    | ✓       | ✓     | ✗       |
| `public`    | ✓    | ✓       | ✓     | ✓       |

```java
public class AccessModifierExample {
    private int privateVar;       // このクラス内でのみアクセス可能
    int defaultVar;               // このクラスと同じパッケージ内でアクセス可能
    protected int protectedVar;   // このクラス、同じパッケージ、サブクラスでアクセス可能
    public int publicVar;         // どこからでもアクセス可能
    
    private void privateMethod() { /* ... */ }
    void defaultMethod() { /* ... */ }
    protected void protectedMethod() { /* ... */ }
    public void publicMethod() { /* ... */ }
}
```

> 📝 **ポイント** アクセス修飾子を選ぶ際は、「最小限の公開」 の原則に従うとよいでしょう。つまり、必要な範囲でのみアクセス可能にし、それ以外のアクセスは制限します。これにより、カプセル化の効果が最大限に発揮されます。

#### 3.2.2 カプセル化に適したアクセス修飾子の選択

効果的なカプセル化のためのアクセス修飾子の選択ガイド：

1. **フィールド (データ)**
   * 通常は `private` を使用して、クラス外からの直接アクセスを防ぎます
   * サブクラスからのアクセスが必要な場合は `protected` を検討します
2. **メソッド**
   * 外部から呼び出される API メソッドは `public` にします
   * クラス内でのみ使用される補助メソッドは `private` にします
   * サブクラスでオーバーライドさせたいメソッドは `protected` を使用します
3. **コンストラクター**
   * 通常は `public` ですが、特定のパターン (シングルトンパターンなど) では `private` にする場合もあります

```java
public class Student {
    // データは private に保護
    private String name;
    private int id;
    private int[] grades;
    
    // 公開コンストラクター
    public Student( String name, int id ) {
        this.name = name;
        this.id = id;
        this.grades = new int[0];
    }
    
    // 公開 API メソッド
    public void addGrade( int grade ) {
        if (grade >= 0 && grade <= 100) {
            int[] newGrades = new int[grades.length + 1];
            System.arraycopy(grades, 0, newGrades, 0, grades.length);
            newGrades[grades.length] = grade;
            grades = newGrades;
        } else {
            System.out.println("無効な点数です。0〜100の範囲で入力してください。");
        }
    }
    
    public double calculateAverage() {
        if (grades.length == 0) return 0;
        
        int sum = 0;
        for (int grade : grades) {
            sum += grade;
        }
        return (double) sum / grades.length;
    }
    
    // クラス内でのみ使用する私的ヘルパーメソッド
    private boolean isValidGrade( int grade ) {
        return grade >= 0 && grade <= 100;
    }
    
    // データへのアクセサー (ゲッター)
    public String getName() {
        return name;
    }
    
    public int getId() {
        return id;
    }
    
    public int[] getGrades() {
        // 配列の直接参照を避けるため、コピーを返す
        return grades.clone();
    }
}
```

> 📝 **ポイント** 上記の例では、成績データ ( `grades` ) は直接変更できないように private になっています。`addGrade` メソッドを通じてのみ値を追加でき、その際にデータの検証 ( 0 - 100 の範囲チェック) も行います。これにより、不正な値が入ることを防いでいます。また、`getGrades` メソッドは配列のコピーを返すことで、外部からの変更を防いでいます。

### 3.3 ゲッターとセッターの実装

#### 3.3.1 基本的なゲッターとセッター

**ゲッター** (getter) と**セッター** (setter) は、カプセル化されたデータにアクセスするための標準的なメソッドです。

* **ゲッター** - データを取得するためのメソッド
* **セッター** - データを設定するためのメソッド

```java
public class Person {
    private String name;
    private int age;t
    
    // コンストラクター
    public Person( String name, int age ) {
        this.name = name;
        this.setAge(age);  // 検証を含むセッターを使用
    }
    
    // ゲッター
    public String getName() {
        return this.name;
    }
    
    public int getAge() {
        return this.age;
    }
    
    // セッター
    public void setName( String name ) {
        if (name != null && !name.trim().isEmpty()) {
            this.name = name;
        } else {
            System.out.println("名前を空にすることはできません。");
        }
    }
    
    public void setAge( int age ) {
        if (age >= 0 && age <= 120) {
            this.age = age;
        } else {
            System.out.println("年齢は0-120の範囲で設定してください。");
        }
    }
}
```

<details>

<summary>使用例</summary>

```java
public class PersonDemo {
    public static void main( String[] args ) {
        Person person = new Person( "田中太郎", 25 );
        
        // ゲッターを使用してデータを取得
        System.out.println("名前: " + person.getName());
        System.out.println("年齢: " + person.getAge());
        
        // セッターを使用してデータを変更
        person.setName("佐藤花子");
        person.setAge(30);
        
        System.out.println("変更後の名前: " + person.getName());
        System.out.println("変更後の年齢: " + person.getAge());
        
        // 無効なデータによるセッター呼び出し
        person.setName("");
        person.setAge(150);
        
        // 変更されていないことを確認
        System.out.println("検証後の名前: " + person.getName());
        System.out.println("検証後の年齢: " + person.getAge());
    }
}
```

</details>

> 📝 **ポイント** ゲッターとセッターを使用することで、データのフォーマット変更、検証、変換などの処理を一箇所で管理できます。また、将来的に内部実装を変更する際も、外部のコードに影響を与えずに変更できます。

### 3.4 カプセル化の実用例

#### 3.4.1 ユーザーアカウント管理

ユーザーアカウント情報は機密性が高く、適切なカプセル化が重要です。

```java
public class UserAccount {
    private String username;
    private String password;
    private String email;
    private boolean active;
    
    public UserAccount( String username, String password, String email ) {
        this.setUsername(username);
        this.setPassword(password);
        this.setEmail(email);
        this.active = true;
    }
    
    // ゲッター
    public String getUsername() {
        return this.username;
    }
    
    public String getEmail() {
        return this.email;
    }
    
    public boolean isActive() {
        return this.active;
    }
    
    // パスワードにはゲッターがない - 外部からの取得は不可
    
    // セッター
    public void setUsername( String username ) {
        if (username != null && username.matches("[a-zA-Z0-9_]{3,20}")) {
            this.username = username;
        } else {
            throw new IllegalArgumentException(
                "ユーザー名は3〜20文字の英数字とアンダースコアのみ使用可能です。");
        }
    }
    
    public void setPassword( String password ) {
        if (password != null && password.length() >= 8) {
            // 実際のアプリではパスワードをハッシュ化して保存
            this.password = password;
        } else {
            throw new IllegalArgumentException("パスワードは8文字以上必要です。");
        }
    }
    
    public void setEmail( String email ) {
        if (email != null && email.matches(".+@.+\\..+")) {
            this.email = email;
        } else {
            throw new IllegalArgumentException("有効なメールアドレスを入力してください。");
        }
    }
    
    public void setActive( boolean active ) {
        this.active = active;
    }
    
    // ログイン処理
    public boolean login( String inputPassword ) {
        if (this.active && this.password.equals(inputPassword)) {
            return true;
        }
        return false;
    }
}
```

この例では、パスワード情報への直接アクセスを防ぎつつ、各種データに対して適切な検証を行っています。また、`login` メソッドによって、パスワードを公開せずに認証機能を提供しています。

> 📝 **ポイント** セキュリティ関連のデータは特に厳格なカプセル化が必要です。このような情報には、ゲッターを提供せず、認証などの機能メソッドを通じてのみ間接的に使用するようにします。

***

### 演習問題

#### 問題 3-1: 基本的なカプセル化の実装

以下の仕様に基づいて、適切にカプセル化された 「Circle」 (円) クラスを作成してください。

* フィールド:
  * radius (半径、double 型) - 正の値である必要がある
* メソッド:
  * コンストラクター: 半径を受け取る
  * getRadius(): 半径を返す
  * setRadius(): 半径を設定 (値の検証を含む)
  * calculateArea(): 円の面積を計算して返す
  * calculateCircumference(): 円周の長さを計算して返す

```java
// Circleクラスを実装してください

```

使用例:

```java
public class CircleDemo {
    public static void main( String[] args ) {
        Circle circle = new Circle( 5.0 );
        
        System.out.println("半径: " + circle.getRadius());
        System.out.println("面積: " + circle.calculateArea());
        System.out.println("円周: " + circle.calculateCircumference());
        
        // 半径を変更
        circle.setRadius(7.5);
        System.out.println("新しい半径: " + circle.getRadius());
        System.out.println("新しい面積: " + circle.calculateArea());
        
        // 無効な半径の設定を試みる
        try {
            circle.setRadius(-2.0);
        } catch (IllegalArgumentException e) {
            System.out.println("エラー: " + e.getMessage());
        }
    }
}
```

<details>

<summary>ヒント</summary>

* `radius` フィールドに `private` 修飾子を使用してください
* `setRadius` メソッドでは、値が正であるかどうかを検証してください
* 面積の計算には `Math.PI * radius * radius` を使用します
* 円周の計算には `2 * Math.PI * radius` を使用します
* 無効な値が設定されようとした場合は例外をスローするか、エラーメッセージを表示してください

</details>

#### 問題 3-2: 商品在庫管理クラスの実装

オンラインショップの商品を表す「Product」クラスを作成してください。以下の仕様を満たすように実装してください。

* フィールド:
  * id（商品ID、int型）
  * name（商品名、String型）- 空であってはならない
  * price（価格、double型）- 0以上である必要がある
  * stock（在庫数、int型）- 0以上である必要がある
* メソッド:
  * コンストラクター: すべてのフィールドを初期化
  * ゲッターとセッター: すべてのフィールドに対して（適切な検証を含む）
  * addStock(int quantity): 在庫を増やす
  * removeStock(int quantity): 在庫を減らす（在庫不足の場合はfalseを返す）
  * getFormattedPrice(): "¥1,000"のような形式で価格を返す
  * displayInfo(): 商品情報を表示

<details>

<summary>ヒント</summary>

* すべてのフィールドに`private`修飾子を使用してください
* セッターでは、各フィールドに適切な検証を実装してください
* `addStock`と`removeStock`メソッドでも引数の検証を行ってください
* `getFormattedPrice`メソッドでは`String.format("%,d円", price)`などを使用して金額をフォーマットします

</details>

問題 3-3: 学生情報管理クラスの実装

大学の学生情報を管理する「Student」クラスを作成してください。以下の要件を満たすように実装してください。

* フィールド:
  * id（学生ID、String型）- 変更不可
  * name（氏名、String型）
  * gpa（成績平均値、double型）- 0.0〜4.0の範囲内
  * year（学年、int型）- 1〜4の範囲内
  * active（在学状態、boolean型）
* メソッド:
  * コンストラクター: id, name, year を受け取る（GPAは0.0、activeはtrueで初期化）
  * 適切なゲッターとセッター（ただしidは変更不可）
  * incrementYear(): 学年を1つ上げる（ただし4年生以上は変更なし）
  * updateGPA(double newGrade): 新しい成績を加味してGPAを更新する
  * displayStudentInfo(): 学生情報を表示する

```java
// ここにStudentクラスを実装してください
```

<details>

<summary>ヒント</summary>

* 学生IDは一度設定したら変更できないようにしましょう（setterを作成しない）
* GPAの更新は、例えば現在のGPAと新しい成績の平均を取るなどの計算で実装できます
* 学年の増加は最大値（4年）を超えないように注意してください
* データの検証を含むセッターを実装してください

</details>

#### 問題 3-4: 銀行システム

銀行口座情報を管理するクラスを作成してください。以下の要件を満たすように実装してください。

* 口座クラス（BankAccount）:
  * フィールド: accountNumber（口座番号）, ownerName（口座名義）, balance（残高）
  * メソッド:
    * コンストラクター: 口座番号と名義を受け取る（残高は0で初期化）
    * deposit(): 入金処理（金額が正の値であることを検証）
    * withdraw(): 引き出し処理（金額が正の値で、かつ残高以下であることを検証）
    * transfer(): 別の口座への送金処理
    * getAccountSummary(): 口座情報の概要を文字列で返す
* 当座預金口座クラス（CheckingAccount）（口座クラスを継承）:
  * 追加フィールド: overdraftLimit（当座貸越限度額）
  * withdraw()をオーバーライド: 残高+当座貸越限度額までの引き出しを許可

```java
// ここにBankAccountクラスとCheckingAccountクラスを実装してください
```

<details>

<summary>ヒント</summary>

* すべてのフィールドは`private`にして、必要なゲッターとセッターを提供してください
* 入金・引き出し・送金処理では、適切なバリデーションを行ってください
* 送金処理は、送金元口座からの引き出しと送金先口座への入金で構成されます
* 当座預金口座では、残高がマイナスになることも許可しますが、当座貸越限度額を超えることはできません

</details>

#### 問題 3-5: 社員管理システム

小さな会社の社員情報を管理するシステムを設計してください。以下のクラスを実装してください。

* 社員クラス（Employee）:
  * フィールド:
    * id（社員ID）
    * name（氏名）
    * department（部署）
    * salary（給与）
    * hireDate（入社日）
  * メソッド:
    * 適切なコンストラクター、ゲッター、セッター
    * calculateYearsOfService(): 勤続年数を計算
    * 給与を更新するメソッド（新しい給与が現在の給与より低くならないように検証）
* 社員管理クラス（EmployeeManager）:
  * フィールド: 社員のリスト
  * メソッド:
    * 社員の追加・削除
    * 社員の検索（ID、名前、部署などで）
    * 部署ごとの平均給与を計算
    * 全社員情報の表示

```java
import java.util.ArrayList;
import java.util.Date;

// ここにEmployeeクラスとEmployeeManagerクラスを実装してください
```

<details>

<summary>ヒント</summary>

* Dateクラスを使って入社日を管理します
* 勤続年数の計算には、現在の日付と入社日の差を使用します
* 社員管理クラスでは、ArrayListを使って社員リストを管理します
* 検索メソッドは、条件に一致する社員だけを含む新しいリストを返すと便利です
* 部署ごとの平均給与計算は、部署名をキーとするHashMapを使うと効率的です

</details>
