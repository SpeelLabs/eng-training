# 8. レイアウトの基本

### 8.1 HTMLのレイアウト構造

Web ページのレイアウトは、HTML 要素をどのように配置するかによって決まります。HTML の要素は大きく分けて 2種類あります。

#### 8.1.1 ブロック要素とインライン要素

**ブロック要素**：

* 常に新しい行から始まる
* 親要素の幅いっぱいに広がる
* 高さと幅を指定できる
* 例：`<div>`, `<h1>～<h6>`, `<p>`, `<ul>`, `<li>`, `<section>`

**インライン要素**：

* 行内に表示される（新しい行を作らない）
* コンテンツの幅だけを占める
* 高さと幅を直接指定できない
* 例：`<span>`, `<a>`, `<img>`, `<strong>`, `<em>`

{% code overflow="wrap" %}
```html
<div>これはブロック要素です。行全体を占めます。</div>
<span>これはインライン要素です。</span><span>隣に他の要素が来ることができます。</span>
```
{% endcode %}

表示結果：

```
これはブロック要素です。行全体を占めます。
これはインライン要素です。隣に他の要素が来ることができます。
```

> 📝 **ポイント**: ブロック要素の中にはブロック要素とインライン要素の両方を入れられますが、インライン要素の中にブロック要素を入れることはHTMLの仕様上避けるべきです。

### 8.2 divとspanの役割

#### 8.2.1 divタグ - ブロックレベルのコンテナ

`<div>`（ディビジョン）は、複数の要素をグループ化するための汎用的なブロックレベルコンテナです。CSSでスタイルを適用する際の「箱」として機能します。

```html
<div class="container">
    <h2>セクションタイトル</h2>
    <p>セクション内のテキスト</p>
    <p>さらにテキスト...</p>
</div>
```

#### 8.2.2 spanタグ - インラインレベルのコンテナ

`<span>`は、テキストの一部分など、インラインコンテンツをグループ化するための汎用的なインラインコンテナです。

```html
<p>ここは<span class="highlight">重要な</span>ポイントです。</p>
```

> 📝 **ポイント**: `div`と`span`自体には特別な意味や視覚的な効果はありませんが、CSSのセレクタとして使うことで、部分的にスタイルを適用するために重要です。

### 8.3 ボックスモデルを理解する

すべてのHTML要素は「ボックス」として扱われます。CSSのボックスモデルは、要素の実際のサイズを決定する重要な概念です。

#### 8.3.1 ボックスモデルの構成要素

<div align="left"><img src="https://claude.ai/.gitbook/assets/box-model.png" alt="ボックスモデル"></div>

各要素は以下の部分で構成されています：

1. **Content（コンテンツ）**: テキストや画像など、実際の内容が表示される部分
2. **Padding（パディング）**: コンテンツの周りの空間
3. **Border（ボーダー）**: パディングの外側の線
4. **Margin（マージン）**: ボーダーの外側の空間

```css
div {
    width: 300px;         /* コンテンツ幅 */
    padding: 20px;        /* 内側の余白 */
    border: 1px solid #000; /* 境界線 */
    margin: 30px;         /* 外側の余白 */
}
```

この例での要素の**実際の幅**は以下のように計算されます： 300px（コンテンツ） + 40px（左右パディング） + 2px（左右ボーダー） = 342px

> 📝 **ポイント**: デフォルトでは、`width`プロパティはコンテンツ領域のみを指定するため、要素の実際の幅はパディングとボーダーを加えたものになります。これを変更するには`box-sizing: border-box;`を使用します。

#### 8.3.2 box-sizing

`box-sizing`プロパティを使うと、ボックスモデルの計算方法を変更できます：

```css
div {
    box-sizing: border-box;
    width: 300px;
    padding: 20px;
    border: 1px solid #000;
}
```

`border-box`を指定すると、`width`にはパディングとボーダーが含まれるようになります。つまり、実際のコンテンツ幅は「指定した幅 - パディング - ボーダー」になります： 300px - 40px（左右パディング） - 2px（左右ボーダー） = 258px（コンテンツ幅）

多くの開発者は、レイアウトを扱いやすくするために、すべての要素に`border-box`を適用します：

```css
* {
    box-sizing: border-box;
}
```

### 8.4 divを使ったレイアウト構築

divを使って基本的なレイアウトを構築する方法を見ていきましょう。

#### 8.4.1 基本的なページレイアウト

```html
<div class="container">
    <div class="header">
        <h1>ウェブサイトのタイトル</h1>
    </div>
    
    <div class="nav">
        <ul>
            <li><a href="#">ホーム</a></li>
            <li><a href="#">サービス</a></li>
            <li><a href="#">お問い合わせ</a></li>
        </ul>
    </div>
    
    <div class="main">
        <div class="content">
            <h2>メインコンテンツ</h2>
            <p>本文がここに入ります...</p>
        </div>
        
        <div class="sidebar">
            <h3>サイドバー</h3>
            <p>補足情報...</p>
        </div>
    </div>
    
    <div class="footer">
        <p>&copy; 2023 サンプルサイト</p>
    </div>
</div>
```

これに対応するCSSは以下のようになります：

```css
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

.header {
    background-color: #f5f5f5;
    padding: 20px;
    text-align: center;
}

.nav {
    background-color: #333;
    color: white;
    padding: 10px;
}

.nav ul {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
}

.nav li {
    margin-right: 20px;
}

.nav a {
    color: white;
    text-decoration: none;
}

.main {
    display: flex;
    margin: 20px 0;
}

.content {
    flex: 2;
    padding: 20px;
    background-color: #fff;
}

.sidebar {
    flex: 1;
    padding: 20px;
    background-color: #f0f0f0;
    margin-left: 20px;
}

.footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 20px;
}
```

#### 8.4.2 divの階層構造

実際のWebサイトでは、divが階層構造を形成することが一般的です。これにより、複雑なレイアウトを構築できます。

```
container
├── header
├── nav
├── main
│   ├── content
│   └── sidebar
└── footer
```

このような階層構造は「ネスト（入れ子）」と呼ばれ、CSSセレクタを使って特定の要素をターゲットにするのに役立ちます：

```css
.main .content {
    /* mainの中のcontentにのみ適用されるスタイル */
}
```

### 8.5 CSS Displayプロパティ

要素の表示方法は`display`プロパティで制御できます。

#### 8.5.1 主なdisplayの値

| 値              | 説明                | 例                |
| -------------- | ----------------- | ---------------- |
| `block`        | ブロックレベル要素として表示    | `div`, `p`, `h1` |
| `inline`       | インライン要素として表示      | `span`, `a`      |
| `inline-block` | インライン配置でブロック特性を持つ | `button`         |
| `none`         | 要素を非表示にする         | -                |
| `flex`         | フレックスボックスとして表示    | -                |
| `grid`         | グリッドとして表示         | -                |

#### 8.5.2 displayの使用例

```css
/* インライン要素をブロックに変更 */
a.button {
    display: block;
    width: 200px;
    padding: 10px;
    text-align: center;
}

/* ブロック要素をインラインに変更 */
li {
    display: inline;
    margin-right: 10px;
}

/* インラインブロックの例 */
.icon {
    display: inline-block;
    width: 32px;
    height: 32px;
    vertical-align: middle;
}

/* 要素を非表示にする */
.hidden {
    display: none;
}
```

> 📝 **ポイント**: `display: none;`は要素を完全に非表示にしますが、`visibility: hidden;`は要素のスペースを維持したまま見えなくします。

### 8.6 レイアウトの実践例

#### 8.6.1 中央揃えのコンテナ

Webサイトのコンテンツを中央に配置するパターン：

```css
.container {
    max-width: 1200px;  /* 最大幅を設定 */
    margin: 0 auto;     /* 左右のマージンを自動に */
    padding: 0 15px;    /* 左右の内側の余白 */
}
```

#### 8.6.2 カードレイアウト

情報カードを表示するパターン：

```html
<div class="card-container">
    <div class="card">
        <h3>カード1</h3>
        <p>内容...</p>
    </div>
    <div class="card">
        <h3>カード2</h3>
        <p>内容...</p>
    </div>
    <div class="card">
        <h3>カード3</h3>
        <p>内容...</p>
    </div>
</div>
```

```css
.card-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
}

.card {
    flex: 1;
    min-width: 250px;
    padding: 20px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    border-radius: 5px;
    background-color: #fff;
}
```

#### 8.6.3 2カラムレイアウト ⭐

メインコンテンツとサイドバーのパターン：

```html
<div class="two-column">
    <div class="main-content">
        <h2>メインコンテンツ</h2>
        <p>本文...</p>
    </div>
    <div class="sidebar">
        <h3>サイドバー</h3>
        <p>補足情報...</p>
    </div>
</div>
```

```css
.two-column {
    display: flex;
    gap: 30px;
}

.main-content {
    flex: 2;  /* メインコンテンツが2/3のスペースを取る */
}

.sidebar {
    flex: 1;  /* サイドバーが1/3のスペースを取る */
}

/* スマホ表示時には縦並びにする */
@media (max-width: 768px) {
    .two-column {
        flex-direction: column;
    }
}
```

### 8.7 実際のサイトのレイアウト分析

実際のWebサイトのレイアウトを理解するための分析方法を見ていきましょう。

#### 8.7.1 ブラウザのデベロッパーツールを使った分析

1. Webサイト上で右クリック→「検証」または「要素を調査」を選択
2. HTML構造とCSSが表示される
3. 要素にカーソルを合わせると、ページ上でその要素がハイライトされる

![デベロッパーツール](https://claude.ai/.gitbook/assets/devtools.png)

#### 8.7.2 一般的なレイアウトパターン

多くのWebサイトで見られるレイアウトパターン：

1. **ヘッダー + コンテンツ + フッター**
   * 最も基本的なレイアウト
2. **ヘッダー + サイドバー + コンテンツ + フッター**
   * ブログやポータルサイトに多い
3. **グリッドレイアウト**
   * 商品一覧やポートフォリオサイトに多い

> 📝 **ポイント**: 既存のWebサイトを分析することで、実際のレイアウト構築手法を学べます。ブラウザの開発者ツールは、Webデザインを学ぶ上で強力な味方です。



***

### 演習問題

#### 問題 8-1: 基本的なレイアウト構造の作成

HTMLファイルに以下の構造を持つ基本的なレイアウトを作成してください：

1. ヘッダー（サイトタイトルを含む）
2. ナビゲーション（3つのリンクを含む）
3. メインコンテンツ（見出しと段落を含む）
4. フッター（著作権表示を含む）

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>基本レイアウト</title>
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 15px;
        }
        .header {
            background-color: #f5f5f5;
            padding: 20px;
            text-align: center;
        }
        .nav {
            background-color: #333;
            padding: 10px;
        }
        .nav ul {
            list-style: none;
            margin: 0;
            padding: 0;
            display: flex;
        }
        .nav li {
            margin-right: 20px;
        }
        .nav a {
            color: white;
            text-decoration: none;
        }
        .main {
            padding: 20px 0;
        }
        .footer {
            background-color: #333;
            color: white;
            text-align: center;
            padding: 20px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>サイトタイトル</h1>
        </div>
        
        <div class="nav">
            <ul>
                <li><a href="#">ホーム</a></li>
                <li><a href="#">サービス</a></li>
                <li><a href="#">お問い合わせ</a></li>
            </ul>
        </div>
        
        <div class="main">
            <h2>メインコンテンツ</h2>
            <p>これはメインコンテンツの部分です。ここに本文が入ります。</p>
            <p>レイアウトの基本を学びながら、HTML/CSSの理解を深めましょう。</p>
        </div>
        
        <div class="footer">
            <p>&copy; 2023 サンプルサイト</p>
        </div>
    </div>
</body>
</html>
```

</details>

#### 問題 8-2: ボックスモデルの適用

次のHTMLに対して、ボックスモデルの各要素（マージン、ボーダー、パディング）を追加してスタイルを整えてください：

```html
<div class="box">
    これはサンプルテキストです。
</div>
```

<details>

<summary>ヒント</summary>

```css
.box {
    width: 300px;
    padding: 20px;
    border: 2px solid #3498db;
    margin: 30px auto;
    background-color: #f5f5f5;
    text-align: center;
    
    /* ボックスモデルを変更（オプション） */
    box-sizing: border-box;
}
```

この例では、paddingとborderがあることで、実際の表示幅は344px（300px + 左右パディング40px + 左右ボーダー4px）になります。`box-sizing: border-box;`を追加すると、表示幅は300pxに固定され、コンテンツ幅が自動調整されます。

</details>

#### 問題 8-3: 2カラムレイアウトの作成 ⭐

フレックスボックスを使って2カラムレイアウト（メインコンテンツとサイドバー）を作成してください：

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2カラムレイアウト</title>
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .two-column {
            display: flex;
            gap: 30px;
        }
        .main-content {
            flex: 2;
            background-color: #f5f5f5;
            padding: 20px;
            border-radius: 5px;
        }
        .sidebar {
            flex: 1;
            background-color: #e0e0e0;
            padding: 20px;
            border-radius: 5px;
        }
        
        /* レスポンシブ対応 */
        @media (max-width: 768px) {
            .two-column {
                flex-direction: column;
            }
            .sidebar {
                margin-top: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>2カラムレイアウト</h1>
        
        <div class="two-column">
            <div class="main-content">
                <h2>メインコンテンツ</h2>
                <p>これはメインコンテンツのセクションです。通常、ページの主要な情報がここに表示されます。</p>
                <p>レスポンシブデザインにより、画面サイズが小さくなると、このレイアウトは1カラムに変わります。</p>
            </div>
            
            <div class="sidebar">
                <h3>サイドバー</h3>
                <p>ここにはサブメニューや補足情報などを配置します。</p>
                <ul>
                    <li>関連リンク1</li>
                    <li>関連リンク2</li>
                    <li>関連リンク3</li>
                </ul>
            </div>
        </div>
    </div>
</body>
</html>
```

ブラウザのサイズを変更して、レスポンシブなレイアウト変更を確認してみてください。

</details>
