# 4. 抽象クラス

### 4.1 抽象クラスの基本概念

#### 4.1.1 抽象クラスが必要になる場面

継承を使ってプログラムを設計していると、 「基本的な枠組みは共通だが、具体的な実装はサブクラスによって異なる」 という状況がよく発生します。

例えば、人を表す Person クラスを考えてみましょう。すべての人に共通する情報 (名前、年齢など) は同じですが、 「挨拶をする」 という行動は人によって異なります。学生なら 「こんにちは、学生の〇〇です」 、教師なら 「こんにちは、〇〇先生です」 といった具合です。

```java
// 通常のクラスでの実装例 (問題がある) 
public class Person {
    protected String name;
    protected int age;
    
    public Person( String name, int age ) {
        this.name = name;
        this.age = age;
    }
    
    // この実装は意味がない
    public void greet() {
        System.out.println("挨拶をします。");  // 具体的でない
    }
    
    public void displayInfo() {
        System.out.println("名前: " + this.name + ", 年齢: " + this.age);
    }
}
```

この例では、`greet()` メソッドの実装が抽象的すぎて、実際には使い物になりません。しかし、すべての Person のサブクラスで挨拶の機能は必要です。

> 📝 **ポイント** スーパークラスでは 「どのようなメソッドが必要か」 は分かるが、 「具体的にどう実装するか」 はサブクラスに任せたい場合があります。このような場合に抽象クラスが有効です。

#### 4.1.2 抽象クラスとは

**抽象クラス**は、一部のメソッドが具体的な実装を持たない (抽象メソッド) クラスです。抽象クラス自体はインスタンス化 (オブジェクト作成) できませんが、継承して具体的な実装を提供することで使用できます。

```java
public abstract class Person {
    protected String name;
    protected int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 抽象メソッド - 実装はサブクラスに委ねる
    public abstract void greet();
    
    // 通常のメソッド - 共通の実装を提供
    public void displayInfo() {
        System.out.println("名前: " + this.name + ", 年齢: " + this.age);
    }
    
    // ゲッター
    public String getName() {
        return this.name;
    }
    
    public int getAge() {
        return this.age;
    }
}
```

抽象クラスの特徴：

1. `abstract` キーワードでクラスを宣言
2. 抽象メソッドを含むことができる ( `abstract` を付けて宣言し、実装は書かない)
3. 通常のメソッドも含むことができる
4. 直接インスタンス化はできない
5. コンストラクターを持つことができる

> 📝 **ポイント** 抽象クラスは 「完全ではないクラス」 です。設計図として機能するが、そのままでは使用できず、継承して完成させる必要があります。

### 4.2 抽象クラスの実装と使用

#### 4.2.1 抽象クラスを継承する

抽象クラスを継承するクラスは、すべての抽象メソッドを実装する必要があります。

```java
// 学生クラス - Personを継承
public class Student extends Person {
    private String studentId;
    private String major;
    
    public Student(String name, int age, String studentId, String major) {
        super(name, age);  // スーパークラスのコンストラクターを呼び出し
        this.studentId = studentId;
        this.major = major;
    }
    
    // 抽象メソッドの実装 (必須) 
    @Override
    public void greet() {
        System.out.println("こんにちは！学生の" + this.name + "です。");
        System.out.println("専攻は" + this.major + "です。");
    }
    
    // 学生特有のメソッド
    public void study() {
        System.out.println(this.name + "は勉強中です。");
    }
    
    public String getStudentId() {
        return this.studentId;
    }
    
    public String getMajor() {
        return this.major;
    }
}
```

```java
// 教師クラス - Person を継承
public class Teacher extends Person {
    private String subject;
    private int experience;
    
    public Teacher(String name, int age, String subject, int experience) {
        super(name, age);
        this.subject = subject;
        this.experience = experience;
    }
    
    // 抽象メソッドの実装 (必須) 
    @Override
    public void greet() {
        System.out.println("こんにちは。" + this.subject + "担当の" + this.name + "です。");
        System.out.println("教師歴" + this.experience + "年です。");
    }
    
    // 教師特有のメソッド
    public void teach() {
        System.out.println(this.name + "先生が" + this.subject + "を教えています。");
    }
    
    public String getSubject() {
        return this.subject;
    }
    
    public int getExperience() {
        return this.experience;
    }
}
```

> 📝 **ポイント** 抽象クラスを継承したクラスは、すべての抽象メソッドを実装しなければコンパイルエラーになります。これにより、必要なメソッドの実装漏れを防ぐことができます。

#### 4.2.2 抽象クラスの使用例

```java
import java.util.ArrayList;
import java.util.List;

public class PersonDemo {
    public static void main(String[] args) {
        // Person person = new Person("太郎", 20);  // エラー！抽象クラスはインスタンス化できない
        
        // 具体的なクラスのインスタンスを作成
        Student student = new Student("田中太郎", 20, "S001", "コンピュータ科学");
        Teacher teacher = new Teacher("佐藤花子", 35, "数学", 10);
        
        // 共通のメソッドを呼び出し
        student.displayInfo();
        student.greet();
        
        System.out.println();
        
        teacher.displayInfo();
        teacher.greet();
        
        System.out.println();
        
        // ポリモーフィズムの活用
        List<Person> people = new ArrayList<>();
        people.add(student);
        people.add(teacher);
        
        System.out.println("全員に挨拶してもらいます:");
        for (Person person : people) {
            person.greet();  // それぞれ異なる実装が実行される
            System.out.println("---");
        }
    }
}
```

実行結果:

```
名前: 田中太郎, 年齢: 20
こんにちは！学生の田中太郎です。
専攻はコンピュータ科学です。

名前: 佐藤花子, 年齢: 35
こんにちは。数学担当の佐藤花子です。
教師歴10年です。

全員に挨拶してもらいます:
こんにちは！学生の田中太郎です。
専攻はコンピュータ科学です。
---
こんにちは。数学担当の佐藤花子です。
教師歴10年です。
---
```

> 📝 **ポイント** 抽象クラスを使うことで、異なるクラスのオブジェクトを同じ型 (Person) として扱いながら、それぞれ異なる実装を実行させることができます。これをポリモーフィズム (多態性) と呼びます。

### 4.3 抽象クラスの設計パターン ⭐

#### 4.3.1 テンプレートメソッドパターン

抽象クラスでよく使われるパターンの一つが 「テンプレートメソッドパターン」 です。これは、処理の流れをスーパークラスで定義し、具体的な処理内容をサブクラスで実装するパターンです。

```java
public abstract class Person {
    protected String name;
    protected int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // テンプレートメソッド - 処理の流れを定義
    public final void introduceSelf() {
        System.out.println("=== 自己紹介開始 ===");
        greet();                    // 抽象メソッド
        sharePersonalInfo();        // 抽象メソッド
        System.out.println("=== 自己紹介終了 ===");
    }
    
    // 抽象メソッド - サブクラスで実装
    protected abstract void greet();
    protected abstract void sharePersonalInfo();
}
```

```java
public class Student extends Person {
    private String major;
    
    public Student(String name, int age, String major) {
        super(name, age);
        this.major = major;
    }
    
    @Override
    protected void greet() {
        System.out.println("こんにちは！学生の" + this.name + "です。");
    }
    
    @Override
    protected void sharePersonalInfo() {
        System.out.println("専攻: " + this.major);
    }
}
```

使用例:

```java
public class TemplatePatternDemo {
    public static void main(String[] args) {
        Student student = new Student("山田太郎", 21, "情報工学");
        student.introduceSelf();
    }
}
```

実行結果:

```
=== 自己紹介開始 ===
こんにちは！学生の山田太郎です。
専攻: 情報工学
=== 自己紹介終了 ===
```

> 📝 **ポイント** テンプレートメソッドパターンでは、`introduceSelf()` メソッドに `final` を付けることで、サブクラスでのオーバーライドを防いでいます。これにより、処理の流れを固定し、具体的な内容だけをサブクラスに委ねることができます。

#### 4.3.2 Webアプリケーションでの活用例

抽象クラスは、Webアプリケーション開発でも有効に活用できます。

```java
public abstract class BaseServlet extends HttpServlet {
    
    // テンプレートメソッド
    @Override
    public final void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        System.out.println("リクエスト処理開始");
        try {
            handleGetRequest(request, response);  // 抽象メソッド
        } finally {
            System.out.println("リクエスト処理終了");
        }
    }
    
    // サブクラスで実装する抽象メソッド
    protected abstract void handleGetRequest(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException;
}
```

```java
@WebServlet("/users")
public class UserServlet extends BaseServlet {
    
    @Override
    protected void handleGetRequest(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // ユーザー一覧表示の処理
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>ユーザー一覧</h1>");
        out.println("<ul>");
        out.println("<li>ユーザー1</li>");
        out.println("<li>ユーザー2</li>");
        out.println("</ul>");
    }
}
```

> 📝 **ポイント** この例では、すべての Servlet で共通する処理を抽象クラスにまとめています。各 Servlet は具体的なリクエスト処理だけを実装すればよく、共通処理の重複を避けることができます。

### 4.4 抽象クラスと通常のクラスの使い分け

#### 4.4.1 抽象クラスを使うべき場面

以下のような場面では抽象クラスが適しています：

1. **共通の属性やメソッドがあるが、一部のメソッドはサブクラスで異なる実装が必要**
2. **処理の流れは共通だが、具体的な処理内容はサブクラスに依存する**
3. **継承関係において、スーパークラス単体では意味をなさない**
4. **サブクラスに特定のメソッドの実装を強制したい**

```java
// 適用例：図形クラス
public abstract class Shape {
    protected String color;
    
    public Shape( String color ) {
        this.color = color;
    }
    
    // 共通メソッド
    public String getColor() {
        return this.color;
    }
    
    // 抽象メソッド - 図形によって計算方法が異なる
    public abstract double calculateArea();
    public abstract double calculatePerimeter();
    
    // テンプレートメソッド
    public final void displayShapeInfo() {
        System.out.println("図形情報:");
        System.out.println("色: " + this.color);
        System.out.println("面積: " + calculateArea());
        System.out.println("周囲長: " + calculatePerimeter());
    }
}
```

#### 4.4.2 通常のクラスを使うべき場面

以下のような場面では通常のクラスが適しています：

1. **そのクラス単体で意味があり、インスタンス化して使用する**
2. **すべてのメソッドに具体的な実装を提供できる**
3. **継承されることを前提としていない**

```java
// 通常のクラスの例：ユーティリティクラス
public class MathUtils {
    
    public static double calculateCircleArea( double radius ) {
        return Math.PI * radius * radius;
    }
    
    public static double calculateRectangleArea( double width, double height ) {
        return width * height;
    }
    
    public static double calculateDistance( double x1, double y1, double x2, double y2 ) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
    }
}
```

> 📝 **ポイント** 抽象クラスは 「is-a」 関係 (〜は〜である) があり、かつスーパークラス単体では不完全な場合に使用します。通常のクラスは、それ単体で完結した機能を提供する場合に使用します。

***

### 演習問題

#### 問題 4-1: 基本的な抽象クラスの実装

以下の仕様に従って、抽象クラス 「Animal」 とそのサブクラスを作成してください。

1. Animal 抽象クラス:
   * フィールド: name (名前) , age (年齢)
   * コンストラクター: 名前と年齢を受け取る
   * 抽象メソッド: makeSound() (鳴き声) , move() (移動方法)
   * 通常のメソッド: displayInfo() (動物の基本情報を表示)
2. Dog クラス (Animalを継承) :
   * 追加フィールド: breed (犬種)
   * makeSound(): "ワンワン！"
   * move(): "四本足で走ります"
3. Bird クラス (Animalを継承) :
   * 追加フィールド: wingspan (翼の幅)
   * makeSound(): "チュンチュン！"
   * move(): "空を飛びます"

```java
// ここにAnimal抽象クラスとそのサブクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 抽象クラスには `abstract` キーワードを使用します
* 抽象メソッドにも `abstract` キーワードを使用し、実装は書きません
* サブクラスでは、すべての抽象メソッドを `@Override` で実装する必要があります
* 抽象クラスのコンストラクターは `super()` で呼び出します

</details>

#### 問題 4-2: テンプレートメソッドパターンの実装

料理のレシピを表現する抽象クラスを作成してください。

1. Recipe 抽象クラス:
   * フィールド: dishName (料理名)
   * コンストラクター: 料理名を受け取る
   * テンプレートメソッド: cook() (調理全体の流れを定義)
   * 抽象メソッド: prepareIngredients() (材料準備) , cookMain() (主要調理) , garnish() (盛り付け)
   * 通常のメソッド: serveDish() (料理を提供)
2. PastaRecipe クラス:
   * パスタ料理のレシピを実装
3. SaladRecipe クラス:
   * サラダのレシピを実装

cook()メソッドでは以下の順序で処理を行ってください：

1. 材料準備
2. 主要調理
3. 盛り付け
4. 料理提供

```java
// ここに Recipe 抽象クラスとそのサブクラスを実装してください

```

<details>

<summary>ヒント</summary>

* テンプレートメソッドには `final` を付けて、サブクラスでのオーバーライドを防ぎます
* 各工程をメソッドとして分離し、抽象メソッドとして定義します
* サブクラスでは、それぞれの工程に応じた具体的な処理を実装します

</details>

#### 問題 4-3: 図形計算システム

図形の面積と周囲長を計算するシステムを、抽象クラスを使って実装してください。

1. Shape 抽象クラス:
   * フィールド: color (色)
   * 抽象メソッド: calculateArea() (面積計算) , calculatePerimeter() (周囲長計算)
   * テンプレートメソッド: displayShapeInfo() (図形情報の表示)
2. Rectangle クラス:
   * フィールド: width (幅) , height (高さ)
3. Circle クラス:
   * フィールド: radius (半径)
4. Triangle クラス:
   * フィールド: side1, side2, side3 (三辺の長さ)

各図形クラスで適切な面積と周囲長の計算を実装してください。

```java
// ここにShape抽象クラスとそのサブクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 長方形の面積: width × height、周囲長: 2 × (width + height)
* 円の面積: π × radius²、周囲長: 2 × π × radius
* 三角形の面積: ヘロンの公式を使用、周囲長: side1 + side2 + side3
* Math.PIとMath.sqrtメソッドを活用してください

</details>

#### 問題 4-4: 従業員管理システム

会社の従業員を管理するシステムを抽象クラスを使って設計してください。

1. Employee 抽象クラス:
   * フィールド: name (名前) , id (社員ID) , baseSalary (基本給)
   * 抽象メソッド: calculateSalary() (給与計算)
   * 通常のメソッド: displayEmployeeInfo() (従業員情報表示)
2. FullTimeEmployee クラス:
   * 追加フィールド: bonus (賞与)
   * 給与計算: 基本給 + 賞与
3. PartTimeEmployee クラス:
   * 追加フィールド: hoursWorked (勤務時間) , hourlyRate (時給)
   * 給与計算: 勤務時間 × 時給

```java
import java.util.ArrayList;
import java.util.List;

// ここにEmployee抽象クラスとそのサブクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 各従業員タイプによって給与計算方法が異なります
* displayEmployeeInfo()では、基本情報と計算された給与を表示します
* ArrayListを使って従業員リストを管理できます

</details>

#### 問題 4-5: ゲームキャラクターシステム

RPG ゲームのキャラクターシステムを抽象クラスを使って実装してください。

1. GameCharacter 抽象クラス:
   * フィールド: name (名前) , level (レベル) , hp (体力)
   * 抽象メソッド: attack() (攻撃) , useSpecialSkill() (特殊技能)
   * 通常のメソッド: displayStatus() (ステータス表示)
2. Warrior クラス (戦士) :
   * 追加フィールド: strength (力)
   * 攻撃: 力に基づく物理攻撃
   * 特殊技能: 「強撃」
3. Mage クラス (魔法使い) :
   * 追加フィールド: intelligence (知力)
   * 攻撃: 知力に基づく魔法攻撃
   * 特殊技能: 「ファイアボール」

```java
import java.util.ArrayList;
import java.util.List;

// ここにGameCharacter抽象クラスとそのサブクラスを実装してください

```

<details>

<summary>ヒント</summary>

* 各キャラクターのタイプによって攻撃方法と特殊技能が異なります
* 攻撃力の計算は、各キャラクターの特徴的な能力値を基準にします
* ArrayList を使ってパーティを管理できます

</details>
