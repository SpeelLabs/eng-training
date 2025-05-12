# 5. ソースファイルの分割とパッケージ

### 5.1 ソースファイルの分割

これまでは、すべてのコードを1つのファイルに記述してきました。しかし、プログラムが大きくなるにつれて、すべてのコードを1つのファイルに保存することは難しくなります。ここでは、ソースコードを複数のファイルに分割する方法を学びます。

#### 5.1.1 ソースファイル分割の目的

ソースファイルを分割する主な目的は以下の通りです：

1. **コードの整理**: 関連する機能ごとにファイルを分けることで、コードが整理され、見つけやすくなります
2. **再利用性の向上**: 特定の機能を持つクラスを独立したファイルにすることで、他のプロジェクトでも再利用しやすくなります
3. **保守性の向上**: ファイルを分割することで、修正や機能追加が特定のファイルに限定され、他のコードへの影響を最小限に抑えられます
4. **チーム開発の効率化**: 複数の開発者が異なるファイルを同時に編集できるようになります

#### 5.1.2 クラスごとにファイルを分割する

Java では、基本的に**1つのパブリッククラスに対して1つのソースファイル**を作成します。ファイル名はパブリッククラスの名前と同じにする必要があります。

```java
// すべてのクラスを1つのファイルに入れる方法（良くない例）
public class MainProgram {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        int result = calc.add(5, 3);
        System.out.println("5 + 3 = " + result);
    }
}

class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
}
```

例えば、これまでのような1つのファイルにすべてのクラスを入れる代わりに、次のように、クラスごとに別のファイルに分けます：

**MainProgram.java**

```java
public class MainProgram {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        int result = calc.add(5, 3);
        System.out.println("5 + 3 = " + result);
    }
}
```

**Calculator.java**

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
}
```

> 📝 **ポイント**: ファイル名には必ず `.java` という拡張子を付けます。また、ファイル名は大文字小文字を含めて、クラス名と完全に一致する必要があります。

#### 5.1.3 ソースファイルを分割する際の注意点

1. **パブリッククラスは1ファイルに1つだけ**: 1つのソースファイルには、`public` 修飾子を持つクラスは1つだけにする必要があります
2. **ファイル名はパブリッククラス名と一致**: パブリッククラスのあるファイルは、クラス名と同じ名前にする必要があります
3. **デフォルトアクセス（修飾子なし）のクラス**: 同一パッケージ内からのみアクセス可能です

```java
// Calculator.java
public class Calculator {  // publicクラス - ファイル名はCalculator.javaにする必要あり
    // ...
}

class Helper {  // デフォルトアクセスのクラス - 同じファイルに定義可能
    // ...
}
```

### 5.2 パッケージの基本

**パッケージ**は、関連するクラスをグループ化する仕組みです。パッケージを使用することで、名前の衝突を避け、コードをより良く整理することができます。

#### 5.2.1 パッケージの目的

パッケージを使用する主な目的は以下の通りです：

1. **名前空間の提供**: 同じ名前のクラスでも異なるパッケージに配置できる
2. **アクセス制御**: パッケージプライベートなメンバーを定義できる
3. **コードの整理**: 関連するクラスをグループ化できる
4. **配布の容易さ**: ライブラリとして配布する場合に便利

#### 5.2.2 パッケージの命名規則

パッケージ名は通常、すべて小文字で記述し、ドメイン名を逆にした形で始まることが多いです。これにより、世界中で一意のパッケージ名を保証します。

例えば、会社のドメインが `example.com` で、`calculator` というアプリケーションを開発している場合：

```
com.example.calculator
```

このような形式でパッケージ名を付けます。

サブパッケージを作成することもできます：

```
com.example.calculator.basic  // 基本的な計算機能
com.example.calculator.advanced  // 高度な計算機能
```

#### 5.2.3 パッケージの宣言

ソースファイルの最初に `package` 文を追加することで、そのクラスがどのパッケージに属するかを宣言します：

```java
package com.example.calculator;

public class Calculator {
    // ...
}
```

> ⚠️ **注意**: `package` 文はファイルの最初に記述する必要があり、それより前にはコメントやドキュメンテーションコメント以外は記述できません。

#### 5.2.4 ディレクトリ構造とパッケージ

パッケージ名はディレクトリ構造に対応します。例えば、`com.example.calculator` パッケージのクラスは、次のディレクトリ構造で配置する必要があります：

```
src/
└── com/
    └── example/
        └── calculator/
            ├── Calculator.java
            ├── MainProgram.java
            └── ...
```

`com.example.calculator.basic` パッケージは：

```
src/
└── com/
    └── example/
        └── calculator/
            ├── basic/
            │   ├── BasicCalculator.java
            │   └── ...
            ├── Calculator.java
            └── ...
```

> 📝 **ポイント**: VS Code の Java プロジェクトでは、`src` ディレクトリがソースファイルのルートディレクトリになります。パッケージに対応するサブディレクトリは、この `src` ディレクトリ内に作成します。

### 5.3 パッケージの使用方法

#### 5.3.1 import 文による他のパッケージの利用

他のパッケージに定義されたクラスを使用するには、`import` 文を使用します：

```java
package com.example.application;

import com.example.calculator.Calculator;  // 特定のクラスをインポート

public class App {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        // ...
    }
}
```

パッケージ内のすべてのクラスをインポートすることもできます：

```java
import com.example.calculator.*;  // calculatorパッケージのすべてのクラスをインポート
```

> ⚠️ **注意**: `*` を使用してもサブパッケージのクラスはインポートされません。サブパッケージのクラスを使用する場合は、個別にインポートする必要があります。

#### 5.3.2 import 文を使用しない完全修飾名

`import` 文を使用せずに、クラスの完全な名前（パッケージ名を含む）を指定することもできます：

```java
package com.example.application;

public class App {
    public static void main(String[] args) {
        com.example.calculator.Calculator calc = new com.example.calculator.Calculator();
        // ...
    }
}
```

#### 5.3.3 名前の衝突と解決方法

異なるパッケージに同じ名前のクラスがある場合、片方だけを `import` し、もう片方は完全修飾名を使用して解決できます：

```java
package com.example.application;

// basic パッケージの Calculator をインポート
import com.example.calculator.basic.Calculator;

public class App {
    public static void main(String[] args) {
        // basic パッケージの Calculator をインポートしたので、単に Calculator と書ける
        Calculator basicCalc = new Calculator();
        
        // advanced パッケージの Calculator は完全修飾名を使用
        com.example.calculator.advanced.Calculator advancedCalc = 
            new com.example.calculator.advanced.Calculator();
    }
}
```

### 5.4 パッケージとアクセス修飾子

Java のアクセス修飾子とパッケージの関係について見ていきましょう。

#### 5.4.1 アクセス修飾子の種類

Java には以下の4種類のアクセス修飾子があります：

| 修飾子           | クラス内 | パッケージ内 | サブクラス | すべてのクラス |
| ------------- | ---- | ------ | ----- | ------- |
| `private`     | ✓    | ×      | ×     | ×       |
| デフォルト (修飾子なし) | ✓    | ✓      | ×     | ×       |
| `protected`   | ✓    | ✓      | ✓     | ×       |
| `public`      | ✓    | ✓      | ✓     | ✓       |

#### 5.4.2 クラスレベルのアクセス修飾子

クラスレベルでは、`public` またはデフォルト（修飾子なし）のいずれかを使用できます：

```java
// Calculator.java
package com.example.calculator;

public class Calculator {  // どこからでもアクセス可能
    // ...
}

class Helper {  // 同じパッケージ内からのみアクセス可能
    // ...
}
```

#### 5.4.3 メンバーレベルのアクセス修飾子

クラスのメンバー（フィールドやメソッド）に対しては、4つのアクセス修飾子をすべて使用できます：

```java
package com.example.calculator;

public class Calculator {
    private int secret;  // このクラス内からのみアクセス可能
    int defaultValue;    // 同じパッケージ内からアクセス可能
    protected int familyValue;  // 同じパッケージかサブクラスからアクセス可能
    public int publicValue;     // どこからでもアクセス可能
    
    private void privateMethod() {
        // ...
    }
    
    void defaultMethod() {
        // ...
    }
    
    protected void protectedMethod() {
        // ...
    }
    
    public void publicMethod() {
        // ...
    }
}
```

> 📝 **ポイント**: アクセス修飾子を使って、クラスの内部実装を隠し、必要な機能のみを公開するようにすることで、カプセル化を実現できます。

### 5.5 実際のプロジェクト例

実際のプロジェクトでは、次のようにソースファイルを分割してパッケージを使用することができます。例として、簡単な学生管理システムを考えてみましょう。

#### 5.5.1 プロジェクト構造

```
src/
└── com/
    └── example/
        └── studentmgmt/
            ├── main/
            │   └── Main.java
            ├── model/
            │   ├── Student.java
            │   └── Course.java
            └── util/
                └── Calculator.java
```

#### 5.5.2 ソースファイルの例

**Main.java**

```java
package com.example.studentmgmt.main;

import com.example.studentmgmt.model.Student;
import com.example.studentmgmt.model.Course;
import com.example.studentmgmt.util.Calculator;

public class Main {
    public static void main(String[] args) {
        // 学生の作成
        Student student1 = new Student("田中", 1001);
        Student student2 = new Student("佐藤", 1002);
        
        // コースの作成
        Course mathCourse = new Course("数学", "M101");
        
        // 学生をコースに登録
        mathCourse.addStudent(student1);
        mathCourse.addStudent(student2);
        
        // 成績の計算
        Calculator gradeCalc = new Calculator();
        double average = gradeCalc.calculateAverage(
            new int[] {85, 90, 78, 92, 88}
        );
        
        System.out.println("数学コースの受講者:");
        mathCourse.printStudents();
        System.out.println("平均成績: " + average);
    }
}
```

**Student.java**

```java
package com.example.studentmgmt.model;

public class Student {
    private String name;
    private int id;
    
    public Student(String name, int id) {
        this.name = name;
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    
    public int getId() {
        return id;
    }
    
    @Override
    public String toString() {
        return "Student [id=" + id + ", name=" + name + "]";
    }
}
```

**Course.java**

```java
package com.example.studentmgmt.model;

import java.util.ArrayList;
import java.util.List;

public class Course {
    private String name;
    private String code;
    private List<Student> students;
    
    public Course(String name, String code) {
        this.name = name;
        this.code = code;
        this.students = new ArrayList<>();
    }
    
    public void addStudent(Student student) {
        students.add(student);
    }
    
    public void printStudents() {
        for (Student student : students) {
            System.out.println(student);
        }
    }
}
```

**Calculator.java**

```java
package com.example.studentmgmt.util;

public class Calculator {
    public double calculateAverage(int[] grades) {
        if (grades.length == 0) {
            return 0;
        }
        
        int sum = 0;
        for (int grade : grades) {
            sum += grade;
        }
        
        return (double) sum / grades.length;
    }
}
```

> 📝 **ポイント**: このように、関連するクラスをパッケージごとに整理することで、プロジェクトの構造が明確になり、管理しやすくなります。

### 5.6 VS Code でのパッケージ作成方法

VS Code で Java プロジェクトにパッケージを作成する手順を説明します。

#### 5.6.1 パッケージ構造の作成

1. プロジェクトの `src` フォルダを右クリックし、「New Folder」を選択
2. パッケージ階層に対応するフォルダ名を入力（例: `com.example.myapp` の場合は `com`）
3. 作成した `com` フォルダ内で同様に `example` フォルダを作成
4. さらに `example` フォルダ内に `myapp` フォルダを作成

> ⚠️ **注意**: パッケージ階層は、必ずフォルダの階層として表現する必要があります。ドットではなくスラッシュ（/）で区切られたフォルダ階層になります。

#### 5.6.2 パッケージ内にクラスを作成

1. 最終的なパッケージフォルダ（例: `myapp`）を右クリックし、「New File」を選択
2. 作成するクラス名に `.java` を付けたファイル名を入力（例: `MyClass.java`）
3. ファイルの先頭に適切なパッケージ宣言を追加：

```java
package com.example.myapp;

public class MyClass {
    // クラスの内容
}
```

> 📝 **ポイント**: VS Code の Java 拡張機能は、ファイルを作成したフォルダの位置に基づいて適切なパッケージ宣言を自動的に提案することがあります。提案に従うと、手動でパッケージ宣言を入力する手間が省けます。

***

#### 演習問題

**問題 5-1: 基本的なソースファイルの分割**

次の2つのクラスを別々のファイルに分割してください。

```java
// 分割前のコード
public class Main {
    public static void main(String[] args) {
        MathUtil util = new MathUtil();
        System.out.println("5 + 3 = " + util.add(5, 3));
        System.out.println("5 - 3 = " + util.subtract(5, 3));
    }
}

class MathUtil {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
}
```

\<details> \<summary>ヒント\</summary>

* 2つのクラスをそれぞれ別のファイルに分割します
* ファイル名は各クラス名と同じにします
* 両方のクラスを `public` にする必要があります
* このとき、パッケージは指定しません（デフォルトパッケージを使用）

\</details>

**問題 5-2: パッケージの作成と使用**

以下の要件を満たすように、パッケージを作成し、クラスを配置してください。

1. `com.example.math` パッケージに `Calculator` クラスを作成
2. `com.example.app` パッケージに `Main` クラスを作成
3. `Main` クラスから `Calculator` クラスを使用して計算を行う

\<details> \<summary>ヒント\</summary>

* フォルダ構造を作成します:
  * `src/com/example/math/`
  * `src/com/example/app/`
* 各フォルダに適切なソースファイルを作成します
* ソースファイルには適切なパッケージ宣言を追加します
* `Main` クラスで `Calculator` クラスを使用するには、`import` 文を追加します

\</details>

**問題 5-3: アクセス修飾子の理解**

以下のクラスにアクセス修飾子を追加して、適切なアクセス制御を実現してください。

```java
// アクセス修飾子を追加する前のコード
class BankAccount {
    int accountNumber;
    double balance;
    String ownerName;
    
    BankAccount(int accountNumber, String ownerName) {
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = 0.0;
    }
    
    void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    void withdraw(double amount) {
        if (amount > 0 && balance >= amount) {
            balance -= amount;
        }
    }
    
    double getBalance() {
        return balance;
    }
}
```

要件：

* クラスは他のパッケージからもアクセスできるようにする
* `accountNumber`, `balance`, `ownerName` はクラス外からアクセスできないようにする
* コンストラクタと `getBalance` メソッドはどこからでもアクセスできるようにする
* `deposit` と `withdraw` メソッドは同じパッケージからのみアクセスできるようにする

\<details> \<summary>ヒント\</summary>

* クラスには `public` 修飾子を追加します
* フィールドには `private` 修飾子を追加します
* コンストラクタと `getBalance` メソッドには `public` 修飾子を追加します
* `deposit` と `withdraw` メソッドはデフォルトアクセス（修飾子なし）のままにします

\</details>

**問題 5-4: パッケージの階層構造**

以下のパッケージ構造を持つシンプルな学生管理システムを設計してください。

パッケージ構造：

* `com.example.student.model`: データモデルを格納
* `com.example.student.service`: ビジネスロジックを格納
* `com.example.student.ui`: ユーザーインターフェースを格納

作成するクラス：

* `Student` クラス (`model` パッケージに配置)
* `StudentService` クラス (`service` パッケージに配置)
* `StudentUI` クラス (`ui` パッケージに配置)

\<details> \<summary>ヒント\</summary>

* 適切なフォルダ構造を作成します
* 各クラスには適切なパッケージ宣言を追加します
* クラス間の依存関係を考慮します（例: `StudentUI` は `StudentService` を使用し、`StudentService` は `Student` を使用する）
* 必要に応じて `import` 文を追加します

例えば：

```java
// Student.java
package com.example.student.model;

public class Student {
    private int id;
    private String name;
    
    // コンストラクタとメソッド
}
```

```java
// StudentService.java
package com.example.student.service;

import com.example.student.model.Student;

public class StudentService {
    // Student を使用するメソッド
}
```

\</details>

**問題 5-5: パッケージとインポートの実践**

以下の要件を満たす図形計算用のクラス群を作成し、適切なパッケージに配置してください。

1. `com.example.shapes` パッケージに `Shape` インターフェースを作成（`calculateArea` メソッドを持つ）
2. `com.example.shapes.basic` パッケージに `Circle` クラスと `Rectangle` クラスを作成（いずれも `Shape` インターフェースを実装）
3. `com.example.app` パッケージに `ShapeApp` クラスを作成し、`Circle` と `Rectangle` のインスタンスを作成して面積を計算して表示する

\<details> \<summary>ヒント\</summary>

* 適切なフォルダ構造を作成します
* 各ファイルに適切なパッケージ宣言と `import` 文を追加します
* `Shape` はインターフェースなので、`interface` キーワードを使用します
* `Circle` と `Rectangle` クラスは `Shape` インターフェースを実装する（`implements` キーワードを使用）
* `ShapeApp` クラスには `main` メソッドを含め、図形のインスタンスを作成して面積を計算します

例えば：

```java
// Shape.java
package com.example.shapes;

public interface Shape {
    double calculateArea();
}
```

```java
// Circle.java
package com.example.shapes.basic;

import com.example.shapes.Shape;

public class Circle implements Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}
```

\</details>
