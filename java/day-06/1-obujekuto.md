# 1. オブジェクト指向入門

### 1.1 関連するデータのグループ化

#### 1.1.1 従来の変数による表現の限界

プログラミングでは様々な情報を扱いますが、関連する情報をバラバラの変数として管理すると、コードがすぐに複雑になってしまいます。

例えば、人の情報を表現する場合を考えてみましょう。

```java
// 別々の変数として管理する場合
String personName = "田中太郎";
int personAge = 25;
String personEmail = "tanaka@example.com";

// 別の人の情報
String anotherPersonName = "佐藤花子";
int anotherPersonAge = 30;
String anotherPersonEmail = "sato@example.com";
```

このようにデータが増えていくと、変数の名前を考えるのも大変になり、関連する情報の管理が難しくなります。さらに、これらの情報に対する処理も複雑になっていきます。

> 📝 **ポイント** 変数が多くなると命名規則の維持や関連性の追跡が難しくなります。また、関連データをまとめて渡すような処理が必要な場合、引数リストが長くなり、順序の間違いなどのバグが発生しやすくなります。

#### 1.1.2 カスタムデータ型としてのクラス

Javaのクラスは独自のデータ型を定義する仕組みです。基本型（int, char, boolean）だけでなく、プログラマーが必要とする「人」や「車」のような概念を型として表現できます。

```java
// 人の情報を一つの型にまとめたクラス
public class Person {
    // データメンバー（フィールド）
    String name;
    int age;
    String email;
}
```

クラスを定義すると、Javaの基本型と同じように新しい型として使えるようになります。つまり「人型」（Person型）の変数を宣言し、その中に関連情報をまとめて扱えます。

```java
// クラスを使用して情報を管理
Person person1 = new Person();
person1.name = "田中太郎";
person1.age = 25;
person1.email = "tanaka@example.com";

Person person2 = new Person();
person2.name = "佐藤花子";
person2.age = 30;
person2.email = "sato@example.com";
```

このように型を定義することで、関連データをまとまりとして扱え、コードの意図も明確になります。「人」を表す型として宣言することで、その変数が何を表しているのか一目でわかります。

> 📝 **ポイント** クラスは単なる情報の入れ物ではなく、新しいデータ型を定義するものです。これにより、問題領域に合わせた適切な抽象化が可能になります。

### 1.2 データと機能の結合

#### 1.2.1 情報と処理の一体化

クラスの強力な特徴は、情報（フィールド）だけでなく、その情報に対する操作（メソッド）も一緒に定義できることです。情報と処理を一つの単位として扱うことができます。

```java
public class Person {
    // フィールド
    String name;
    int age;
    String email;
    
    // メソッド
    public void displayInfo() {
        System.out.println("名前: " + name);
        System.out.println("年齢: " + age);
        System.out.println("メール: " + email);
    }
    
    public void celebrateBirthday() {
        age++;
        System.out.println(name + "さんの誕生日おめでとう！" + age + "歳になりました。");
    }
}
```

使用例：

```java
public class PersonTest {
    public static void main( String[] args ) {
        Person person1 = new Person();
        person1.name = "田中太郎";
        person1.age = 25;
        person1.email = "tanaka@example.com";
        
        // メソッドを呼び出して情報を表示
        person1.displayInfo();
        
        // 誕生日を祝うメソッドを呼び出し
        person1.celebrateBirthday();
    }
}
```

実行結果：

```
名前: 田中太郎
年齢: 25
メール: tanaka@example.com
田中太郎さんの誕生日おめでとう！26歳になりました。
```

> 📝 **ポイント** データと、そのデータを操作する機能を一つの単位にまとめることで、コードの論理的なまとまりが良くなります。データがどのように使われるべきかが、クラス内に定義されるため、使い方の一貫性も保たれます。

#### 1.2.2 コンストラクターによる初期化

オブジェクトを作成する際に、初期データを設定するための特別なメソッドをコンストラクターと呼びます。コンストラクターを使うことで、オブジェクト作成時に必要な初期設定を一度に行えます。

```java
public class Person {
    // フィールド
    String name;
    int age;
    String email;
    
    // コンストラクター - クラス名と同じ名前を持つ特別なメソッド
    public Person( String name, int age, String email ) {
        this.name = name;     // this.name はフィールド、name は引数
        this.age = age;
        this.email = email;
    }
    
    // 表示用メソッド
    void displayInfo() {
        System.out.println("名前: " + name);
        System.out.println("年齢: " + age);
        System.out.println("メール: " + email);
    }
}
```

使用例：

```java
public class PersonTest {
    public static void main( String[] args ) {
        // コンストラクターを使ってオブジェクト作成と同時にデータを設定
        Person person1 = new Person( "田中太郎", 25, "tanaka@example.com" );
        Person person2 = new Person( "佐藤花子", 30, "sato@example.com" );
        
        // データ表示
        person1.displayInfo();
        person2.displayInfo();
    }
}
```

> 📝 **ポイント** コンストラクターを使うと、オブジェクト作成時に必要な初期値をすぐに設定できます。`new Person(...)` の括弧内に初期値を指定します。これにより、オブジェクトが常に有効な状態で作成されることを保証できます。

### 1.3 クラスとオブジェクトの関係

#### 1.3.1 設計図と実体の概念

クラスとオブジェクトの関係を理解するために、「設計図と実体」というアナロジーがよく使われます。

* **クラス**は型の定義です。例えば「Bookとはどんな情報を持つか」を定めたものです。
* **オブジェクト**はその型の実際のデータです。例えば「Java入門」という具体的な一冊の本です。

```java
// Bookクラス（設計図）
public class Book {
    String title;
    String author;
    int pageCount;
    
    // コンストラクター
    public Book( String title, String author, int pageCount ) {
        this.title = title;
        this.author = author;
        this.pageCount = pageCount;
    }
    
    void displayInfo() {
        System.out.println("タイトル: " + title);
        System.out.println("著者: " + author);
        System.out.println("ページ数: " + pageCount);
    }
}
```

```java
public class BookDemo {
    public static void main( String[] args ) {
        // Bookクラスから複数のオブジェクトを作成（実体）
        Book book1 = new Book( "Java入門", "山田太郎", 300 );
        Book book2 = new Book( "データベースの基礎", "佐藤花子", 250 );
        
        // それぞれのオブジェクトは独立したデータを持つ
        book1.displayInfo();
        System.out.println("--------");
        book2.displayInfo();
    }
}
```

実行結果：

```
タイトル: Java入門
著者: 山田太郎
ページ数: 300
--------
タイトル: データベースの基礎
著者: 佐藤花子
ページ数: 250
```

> 📝 **ポイント** 同じクラス（設計図）から複数の異なるオブジェクト（実体）を作成できます。各オブジェクトは同じ構造（フィールドとメソッド）を持ちますが、それぞれ独自のデータ値を持ちます。

#### 1.3.2 オブジェクト間の連携

オブジェクト指向プログラミングの強みは、オブジェクト同士が連携して動作できることです。現実世界の関係性をプログラム内で表現できます。

```java
// 学生クラス
public class Student {
    String name;
    int id;
    
    public Student( String name, int id ) {
        this.name = name;
        this.id = id;
    }
    
    void displayInfo() {
        System.out.println("学生名: " + name);
        System.out.println("学生ID: " + id);
    }
}

// コースクラス
public class Course {
    String courseName;
    Student[] enrolledStudents;
    int studentCount;
    
    public Course( String courseName, int maxStudents ) {
        this.courseName = courseName;
        this.enrolledStudents = new Student[maxStudents];
        this.studentCount = 0;
    }
    
    // 学生をコースに登録するメソッド
    void enrollStudent( Student student ) {
        if (studentCount < enrolledStudents.length) {
            enrolledStudents[studentCount] = student;
            studentCount++;
            System.out.println(student.name + "さんを" + courseName + "に登録しました。");
        } else {
            System.out.println("コースの定員に達しています。");
        }
    }
    
    // 登録された学生一覧を表示するメソッド
    void displayEnrolledStudents() {
        System.out.println(courseName + "の登録学生一覧:");
        for (int i = 0; i < studentCount; i++) {
            System.out.println("- " + enrolledStudents[i].name + " (ID: " + enrolledStudents[i].id + ")");
        }
    }
}
```

使用例：

```java
public class SchoolDemo {
    public static void main( String[] args ) {
        // 学生オブジェクトを作成
        Student student1 = new Student( "田中太郎", 1001 );
        Student student2 = new Student( "佐藤花子", 1002 );
        Student student3 = new Student( "鈴木一郎", 1003 );
        
        // コースオブジェクトを作成
        Course javaCourse = new Course( "Java プログラミング", 30 );
        
        // 学生をコースに登録
        javaCourse.enrollStudent(student1);
        javaCourse.enrollStudent(student2);
        
        // 登録された学生を表示
        javaCourse.displayEnrolledStudents();
    }
}
```

実行結果：

```
田中太郎さんをJava プログラミングに登録しました。
佐藤花子さんをJava プログラミングに登録しました。
Java プログラミングの登録学生一覧:
- 田中太郎 (ID: 1001)
- 佐藤花子 (ID: 1002)
```

> 📝 **ポイント** オブジェクト指向プログラミングでは、オブジェクトが他のオブジェクトを参照したり、操作したりできます。これにより、現実世界のような関係性（学生とコースの関係など）をプログラムで表現できます。オブジェクト間の連携が、複雑なシステムを構築する上での基盤となります。

### 1.4 オブジェクト指向の利点

#### 1.4.1 コードの整理と再利用

オブジェクト指向プログラミングには多くの利点があります。特に重要なのは、コードの整理と再利用性の向上です。

1. **コードの整理** 関連する情報と処理がひとつの場所にまとまるため、コードが整理されます。「何がどこにあるか」が明確になり、プログラムの構造が理解しやすくなります。
2. **再利用性** 一度作成したクラスは異なる場所やプロジェクトで再利用できます。例えば、よく使う「住所」や「日付」のクラスを作れば、それを様々なプログラムで使い回せます。

#### 1.4.2 保守性と拡張性

システム開発では、作成した後のメンテナンスや機能追加も重要な要素です。オブジェクト指向はこれらの面でも優れています。

1. **保守性** クラス内部の実装を変更しても、そのクラスを使用するコードに影響を与えにくいです。これにより、バグ修正や機能改善が容易になります。
2. **拡張性** 新しい機能を追加する際に、既存のコードを変更せずに済むことが多いです。新しいクラスを追加するだけで機能拡張ができるため、既存機能への影響を最小限に抑えられます。

> 📝 **ポイント** オブジェクト指向プログラミングは、大規模なプログラム開発や、チームでの開発において特に効果を発揮します。複雑なシステムを管理しやすい形で設計できるため、長期的なプロジェクトでは特に重要です。

***

#### 演習問題

**問題 1-1: 基本的なクラスの作成**

人 ( Person ) を表すクラスを作成してください。以下のフィールドとメソッドを含めます。

* フィールド: name（名前）, age（年齢）
* コンストラクター: 名前と年齢を受け取るコンストラクター
* メソッド: introduce()（自己紹介を表示する）

```java
java// ここにPersonクラスを作成してください
```

使用例:

```java
public class PersonDemo {
    public static void main( String[] args ) {
        Person person1 = new Person( "山田太郎", 25 );
        person1.introduce();  // "私の名前は山田太郎です。25歳です。" と表示
    }
}
```

<details>

<summary>ヒント</summary>

* クラス定義には `public class クラス名 { }` を使用します
* コンストラクターはクラス名と同じ名前のメソッドです
* フィールドと引数の名前が同じ場合は、`this.フィールド名` を使ってフィールドを参照します

</details>

**問題 1-2: 計算機クラスの実装**

簡単な計算機 ( Calculator ) クラスを作成してください。以下の機能を実装します。

* フィールドなし
* メソッド:
  * add( int a, int b ): 2つの整数の和を返す
  * subtract( int a, int b ): aからbを引いた値を返す
  * multiply( int a, int b ): 2つの整数の積を返す
  * divide( int a, int b ): aをbで割った値を返す（bが0の場合は0を返す）

```java
java// ここにCalculatorクラスを作成してください
```

使用例:

```java
public class CalculatorDemo {
    public static void main( String[] args ) {
        Calculator calc = new Calculator();
        
        System.out.println("5 + 3 = " + calc.add( 5, 3 ));
        System.out.println("5 - 3 = " + calc.subtract( 5, 3 ));
        System.out.println("5 * 3 = " + calc.multiply( 5, 3 ));
        System.out.println("6 / 3 = " + calc.divide( 6, 3 ));
        System.out.println("5 / 0 = " + calc.divide( 5, 0 ));  // 0を返す
    }
}
```

<details>

<summary>ヒント</summary>

* 戻り値のあるメソッドは `return` を使って値を返します
* 除算は特別な処理が必要です（0で割る場合）
* 条件分岐には `if` 文を使います

</details>

**問題 1-3: 商品クラスの作成**

オンラインショップの商品 ( Product ) を表すクラスを作成してください。

* フィールド:
  * id (商品ID)
  * name (商品名)
  * price (価格)
  * stock (在庫数)
* コンストラクター:
  * 全てのフィールドを初期化するコンストラクター
* メソッド:
  * displayInfo(): 商品情報を表示
  * isAvailable(): 在庫があるかどうかを真偽値で返す
  * sell( int quantity ): 指定数量を販売する（在庫から減らす）。在庫不足の場合はメッセージを表示

```java
java// ここにProductクラスを作成してください
```

使用例:

```java
public class ProductDemo {
    public static void main( String[] args ) {
        Product product = new Product( 1, "ノートパソコン", 80000, 5 );
        
        product.displayInfo();
        
        System.out.println("商品は利用可能: " + product.isAvailable());
        
        // 3台販売
        product.sell( 3 );
        product.displayInfo();
        
        // さらに3台販売（在庫不足）
        product.sell( 3 );
    }
}
```

<details>

<summary>ヒント</summary>

* `isAvailable()` メソッドは在庫が1以上あれば `true` を返します
* `sell()` メソッドでは在庫数を減らす前に、十分な在庫があるかチェックします
* 在庫不足の場合はエラーメッセージを表示します

</details>

**問題 1-4: 書籍と図書館の実装**

書籍 ( Book ) と図書館 ( Library ) のクラスを作成してください。

1. Book クラス:
   * フィールド: title, author
   * コンストラクター: タイトルと著者を受け取る
   * メソッド: getInfo() - タイトルと著者の情報を文字列で返す
2. Library クラス:
   * フィールド:
     * name (図書館名)
     * books (Book型の配列、最大100冊)
     * bookCount (現在の蔵書数)
   * コンストラクター: 図書館名を受け取る
   * メソッド:
     * addBook( Book book ): 蔵書に本を追加
     * findBooksByAuthor( String author ): 指定著者の本を検索して表示

```java
java// ここにBookクラスとLibraryクラスを作成してください
```

使用例:

```java
public class LibraryDemo {
    public static void main( String[] args ) {
        // 図書館を作成
        Library library = new Library( "中央図書館" );
        
        // 数冊の本を追加
        library.addBook( new Book( "Java入門", "山田太郎" ) );
        library.addBook( new Book( "データベースの基礎", "佐藤花子" ) );
        library.addBook( new Book( "Webプログラミング", "山田太郎" ) );
        library.addBook( new Book( "アルゴリズム入門", "田中二郎" ) );
        
        // 著者で検索
        library.findBooksByAuthor( "山田太郎" );
    }
}
```

<details>

<summary>ヒント</summary>

* Bookクラスはシンプルなデータ保持クラスです
* Libraryクラスのコンストラクターでは、空の配列を作成します: `this.books = new Book[100];`
* addBookメソッドでは、現在の冊数を追跡する変数を増やしていきます
* findBooksByAuthorメソッドでは、すべての本をループして著者を比較します

</details>

**問題 1-5: オンラインショッピングカートの実装**

オンラインショッピングカートを表すクラスを作成しましょう。

1. CartItem クラス (個々のカート内商品):
   * フィールド: product (Product型), quantity (数量)
   * コンストラクター: 商品と数量を受け取る
   * メソッド:
     * getTotalPrice(): 小計（価格×数量）を返す
     * displayInfo(): 商品名、単価、数量、小計を表示
2. ShoppingCart クラス:
   * フィールド:
     * items (CartItem型の配列)
     * itemCount (アイテム数)
   * コンストラクター: 空のカートを作成（最大20アイテム）
   * メソッド:
     * addItem( Product product, int quantity ): カートに商品を追加
     * displayCart(): カートの内容と合計金額を表示
     * checkout(): 購入処理（在庫を減らし、カートを空にする）

```java
// 前の問題のProductクラスを使用します
// ここにCartItemクラスとShoppingCartクラスを作成してください
```

使用例:

```java
public class ShoppingTest {
    public static void main( String[] args ) {
        // 商品を作成
        Product laptop = new Product( 1, "ノートパソコン", 80000, 5 );
        Product phone = new Product( 2, "スマートフォン", 60000, 10 );
        Product tablet = new Product( 3, "タブレット", 40000, 8 );
        
        // ショッピングカートを作成
        ShoppingCart cart = new ShoppingCart();
        
        // カートに商品を追加
        cart.addItem( laptop, 1 );
        cart.addItem( phone, 2 );
        
        // カートの内容を表示
        cart.displayCart();
        
        // 購入処理
        cart.checkout();
        
        // 在庫の確認
        laptop.displayInfo();
        phone.displayInfo();
    }
}
```

<details>

<summary>ヒント</summary>

* CartItemクラスは商品とその数量の組み合わせを表します
* ShoppingCartクラスはCartItemの配列を管理します
* カートに同じ商品を追加する場合は、既存のアイテムの数量を増やすか、新しいアイテムとして追加するかを選べます（この問題では新しいアイテムとして追加してください）
* checkout()メソッドでは、各商品の在庫を減らし、カート内の商品をクリアします

</details>
