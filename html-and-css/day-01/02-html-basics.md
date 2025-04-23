# 2. HTML の基本

### 2.1 HTMLとは何か

**HTML (HyperText Markup Language)** は、Web ページの構造とコンテンツを定義するためのマークアップ言語です。テキスト、画像、リンクなど、Web ページに表示される要素は HTML によって記述されます。

HTML は **タグ** を使って文書の構造を表現します。タグは Web ブラウザーに 「この部分は見出しです」 「ここは段落です」 のように指示を与えるものです。

```
HTML ≠ プログラミング言語
HTML = マークアップ言語（文書構造を定義する言語）
```

> 📝 **ポイント**: HTML は 「何を表示するか」 を指定します。スタイル (デザイン) は CSS で、動作は JavaScript で制御します。この役割分担を理解することが重要です。

### 2.2 HTML の基本構造

すべての HTML ファイルは、以下の基本的な構造に従っています。

{% code title="index.html" %}
```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ページのタイトル</title>
</head>
<body>
    <!-- ここにコンテンツが入ります -->
    <h1>こんにちは、世界！</h1>
    <p>これは段落です。</p>
</body>
</html>
```
{% endcode %}

この構造の各部分について見ていきましょう。

#### 2.2.1 DOCTYPE 宣言

```html
<!DOCTYPE html>
```

この宣言は文書の先頭に配置され、ブラウザーに HTML 文書であることを伝えます。

#### 2.2.2 html要素

```html
<html lang="ja">
  ...
</html>
```

html 要素はルート要素と呼ばれ、ドキュメント全体を包みます。`lang` 属性は文書の言語を指定します。

#### 2.2.3 head 要素

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ページのタイトル</title>
</head>
```

head 要素には、ページに関するメタデータが含まれます。

* **meta charset**: 文字エンコーディングを指定 (日本語表示に必要)
* **meta viewport**: モバイルデバイスでの表示方法を制御
* **title**: ブラウザーのタブに表示されるタイトル

#### 2.2.4 body 要素

```html
<body>
    <h1>こんにちは、世界！</h1>
    <p>これは段落です。</p>
</body>
```

body 要素には、ブラウザーに実際に表示されるすべてのコンテンツが含まれます。

> 📝 **ポイント**: VS Code では、`!` + <kbd>Tab</kbd> キーを押すと、この基本構造が自動的に挿入されます。

### 2.3 HTML タグの基本

HTML はタグによって構成されます。

#### 2.3.1 タグの基本形式

多くの HTML タグは、開始タグと終了タグのペアで使用します。

```html
<タグ名>内容</タグ名>
```

**例：**

```html
<h1>これは大見出しです</h1>
<p>これは段落です。</p>
```

#### 2.3.2 属性

タグには 「属性」 を追加して、追加情報を提供できます。

```html
<タグ名 属性名="属性値">内容</タグ名>
```

**例：**

```html
<a href="https://example.com">リンクテキスト</a>
<img src="image.jpg" alt="画像の説明">
```

#### 2.3.3 空要素

一部のタグは開始タグのみを持ち、終了タグを持ちません。

```html
<img src="image.jpg" alt="画像の説明">
<br>
<hr>
<input type="text" name="username">
```

### 2.4 主要な HTML タグ

#### 2.4.1 見出しタグ (h1～h6)

見出しタグは文書の構造を示す重要な要素です。h1 が最も重要な見出し、h6 が最も小さい見出しです。

```html
<h1>最も重要な見出し</h1>
<h2>セクションの見出し</h2>
<h3>サブセクションの見出し</h3>
<h4>小見出し</h4>
```

見出しタグの使用ガイドライン：

* 各ページには通常 **1つの h1** のみ使用
* 見出しは階層構造を守る (h1 の後に h3 ではなく h2 を使う)

#### 2.4.2 段落タグ (p)

段落タグはテキストの段落を定義します。

```html
<p>これは1つの段落です。テキストは自動的に折り返されます。
HTMLコード内の改行はブラウザーに無視されます。</p>

<p>新しい段落を始めるには新しいpタグを使います。</p>
```

#### 2.4.3 テキストフォーマットタグ

テキストを視覚的に強調するためのタグ：

<table><thead><tr><th width="137.671875">タグ</th><th>説明</th><th>例</th></tr></thead><tbody><tr><td><code>&#x3C;strong></code></td><td>強い重要性 (通常は太字)</td><td>&#x3C;strong>重要な文章&#x3C;/strong></td></tr><tr><td><code>&#x3C;em></code></td><td>強調 (通常は斜体)</td><td>&#x3C;em>強調された文章&#x3C;/em></td></tr></tbody></table>

使い方の例：

```html
<p>これは<strong>とても重要な</strong>情報です。</p>
<p>この単語を<em>強調</em>します。</p>
```

#### 2.4.4 リストタグ (ul, ol, li)

**順序なしリスト (ul)**

箇条書きリストを作成します。

```html
<ul>
    <li>リンゴ</li>
    <li>バナナ</li>
    <li>オレンジ</li>
</ul>
```

**順序付きリスト (ol)**

番号付きリストを作成します。

```html
<ol>
    <li>材料を準備する</li>
    <li>材料を混ぜる</li>
    <li>オーブンで焼く</li>
</ol>
```

#### 2.4.5 リンクタグ (a)

a タグは、ハイパーリンクを作成するために使用します。

```html
<a href="https://www.example.com">リンクテキスト</a>
```

主な属性：

* **href**: リンク先の URL
* **target**: リンクの開き方 ( `_blank` で新しいタブ)

**使い方の例：**

```html
<!-- 外部リンク -->
<a href="https://www.google.com" target="_blank">Googleへ</a>

<!-- 内部リンク（同じサイト内の別ページ） -->
<a href="about.html">自己紹介ページ</a>
```

#### 2.4.6 画像タグ (img)

img タグは画像を表示するために使用します。

```html
<img src="image.jpg" alt="画像の説明" width="300" height="200">
```

必須属性：

* **src**: 画像ファイルのパス
* **alt**: 画像の代替テキスト (画像が表示できない場合に重要)

#### 2.4.7 コンテナ要素 (div, span)

コンテナ要素は、他の要素をグループ化するために使用されます。

**div 要素**

`div` はブロックレベル要素で、通常は新しい行で始まります。

```html
<div class="container">
    <h2>セクションのタイトル</h2>
    <p>セクションの内容...</p>
</div>
```

**span 要素**

`span` はインライン要素で、テキストの流れの中で使用されます。

```html
<p>この文の<span class="highlight">一部</span>だけをハイライトしたい場合に使います。</p>
```

> 📝 **ポイント**: `div` と`span` は、HTML の構造を整理し、CSS でスタイリングするための基本要素です。

### 2.5 HTMLの基本的な属性

属性は HTML タグに追加情報を提供します。

#### 2.5.1 よく使う属性

<table><thead><tr><th width="109.01776123046875">属性</th><th width="281.92431640625">説明</th><th>例</th></tr></thead><tbody><tr><td>id</td><td>要素の一意の識別子</td><td><code>&#x3C;div id="header"></code></td></tr><tr><td>class</td><td>スタイル適用や JavaScript で使用するクラス名</td><td><code>&#x3C;p class="intro"></code></td></tr><tr><td>style</td><td>インライン CSS スタイル</td><td><code>&#x3C;p style="color:blue;"></code></td></tr></tbody></table>



***

### 演習問題

#### 問題 2-1: 基本的な HTML ドキュメントの作成

新しい HTML ファイルを作成し、以下の要素を含む基本的な文書構造を記述してください：

1. HTML の DOCTYPE 宣言
2. html 要素 (lang 属性を "ja" に設定)
3. head 要素 (title, meta charset, viewportを含む)
4. body 要素に 「こんにちは、HTML」 という見出し (h1)

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML入門</title>
</head>
<body>
    <h1>こんにちは、HTML</h1>
</body>
</html>
```

</details>

#### 問題 2-2: 自己紹介ページの作成

問題2-1で作成したHTMLに以下の要素を追加して、簡単な自己紹介ページを作成してください：

1. 「プロフィール」 という h2 見出し
2. 自分についての段落 (p 要素)
3. 「趣味」 という h2 見出し
4. 趣味のリスト (ul 要素と複数の li 要素)

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>自己紹介</title>
</head>
<body>
    <h1>こんにちは、HTML</h1>
    
    <h2>プロフィール</h2>
    <p>はじめまして。私は[あなたの名前]です。現在HTML/CSSを学習中です。</p>
    
    <h2>趣味</h2>
    <ul>
        <li>読書</li>
        <li>映画鑑賞</li>
        <li>プログラミング</li>
    </ul>
</body>
</html>
```

</details>

#### 問題 2-3: div を使った構造化 ⭐

問題 2-2 で作成した HTML を、div を使って構造化してください：

1. 全体を 「wrapper」 というクラスを持つ div で囲む
2. 「プロフィール」 セクションを 「profile」 というクラスを持つ div で囲む
3. 「趣味」 セクションを 「hobbies」 というクラスを持つ div で囲む

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>自己紹介</title>
</head>
<body>
    <div class="wrapper">
        <h1>こんにちは、HTML</h1>
        
        <div class="profile">
            <h2>プロフィール</h2>
            <p>はじめまして。私は[あなたの名前]です。現在HTML/CSSを学習中です。</p>
        </div>
        
        <div class="hobbies">
            <h2>趣味</h2>
            <ul>
                <li>読書</li>
                <li>映画鑑賞</li>
                <li>プログラミング</li>
            </ul>
        </div>
    </div>
</body>
</html>
```

</details>
