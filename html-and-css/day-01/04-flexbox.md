# 4. フレックスボックス

### 4.1 フレックスボックスとは？

**フレックスボックス (Flexbox)** は、要素を行または列に簡単に配置するための CSS のレイアウトモデルです。従来の方法では複雑だったレイアウトを、少ないコードで実現できます。

フレックスボックスを使うと、以下のようなレイアウトが簡単に作成できます。

* ナビゲーションメニューの横並び
* カードの均等配置
* 要素の中央揃え
* スマートフォンでの表示に対応したレイアウト

> 📝 **ポイント**: フレックスボックスは、行または列の一次元レイアウトに最適です。2次元 (行と列の両方) のレイアウトにはグリッドレイアウトが向いています。

### 4.2 フレックスボックスの基本概念

フレックスボックスを使うには、まず **親要素 (コンテナー) に `display: flex` を適用します。**&#x3059;ると、**その中の子要素 (アイテム)** が自動的にフレックスアイテムになります。

{% code lineNumbers="true" %}
```html
<div class="container">
    <div class="item">アイテム1</div>
    <div class="item">アイテム2</div>
    <div class="item">アイテム3</div>
</div>
```
{% endcode %}

<pre class="language-css" data-line-numbers><code class="lang-css"><strong>.container {
</strong><strong>    display: flex;  /* この要素をフレックスコンテナにする */
</strong>    background-color: #f0f0f0;
    padding: 10px;
    border: 1px solid #ccc;
}

<strong>.item {
</strong>    background-color: #4CAF50;  /* 緑色の背景 */
    color: white;
    margin: 5px;
    padding: 15px;
    text-align: center;
    border-radius: 4px;
}
</code></pre>

この例を試すと、3つの緑色のボックスが横に並びます。これが基本的なフレックスボックスの動作です。

フレックスボックスの重要な概念：

![フレックスボックスの基本概念](https://claude.ai/.gitbook/assets/flexbox-basic.png)

* **フレックスコンテナー**: `display: flex` を適用した親要素
* **フレックスアイテム**: フレックスコンテナーの直接の子要素
* **主軸 (Main Axis)**: アイテムが並ぶ方向 (デフォルトは横方向)
* **交差軸 (Cross Axis)** : 主軸に対して垂直な方向

> 📝 **ポイント**: フレックスボックスでは、親要素 (コンテナー) のプロパティが子要素 (アイテム) のレイアウトを制御します。コンテナーにスタイルを当てると、その中のすべてのアイテムの配置が変わります。

### 4.3 フレックスコンテナーのプロパティ

#### 4.3.1 flex-direction

アイテムを並べる方向 (主軸の方向) を指定します。

```css
.container {
    display: flex;
    flex-direction: row;  /* 左から右へ（デフォルト） */
}
```

<table><thead><tr><th width="179.93359375">値</th><th>説明</th></tr></thead><tbody><tr><td><code>row</code></td><td>横方向 (左から右) に配置 (デフォルト)</td></tr><tr><td><code>row-reverse</code></td><td>横方向 (右から左) に配置</td></tr><tr><td><code>column</code></td><td>縦方向 (上から下) に配置</td></tr><tr><td><code>column-reverse</code></td><td>縦方向 (下から上) に配置</td></tr></tbody></table>

#### 4.3.2 flex-wrap

アイテムが 1行に収まらない場合に、折り返すかどうかを指定します。

```css
.container {
    display: flex;
    flex-wrap: wrap;  /* 幅が足りない場合は折り返す */
}
```

<table><thead><tr><th width="180.03125">値</th><th>説明</th></tr></thead><tbody><tr><td><code>nowrap</code></td><td>折り返さない (デフォルト)</td></tr><tr><td><code>wrap</code></td><td>折り返す</td></tr><tr><td><code>wrap-reverse</code></td><td>逆方向に折り返す</td></tr></tbody></table>

#### 4.3.3 justify-content

主軸 (デフォルトでは横方向) に沿ったアイテムの配置を制御します。

```css
.container {
    display: flex;
    justify-content: center;  /* 中央揃え */
}
```

<table><thead><tr><th width="179.7890625">値</th><th>説明</th></tr></thead><tbody><tr><td><code>flex-start</code></td><td>主軸の始点に寄せる (デフォルト)</td></tr><tr><td><code>flex-end</code></td><td>主軸の終点に寄せる</td></tr><tr><td><code>center</code></td><td>主軸の中央に配置</td></tr><tr><td><code>space-between</code></td><td>両端に配置し、残りのスペースを均等に分配</td></tr><tr><td><code>space-around</code></td><td>アイテムの周囲に均等なスペースを配置</td></tr><tr><td><code>space-evenly</code></td><td>すべての間隔を均等に</td></tr></tbody></table>

#### 4.3.4 align-items

交差軸 (デフォルトでは縦方向) に沿ったアイテムの配置を制御します。

```css
.container {
    display: flex;
    align-items: center;  /* 交差軸の中央に配置 */
}
```

<table><thead><tr><th width="180.35546875">値</th><th>説明</th></tr></thead><tbody><tr><td><code>stretch</code></td><td>コンテナの高さいっぱいに伸ばす (デフォルト)</td></tr><tr><td><code>flex-start</code></td><td>交差軸の始点に寄せる</td></tr><tr><td><code>flex-end</code></td><td>交差軸の終点に寄せる</td></tr><tr><td><code>center</code></td><td>交差軸の中央に配置</td></tr><tr><td><code>baseline</code></td><td>テキストのベースラインに合わせる</td></tr></tbody></table>

> 📝 **ポイント**: `justify-content` は主軸方向、`align-items` は交差軸方向の配置を制御します。この 2つのプロパティを組み合わせることで、さまざまな配置が可能になります。

### 4.4 フレックスアイテムのプロパティ

#### 4.4.1 flex-grow

コンテナー内の余白をアイテムにどのように分配するかを指定します。値が大きいほど、より多くのスペースを取ります。

```css
.item {
    flex-grow: 1;  /* 余白を均等に分配 */
}

.item.large {
    flex-grow: 2;  /* 他のアイテムの 2倍のスペースを取る */
}
```

#### 4.4.2 flex-shrink

コンテナーが小さくなったときに、アイテムがどのように縮小するかを指定します。値が大きいほど、より多く縮小します。

```css
.item {
    flex-shrink: 1;  /* デフォルト値 */
}

.item.fixed {
    flex-shrink: 0;  /* 縮小しない */
}
```

#### 4.4.3 flex-basis

アイテムの基本サイズを指定します。これは、伸縮する前の初期サイズです。

```css
.item {
    flex-basis: 200px;  /* 初期幅 (または高さ) として 200px */
}
```

#### 4.4.4 flex (一括指定)

`flex` プロパティは、`flex-grow`、`flex-shrink`、`flex-basis` の 3つの値を一度に指定できる便利なショートハンドプロパティです。

```css
.item {
    /* flex: flex-grow flex-shrink flex-basis; */
    flex: 1 1 auto;  /* 余白があれば伸び、狭ければ縮み、初期サイズは内容に基づく */
}
```

以下が、異なる `flex` 値を持つアイテムがどのように表示されるかの例です。

```html
<div class="flex-container">
    <div class="flex-item item1">flex: 1 1 100px;</div>
    <div class="flex-item item2">flex: 2 1 100px;</div>
    <div class="flex-item item3">flex: 1 1 200px;</div>
</div>
```

```css
.flex-container {
    display: flex;
    background-color: #f5f5f5;
    border: 1px solid #ddd;
    padding: 10px;
}

.flex-item {
    padding: 20px;
    text-align: center;
    color: white;
    margin: 5px;
    border-radius: 4px;
}

.item1 {
    background-color: #3498db;
    flex: 1 1 100px;  /* 1倍の割合で伸び、初期サイズは100px */
}

.item2 {
    background-color: #e74c3c;
    flex: 2 1 100px;  /* 2倍の割合で伸び、初期サイズは100px */
}

.item3 {
    background-color: #2ecc71;
    flex: 1 1 200px;  /* 1倍の割合で伸び、初期サイズは200px */
}
```

この例では、

* すべてのアイテムは `flex-shrink: 1` で、必要に応じて縮みます
* `item1` と `item3` は `flex-grow: 1` で、余ったスペースを 1 の割合で取ります
* `item2` は `flex-grow: 2` で、`item1` や `item3` の 2倍のスペースを取ります
* `item1` と `item2`の初期幅は 100px、`item3` は 200pxです

> 📝 **ポイント**: `flex` プロパティは、3つの値を指定することでアイテムの伸縮性と初期サイズをコントロールできます。よく使われる組み合わせとしては、
>
> * `flex: 1;` = `flex: 1 1 0%;`  (均等に伸び縮みし、内容サイズを無視)
> * `flex: auto;` = `flex: 1 1 auto;`  (均等に伸び縮みし、内容サイズを考慮)
> * `flex: none;` = `flex: 0 0 auto;`  (伸び縮みせず、内容サイズを維持)

### 4.5 フレックスボックスの実用例

#### 4.5.1 ナビゲーションメニュー

横並びのナビゲーションメニューを簡単に作成できます。

```html
<nav class="navbar">
    <div class="logo">Logo</div>
    <ul class="menu">
        <li><a href="#">ホーム</a></li>
        <li><a href="#">サービス</a></li>
        <li><a href="#">お問い合わせ</a></li>
    </ul>
</nav>
```

```css
.navbar {
    display: flex;
    justify-content: space-between;  /* ロゴとメニューを左右に配置 */
    align-items: center;  /* 垂直方向に中央揃え */
    padding: 10px;
    background-color: #333;
    color: white;
}

.menu {
    display: flex;  /* メニュー項目を横並びに */
    list-style: none;  /* リストマーカーを削除 */
    margin: 0;
    padding: 0;
}

.menu li {
    margin-left: 20px;  /* メニュー項目間の余白 */
}

.menu a {
    color: white;
    text-decoration: none;
}
```

#### 4.5.2 カードレイアウト

情報カードを均等に配置するレイアウトも簡単に作成できます。

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
    flex-wrap: wrap;  /* 幅が狭いときに折り返す */
    gap: 20px;  /* カード間の余白 */
}

.card {
    flex: 1;  /* 余白を均等に分配 */
    min-width: 250px;  /* 最小幅を設定することで、小さい画面で折り返される */
    padding: 20px;
    background-color: #f5f5f5;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}
```

> 📝 **ポイント**: `flex-wrap: wrap` と `min-width` を組み合わせることで、レスポンシブなカードレイアウトを実現できます。

#### 4.5.3 中央揃えレイアウト

要素を水平・垂直方向に完全に中央に配置するのは、フレックスボックス以前は複雑でしたが、今ではとても簡単です。

```html
<div class="center-container">
    <div class="centered-content">
        <h1>中央に配置されたコンテンツ</h1>
        <p>このテキストは中央に配置されています。</p>
    </div>
</div>
```

```css
.center-container {
    display: flex;
    justify-content: center;  /* 水平方向の中央揃え */
    align-items: center;  /* 垂直方向の中央揃え */
    height: 100vh;  /* ビューポートの高さいっぱいに */
}
```

### 4.6 フレックスボックスとレスポンシブデザイン

フレックスボックスは、さまざまな画面サイズに対応するレスポンシブデザインに最適です。

#### 4.6.1 基本的なレスポンシブレイアウト

```css
.container {
    display: flex;
    flex-wrap: wrap;  /* 小さい画面で折り返す */
}

.item {
    flex: 1;
    min-width: 300px;  /* この幅以下になると折り返される */
}
```

#### 4.6.2 メディアクエリーとの組み合わせ ⭐

メディアクエリーと組み合わせることで、画面サイズに応じてレイアウトを変更できます。

```css
.container {
    display: flex;
    flex-direction: row;  /* デフォルトでは横並び */
}

/* 画面幅が768px以下の場合 */
@media (max-width: 768px) {
    .container {
        flex-direction: column;  /* 縦並びに変更 */
    }
}
```

> 📝 **ポイント**: レスポンシブデザインでは、大きい画面から小さい画面へ、または小さい画面から大きい画面へと考えながら設計しましょう。



***

### 演習問題

#### 問題 4-1: フレックスボックスを使ったナビゲーションの作成

以下の HTML を使って、フレックスボックスでナビゲーションバーを作成してください。

```html
<header>
    <nav class="navbar">
        <div class="logo">MyWebsite</div>
        <ul class="nav-menu">
            <li><a href="#">ホーム</a></li>
            <li><a href="#">プロフィール</a></li>
            <li><a href="#">作品</a></li>
            <li><a href="#">お問い合わせ</a></li>
        </ul>
    </nav>
</header>
```

**要件：**

1. ロゴを左側、メニューを右側に配置する
2. メニュー項目は横並びに表示する
3. 全体を垂直方向に中央揃えにする

<details>

<summary>ヒント</summary>

```css
.navbar {
    display: flex;
    justify-content: space-between;  /* 左右に配置 */
    align-items: center;  /* 垂直方向に中央揃え */
    padding: 15px;
    background-color: #333;
    color: white;
}

.nav-menu {
    display: flex;  /* メニュー項目を横並びに */
    list-style: none;
    margin: 0;
    padding: 0;
}

.nav-menu li {
    margin-left: 20px;  /* 項目間の間隔 */
}

.nav-menu a {
    color: white;
    text-decoration: none;
}
```

</details>

#### 問題 4-2: フレックスカードレイアウトの作成

3つのスキルカードを横並びに表示し、小さい画面では縦並びになるレイアウトを作成してください。

```html
<section>
    <h2>スキル</h2>
    <div class="skill-cards">
        <div class="skill-card">
            <h3>HTML</h3>
            <p>ウェブページの構造を定義する言語</p>
        </div>
        <div class="skill-card">
            <h3>CSS</h3>
            <p>ウェブページのスタイルを設定する言語</p>
        </div>
        <div class="skill-card">
            <h3>JavaScript</h3>
            <p>ウェブページに動きをつける言語</p>
        </div>
    </div>
</section>
```

**要件：**

1. 大きい画面では 3つのカードを横並びに、均等な幅で表示する
2. 小さい画面 (モバイル) では縦に積み重なるように表示する
3. カード間に適切な余白を設定する

<details>

<summary>ヒント</summary>

```css
.skill-cards {
    display: flex;
    flex-wrap: wrap;  /* 画面幅が狭いときに折り返す */
    gap: 20px;  /* カード間の余白 */
}

.skill-card {
    flex: 1;  /* 利用可能なスペースを均等に分配 */
    min-width: 250px;  /* この幅より狭くなると折り返される */
    padding: 20px;
    background-color: #f5f5f5;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.skill-card h3 {
    margin-top: 0;
}
```

</details>

#### 問題 4-3: 中央揃えのヒーロセクションの作成 ⭐

ウェブサイトのトップに表示される、内容が中央に配置されたヒーロセクションを作成してください。

```html
<section class="hero">
    <div class="hero-content">
        <h1>あなたの名前</h1>
        <p>ウェブ開発者</p>
        <button class="cta-button">お問い合わせ</button>
    </div>
</section>
```

**要件：**

1. ヒーロセクションは画面の高さいっぱいに表示する
2. コンテンツは水平・垂直方向に完全に中央揃えにする
3. ボタンにはスタイルを適用し、ホバー効果をつける

<details>

<summary>ヒント</summary>

```css
.hero {
    display: flex;
    justify-content: center;  /* 水平方向の中央揃え */
    align-items: center;  /* 垂直方向の中央揃え */
    height: 100vh;  /* ビューポートの高さいっぱい */
    background-color: #f5f5f5;
    text-align: center;
}

.hero-content h1 {
    font-size: 2.5rem;
    margin-bottom: 10px;
}

.hero-content p {
    font-size: 1.2rem;
    margin-bottom: 20px;
    color: #666;
}

.cta-button {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.3s;
}

.cta-button:hover {
    background-color: #45a049;
}
```

</details>
