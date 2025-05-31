# 1. 投稿データのモデルクラス作成

### 1.1 モデルクラスの概念と役割

モジュール 2 では、投稿データを単純な文字列配列 `String[]` として扱っていました。この方法はシンプルですが、配列の各要素が何を表しているのかわかりにくく、新しい属性を追加するたびにコードを修正する必要があります。

このセクションでは、投稿データを表現するための専用のクラス (モデルクラス) を作成します。モデルクラスを使用することで、データと関連する操作を一つのクラスにまとめることができ、コードの可読性が向上し、新しい属性や機能を追加しやすくなります。

この `Post` クラスは、MVC (Model-View-Controller) アーキテクチャにおける 「Model (モデル)」 の部分として機能し、ブログ投稿のデータとその基本的な操作を提供します。

### 1.2 Post クラスの基本設計

まず、投稿データを表現する `Post` クラスの基本構造を設計します。このクラスには、以下の属性 (プロパティー) を持たせます。

| 属性名           | 説明               | 型             |
| ------------- | ---------------- | ------------- |
| **id**        | 投稿を一意に識別するための ID | int           |
| **title**     | 投稿のタイトル          | String        |
| **content**   | 投稿の内容            | String        |
| **createdAt** | 投稿が作成された日時       | LocalDateTime |

#### クラスファイルの作成

モデルクラスを格納するためのパッケージを作成し、その中に `Post.java` ファイルを作成します。

1. `src/main/java/com/example/blog/model` ディレクトリーを作成します。
2. その中に `Post.java` ファイルを作成します。

#### クラスの基本構造を実装する

`Post.java` に以下のコードを記述します。

```java
package com.example.blog.model;

import java.time.LocalDateTime;

/**
 * ブログ投稿を表すモデルクラス
 */
public class Post {
    // プロパティーの宣言
    private int id;
    private String title;
    private String content;
    private LocalDateTime createdAt;
    
    // ここにコンストラクターやメソッドを追加していきます
}
```

> 📝 **ポイント**: クラスの先頭には `package` 宣言を記述し、そのクラスがどのパッケージに属しているかを示します。また、`import` 文を使って、他のパッケージから必要なクラス (ここでは `java.time.LocalDateTime` ) を読み込みます。従来の `java.util.Date` ではなく、より現代的で使いやすい `LocalDateTime` を使用しています。

### 1.3 コンストラクターの実装

次に、`Post` クラスのインスタンスを作成するためのコンストラクターを実装します。コンストラクターは、クラスと同じ名前を持つ特殊なメソッドで、オブジェクトの初期化を行います。

#### デフォルトコンストラクター

まず、引数を取らないデフォルトコンストラクターを作成します。

```java
/**
 * デフォルトコンストラクター
 */
public Post() {
    this.createdAt = LocalDateTime.now(); // 現在の日時で初期化
}
```

> 📝 **ポイント**: デフォルトコンストラクターでは、`createdAt` プロパティーを現在の日時で初期化しています。`LocalDateTime.now()` は現在の日時を取得するメソッドで、従来の `new Date()` よりも明確で扱いやすい方法です。

#### パラメーター付きコンストラクター

次に、タイトルと内容を指定して投稿を作成できるコンストラクターを追加します。

```java
/**
 * タイトルと内容を指定して投稿を作成するコンストラクター
 * 
 * @param title   投稿のタイトル
 * @param content 投稿の内容
 */
public Post(String title, String content) {
    this(); // デフォルトコンストラクターを呼び出す (日時の初期化) 
    this.title = title;
    this.content = content;
}
```

> 📝 **ポイント**: `this()` を使用して、まずデフォルトコンストラクターを呼び出しています。これにより、デフォルトコンストラクターで行っている初期化処理 (日時の設定) を再利用できます。そして、引数で受け取ったタイトルと内容をそれぞれのプロパティーに設定しています。

#### 全パラメーター付きコンストラクター

最後に、すべてのプロパティーを指定できるコンストラクターを追加します。これは主に、既存の投稿データをデータベースから読み込む際などに使用します。

```java
/**
 * すべてのプロパティを指定して投稿を作成するコンストラクター
 * 
 * @param id        投稿ID
 * @param title     投稿のタイトル
 * @param content   投稿の内容
 * @param createdAt 投稿の作成日時
 */
public Post(int id, String title, String content, LocalDateTime createdAt) {
    this.id = id;
    this.title = title;
    this.content = content;
    this.createdAt = createdAt;
}
```

### 1.4 ゲッターとセッターの実装

プロパティーへのアクセスを制御するために、ゲッター (取得用メソッド) とセッター (設定用メソッド) を実装します。これはカプセル化の一環で、クラスの外部からプロパティーに直接アクセスするのではなく、メソッドを通じてアクセスするようにします。

#### ID のゲッターとセッター

```java
public int getId() {
    return id;
}

public void setId(int id) {
    this.id = id;
}
```

#### タイトルのゲッターとセッター

```java
public String getTitle() {
    return title;
}

public void setTitle(String title) {
    this.title = title;
}
```

#### 内容のゲッターとセッター

```java
public String getContent() {
    return content;
}

public void setContent(String content) {
    this.content = content;
}
```

#### 作成日時のゲッターとセッター

```java
public LocalDateTime getCreatedAt() {
    return createdAt;
}

public void setCreatedAt(LocalDateTime createdAt) {
    this.createdAt = createdAt;
}
```

> 📝 **ポイント**: ゲッターとセッターを使用することで、将来的にプロパティーへのアクセス制御やバリデーション (入力値の検証) を追加しやすくなります。例えば、タイトルが空でないことを確認するなどの処理をセッター内に追加できます。

### 1.5 便利なメソッドの追加

最後に、`Post` クラスに便利なメソッドをいくつか追加します。これらのメソッドは、投稿データの扱いを簡単にするためのものです。

#### toString メソッドのオーバーライド

オブジェクトの文字列表現を返す `toString()` メソッドをオーバーライド (上書き) します。このメソッドは主にデバッグ用で、オブジェクトの内容を確認するのに役立ちます。

```java
/**
 * 投稿の文字列表現を返す
 * 
 * @return 投稿の文字列表現
 */
@Override
public String toString() {
    String displayContent = content != null && content.length() > 20 
        ? content.substring(0, 20) + "..." 
        : content;
    
    return "Post [id=" + id + 
           ", title=" + title + 
           ", content=" + displayContent + 
           ", createdAt=" + createdAt + 
           "]";
}
```

> 📝 **ポイント**: `@Override` アノテーションは、このメソッドが親クラス (この場合は `Object` クラス) のメソッドをオーバーライドしていることを示します。内容が長い場合は、先頭の一部だけを表示して 「...」 を付けることで、読みやすくしています。

#### 投稿内容の要約を返すメソッド

投稿内容の先頭部分だけを取得する便利なメソッドを追加します。これは、投稿一覧などで内容の一部だけを表示する際に役立ちます。

```java
/**
 * 投稿内容の要約を返す (最初の100文字まで) 
 * 
 * @return 投稿内容の要約
 */
public String getSummary() {
    if (content == null || content.length() <= 100) {
        return content;
    }
    return content.substring(0, 100) + "...";
}
```

#### 日時フォーマット用のメソッド

LocalDateTimeを文字列形式でフォーマットするための便利なメソッドを追加します。

```java
/**
 * 作成日時を指定フォーマットで返す
 * 
 * @param pattern フォーマットパターン
 * @return フォーマットされた日時文字列
 */
public String getFormattedCreatedAt(String pattern) {
    return createdAt.format(java.time.format.DateTimeFormatter.ofPattern(pattern));
}

/**
 * 作成日時を標準フォーマット（yyyy/MM/dd HH:mm:ss）で返す
 * 
 * @return フォーマットされた日時文字列
 */
public String getFormattedCreatedAt() {
    return getFormattedCreatedAt("yyyy/MM/dd HH:mm:ss");
}
```

### 1.6 完成した Post クラス

以上の実装を組み合わせた、完成版の `Post.java` の全体コードは以下の通りです。

```java
package com.example.blog.model;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

/**
 * ブログ投稿を表すモデルクラス
 */
public class Post {
    // プロパティの宣言
    private int id;
    private String title;
    private String content;
    private LocalDateTime createdAt;
    
    /**
     * デフォルトコンストラクター
     */
    public Post() {
        this.createdAt = LocalDateTime.now(); // 現在の日時で初期化
    }
    
    /**
     * タイトルと内容を指定して投稿を作成するコンストラクター
     * 
     * @param title   投稿のタイトル
     * @param content 投稿の内容
     */
    public Post(String title, String content) {
        this(); // デフォルトコンストラクターを呼び出す (日時の初期化) 
        this.title = title;
        this.content = content;
    }
    
    /**
     * すべてのプロパティを指定して投稿を作成するコンストラクター
     * 
     * @param id        投稿ID
     * @param title     投稿のタイトル
     * @param content   投稿の内容
     * @param createdAt 投稿の作成日時
     */
    public Post(int id, String title, String content, LocalDateTime createdAt) {
        this.id = id;
        this.title = title;
        this.content = content;
        this.createdAt = createdAt;
    }
    
    // ゲッターとセッター
    public int getId() {
        return id;
    }
    
    public void setId(int id) {
        this.id = id;
    }
    
    public String getTitle() {
        return title;
    }
    
    public void setTitle(String title) {
        this.title = title;
    }
    
    public String getContent() {
        return content;
    }
    
    public void setContent(String content) {
        this.content = content;
    }
    
    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
    
    public void setCreatedAt(LocalDateTime createdAt) {
        this.createdAt = createdAt;
    }
    
    /**
     * 投稿内容の要約を返す (最初の100文字まで) 
     * 
     * @return 投稿内容の要約
     */
    public String getSummary() {
        if (content == null || content.length() <= 100) {
            return content;
        }
        return content.substring(0, 100) + "...";
    }
    
    /**
     * 作成日時を指定フォーマットで返す
     * 
     * @param pattern フォーマットパターン
     * @return フォーマットされた日時文字列
     */
    public String getFormattedCreatedAt(String pattern) {
        return createdAt.format(DateTimeFormatter.ofPattern(pattern));
    }

    /**
     * 作成日時を標準フォーマット（yyyy/MM/dd HH:mm:ss）で返す
     * 
     * @return フォーマットされた日時文字列
     */
    public String getFormattedCreatedAt() {
        return getFormattedCreatedAt("yyyy/MM/dd HH:mm:ss");
    }
    
    /**
     * 投稿の文字列表現を返す
     * 
     * @return 投稿の文字列表現
     */
    @Override
    public String toString() {
        String displayContent = content != null && content.length() > 20 
            ? content.substring(0, 20) + "..." 
            : content;
        
        return "Post [id=" + id + 
            ", title=" + title + 
            ", content=" + displayContent + 
            ", createdAt=" + createdAt + 
            "]";
    }
}
```

### 1.7 Post クラスのテスト

作成した `Post` クラスが正しく動作するかを確認するため、簡単なテストコードを記述してみましょう。実際のアプリケーションでは単体テストフレームワークを使用しますが、ここでは簡易的な方法で確認します。

#### テスト用のメインメソッド

`Post` クラスの最後に、以下のようなメインメソッドを一時的に追加して動作確認ができます。

```java
// テスト用メインメソッド (動作確認後に削除してもよい) 
public static void main(String[] args) {
    // 新しい投稿を作成
    Post post1 = new Post(
        "最初の投稿", 
        "これは最初の投稿の内容です。LocalDateTimeを使用した現代的な日時処理のテストです。"
    );
    post1.setId(1);
    
    // 投稿の内容を表示
    System.out.println("ID: " + post1.getId());
    System.out.println("タイトル: " + post1.getTitle());
    System.out.println("内容: " + post1.getContent());
    System.out.println("作成日時: " + post1.getCreatedAt());
    System.out.println("フォーマット済み日時: " + post1.getFormattedCreatedAt());
    System.out.println("要約: " + post1.getSummary());
    System.out.println("toString: " + post1);
    
    // 別の投稿を作成
    Post post2 = new Post(
        2, 
        "2番目の投稿", 
        "これは2番目の投稿の内容です。とても長い内容の場合は要約されます。".repeat(5), 
        LocalDateTime.now()
    );
    System.out.println("\n2番目の投稿:");
    System.out.println("要約: " + post2.getSummary());
    System.out.println("カスタムフォーマット: " + post2.getFormattedCreatedAt("yyyy年MM月dd日"));
}
```

> 📝 **ポイント**: このメインメソッドは、`Post` クラスの動作確認のために一時的に追加するものです。実際のアプリケーションでは、このようなテストコードは別のクラスや専用のテストフレームワークを使って記述するのが一般的です。確認が終わったら削除するか、コメントアウトしておくとよいでしょう。

このテストコードを実行すると、コンソールに投稿の内容が表示され、`Post` クラスの各メソッドが正しく動作していることが確認できます。LocalDateTimeを使用した日時処理も適切に機能することが確認できるはずです。
