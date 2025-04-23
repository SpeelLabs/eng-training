# 3. CSS の基本

### 3.1 CSSとは？

**CSS (Cascading Style Sheets)** は、HTML で作成した文書の見た目やレイアウトを指定するための言語です。HTML が 「何を表示するか」 を指定するのに対して、CSS は 「どのように表示するか」 を指定します。

私たちが普段目にする Web サイトの色、フォント、レイアウトなどはすべて CSS によって制御されています。

```
HTML = 文書の構造（骨組み）
CSS = 文書のデザイン（見た目）
```

> 📝 **ポイント**: CSS を使うことで、HTML の構造とデザインを分離できます。これにより、内容を変更せずにデザインだけを変更することが可能になります。

### 3.2 CSS の基本構文

CSS は 「セレクター」 と 「宣言ブロック」 で構成されます。

```css
セレクター {
    プロパティ: 値;
    プロパティ: 値;
}
```

例：

{% code title="styles.css" %}
```css
h1 {
    color: blue;
    font-size: 24px;
}
```
{% endcode %}

この例では、

* `h1` がセレクター (スタイルを適用する対象の要素)
* `color: blue;` と `font-size: 24px;` が宣言 (適用するスタイル)

> 📝 **ポイント**: 各宣言の末尾にはセミコロン (`;`) が必要です。

### 3.3 HTML への CSS の適用方法

CSS を HTML に適用する方法は 3つあります。

#### 3.3.1 インラインスタイル

HTML 要素の `style` 属性内に直接 CSS を記述します。

{% code title="index.html" %}
```html
<h1 style="color: blue; font-size: 24px;">青色の見出し</h1>
```
{% endcode %}

* **メリット**: 即座に特定の要素だけにスタイルを適用できる
* **デメリット**: 多くの要素に同じスタイルを適用する場合、コードが冗長になる

#### 3.3.2 内部スタイルシート

HTMLの `<head>` セクション内に `<style>` タグを使用して CSS を記述します。

{% code title="index.html" %}
```html
<head>
    <style>
        h1 {
            color: blue;
            font-size: 24px;
        }
    </style>
</head>
```
{% endcode %}

* **メリット**: 外部ファイルを作成せずに済む
* **デメリット**: HTMLファイルが大きくなる、複数のページで共有できない

#### 3.3.3 外部スタイルシート (推奨)

別ファイル (通常は`.css`拡張子) に CSS を記述し、HTML の `<head>` 内から `<link>` タグで参照します。

{% code title="index.html" %}
```html
<head>
    <link rel="stylesheet" href="styles.css">
</head>
```
{% endcode %}

**`styles.css` ファイルの内容**

{% code title="styles.css" %}
```css
h1 {
    color: blue;
    font-size: 24px;
}
```
{% endcode %}

* **メリット**: 複数の HTML ページで同じスタイルを共有できる、メンテナンスが容易
* &#x20;**デメリット**: 追加のファイルを管理する必要がある

> 📝 **ポイント**: 実務では外部スタイルシートを使用する方法が最も一般的です。コードの整理と再利用が容易になります。

### 3.4 基本的なセレクター

CSS では様々な方法で要素を選択できます。

#### 3.4.1 要素セレクター

HTML タグ名で要素を選択します。ページ内のすべての指定したタグにスタイルが適用されます。

{% code title="styles.css" %}
```css
h1 { color: blue; }
p { font-size: 16px; }
```
{% endcode %}

#### 3.4.2 クラスセレクター

`class` 属性を持つ要素を選択します。クラスは複数の要素で共有できます。**クラスセレクターはドット ( `.`  ) で始まります。**

{% code title="styles.css" %}
```css
.highlight { background-color: yellow; }
```
{% endcode %}

{% code title="index.html" %}
```html
<p class="highlight">この段落はハイライトされます。</p>
<span class="highlight">このテキストもハイライトされます。</span>
```
{% endcode %}

#### 3.4.3 ID セレクター

`id` 属性を持つ要素を選択します。ID はページ内で一意である必要があります。**ID セレクターはハッシュ記号 ( `#` ) で始まります。**

{% code title="styles.css" %}
```css
#header { background-color: black; color: white; }
```
{% endcode %}

{% code title="index.html" %}
```html
<div id="header">ヘッダー</div>
```
{% endcode %}

> 📝 **ポイント**: ID は一意なので、ページ内で一度しか使えません。繰り返し使用するスタイルにはクラスを使いましょう。

#### 3.4.4 セレクターの組み合わせ ⭐

セレクターを組み合わせて、より具体的な要素を選択できます。

{% code title="styles.css" %}
```css
/* .info クラスを持つ p 要素のみを選択 */
p.info { font-style: italic; }

/* 子孫セレクタ: #main の中にある li 要素 */
#main li { list-style-type: square; }

/* 子セレクタ: nav の直接の子である ul */
nav > ul { margin: 0; }
```
{% endcode %}

### 3.5 よく使う CSS プロパティ

#### 3.5.1 テキストのスタイリング

<table><thead><tr><th width="178.616455078125">プロパティ</th><th width="171.79473876953125">説明</th><th>例</th></tr></thead><tbody><tr><td>color</td><td>文字色</td><td><code>color: #333333;</code></td></tr><tr><td>font-family</td><td>フォント</td><td><code>font-family: Arial, sans-serif;</code></td></tr><tr><td>font-size</td><td>文字サイズ</td><td><code>font-size: 16px;</code></td></tr><tr><td>font-weight</td><td>文字の太さ</td><td><code>font-weight: bold;</code></td></tr><tr><td>text-align</td><td>テキストの配置</td><td><code>text-align: center;</code></td></tr><tr><td>line-height</td><td>行の高さ</td><td><code>line-height: 1.5;</code></td></tr></tbody></table>

{% code title="styles.css" %}
```css
p {
    color: #333333;
    font-family: Arial, sans-serif;
    font-size: 16px;
    line-height: 1.5;
}

h1 {
    font-size: 32px;
    font-weight: bold;
    text-align: center;
}
```
{% endcode %}

#### 3.5.2 背景のスタイリング

<table><thead><tr><th width="190.7542724609375">プロパティ</th><th width="197.382080078125">説明</th><th>例</th></tr></thead><tbody><tr><td>background-color</td><td>背景色</td><td><code>background-color: #f0f0f0;</code></td></tr><tr><td>background-image</td><td>背景画像</td><td><code>background-image: url('bg.jpg');</code></td></tr><tr><td>background-repeat</td><td>背景画像の繰り返し</td><td><code>background-repeat: no-repeat;</code></td></tr><tr><td>background-position</td><td>背景画像の位置</td><td><code>background-position: center;</code></td></tr></tbody></table>

{% code title="styles.css" %}
```css
body {
    background-color: #f5f5f5;
}

.hero {
    background-image: url('header-bg.jpg');
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;
}
```
{% endcode %}

#### 3.5.3 ボックスモデル

すべての HTML 要素は 「ボックス」 として扱われます。このボックスは以下の部分で構成されています。

![ボックスモデル](https://claude.ai/.gitbook/assets/box-model.png)

<table><thead><tr><th width="158.225830078125">部分</th><th>説明</th></tr></thead><tbody><tr><td>content</td><td>内容が表示される部分</td></tr><tr><td>padding</td><td>内容と境界線の間の空間</td></tr><tr><td>border</td><td>ボックスの境界線</td></tr><tr><td>margin</td><td>ボックスの外側の空間</td></tr></tbody></table>

{% code title="styles.css" %}
```css
div {
    width: 300px;             /* 幅 */
    height: 200px;            /* 高さ */
    padding: 20px;            /* 内側の余白 */
    border: 1px solid black;  /* 枠線 */
    margin: 10px;             /* 外側の余白 */
}
```
{% endcode %}

> 📝 **ポイント**: ボックスモデルを理解することは、レイアウトを制御する上で非常に重要です。要素の実際の幅は、content + padding + border で計算されます。

### 3.6 余白と配置

#### 3.6.1 padding と margin

* **padding**: 要素の内容と境界線 (border) の間のスペース
* **margin**: 要素の境界線と隣接要素との間のスペース

{% code title="styles.css" %}
```css
/* 四方向をまとめて指定 */
div {
    padding: 10px;  /* 上下左右すべて10px */
    margin: 20px;   /* 上下左右すべて20px */
}

/* 上下・左右をそれぞれ指定 */
div {
    padding: 10px 20px;  /* 上下10px、左右20px */
    margin: 15px 5px;    /* 上下15px、左右5px */
}

/* 上・右・下・左の順に個別指定 */
div {
    padding: 10px 20px 15px 5px;  /* 時計回り: 上 右 下 左 */
    margin: 10px 5px 10px 5px;
}
```
{% endcode %}

#### 3.6.2 中央揃え

**水平方向の中央揃え**

{% code title="styles.css" %}
```css
/* ブロック要素の中央揃え */
div {
    width: 300px;        /* 幅の指定が必要 */
    margin-left: auto;
    margin-right: auto;
    /* または margin: 0 auto; */
}

/* テキストの中央揃え */
p {
    text-align: center;
}
```
{% endcode %}

### 3.7 カラー表現

CSS では複数の方法で色を指定できます。

#### 3.7.1 色の指定方法

<table><thead><tr><th width="158.055419921875">方法</th><th width="192.25994873046875">書式</th><th>例</th></tr></thead><tbody><tr><td>名前</td><td>色名</td><td><code>color: red;</code></td></tr><tr><td>16進数表記</td><td>#RRGGBB</td><td><code>color: #FF0000;</code> (赤)</td></tr><tr><td>RGB表記</td><td>rgb(R, G, B)</td><td><code>color: rgb(255, 0, 0);</code> (赤)</td></tr><tr><td>RGBA表記</td><td>rgba(R, G, B, A)</td><td><code>color: rgba(255, 0, 0, 0.5);</code> (半透明の赤)</td></tr></tbody></table>

{% code title="styles.css" %}
```css
/* 名前で指定 */
h1 {
    color: blue;
}

/* 16進数表記 */
p {
    color: #333333;  /* ダークグレー */
}

/* RGB表記 */
.highlight {
    background-color: rgb(255, 255, 0);  /* 黄色 */
}

/* RGBA表記（透明度付き） */
.overlay {
    background-color: rgba(0, 0, 0, 0.7);  /* 70%不透明の黒 */
}
```
{% endcode %}

> 📝 **ポイント**: 16進数表記は最も一般的に使われます。同じ値が並ぶ場合は短縮形を使えます (#FF0000 → #F00)。

### 3.8 CSS のカスケードと優先順位

CSS では複数のスタイルが同じ要素に適用される場合、一定のルールに従って最終的なスタイルが決まります。

#### 3.8.1 優先順位のルール

1. **重要度**: `!important` が付いたスタイルが最優先
2. **詳細度**: セレクタの詳細さに基づく優先順位 (ID > クラス > 要素)
3. **順序**: 同じ詳細度の場合、後に書かれたスタイルが優先

{% code title="styles.css" %}
```css
p { color: blue; }                 /* 詳細度: 低 */
.text { color: green; }            /* 詳細度: 中 */
#unique { color: red; }            /* 詳細度: 高 */
p { color: purple !important; }    /* !important: 最優先 */
```
{% endcode %}

> 📝 **ポイント**: `!important` は使いすぎるとスタイルの管理が難しくなるため、必要な場合のみ使用しましょう。

### 演習問題

#### 問題 3-1: 外部 CSS ファイルの作成とリンク

1. プロジェクトフォルダ内に `css` フォルダーを作成
2. そのフォルダー内に `styles.css` ファイルを作成
3. HTML ファイルの `<head>` セクションに外部 CSS ファイルへのリンクを追加

<details>

<summary>ヒント</summary>

{% code title="index.html" %}
```html
<!-- index.html のhead内に追加 -->
<link rel="stylesheet" href="css/styles.css">
```
{% endcode %}

</details>

#### 問題 3-2: 基本スタイルの適用

`styles.css` ファイルに以下のスタイルを追加してください。

1. ページ全体の文字フォントを 「Arial, sans-serif」 に設定
2. h1 見出しを中央揃えにし、色を青系の色に変更
3. 段落 (p要素) のテキストサイズを 16pxに、行間 (line-height) を 1.5 に設定

<details>

<summary>ヒント</summary>

{% code title="styles.css" %}
```css
/* styles.css */
body {
    font-family: Arial, sans-serif;
}

h1 {
    text-align: center;
    color: #3366cc; /* 青色 */
}

p {
    font-size: 16px;
    line-height: 1.5;
}
```
{% endcode %}

</details>

#### 問題 3-3: divのスタイリング ⭐

`styles.css` ファイルに、divに対するスタイルを追加してください。

1. `.wrapper` クラスを持つdivに対して、最大幅を800pxに設定し、余白を自動（中央揃え）に
2. `.profile` クラスを持つdivに対して、背景色を薄い灰色に、内側の余白を20px、下側の余白を30pxに設定
3. `.hobbies` クラスを持つdivに対して、境界線（枠線）を追加

<details>

<summary>ヒント</summary>

{% code title="styles.css" %}
```css
/* styles.css に追加 */
.wrapper {
    max-width: 800px;
    margin: 0 auto;
}

.profile {
    background-color: #f5f5f5;
    padding: 20px;
    margin-bottom: 30px;
}

.hobbies {
    padding: 20px;
    border: 1px solid #ddd;
    margin-bottom: 30px;
}
```
{% endcode %}

</details>
