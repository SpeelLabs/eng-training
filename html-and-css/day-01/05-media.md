# 5. 画像の扱い方とメディア要素

### 5.1 HTMLでの画像の基本

Webページに画像を表示するには、`<img>` タグを使用します。これは閉じタグが不要な「空要素」です。

#### 5.1.1 基本的な画像の挿入

```html
<img src="画像のパス" alt="画像の説明">
```

**必須の属性**:

* `src`: 画像ファイルのパス（Source）
* `alt`: 代替テキスト（Alternative Text）- 画像が表示できない場合や、スクリーンリーダーで読み上げられる説明文

```html
<img src="images/profile.jpg" alt="山田太郎のプロフィール写真">
```

> 📝 **ポイント**: `alt` 属性は必ず指定しましょう。視覚障害のあるユーザーのためのアクセシビリティと、SEO（検索エンジン最適化）の両方に重要です。

#### 5.1.2 画像のパス指定

画像のパスには、相対パスと絶対パスの2種類があります。

**相対パス**（現在のHTMLファイルの場所を基準）:

```html
<!-- 同じフォルダ内の画像 -->
<img src="logo.png" alt="ロゴ">

<!-- サブフォルダ内の画像 -->
<img src="images/photo.jpg" alt="写真">

<!-- 一つ上の階層にあるフォルダ内の画像 -->
<img src="../images/banner.jpg" alt="バナー">
```

**絶対パス**（完全なURLまたはルートからのパス）:

```html
<!-- 完全なURLを使用 -->
<img src="https://example.com/images/photo.jpg" alt="写真">

<!-- サイトのルートからの絶対パス -->
<img src="/images/logo.png" alt="ロゴ">
```

#### 5.1.3 サイズ指定

`width` と `height` 属性で画像のサイズを指定できます。

```html
<img src="logo.png" alt="ロゴ" width="200" height="100">
```

サイズはピクセル単位で指定します。CSSでも同様に指定できます：

```css
img {
    width: 200px;
    height: 100px;
}
```

> 📝 **ポイント**: 画像のサイズを指定すると、ページの読み込み時にレイアウトシフトを防ぐことができます。可能であれば元の画像と同じ縦横比を維持するか、片方だけを指定して縦横比を自動的に保つのがベストです。

### 5.2 レスポンシブな画像

異なる画面サイズに対応できるレスポンシブな画像の扱い方を見ていきましょう。

#### 5.2.1 基本的なレスポンシブ画像

CSSを使って、画像を親要素に合わせて伸縮させることができます。

```css
img {
    max-width: 100%;
    height: auto;
}
```

これにより、画像は親要素の幅を超えて表示されず、縦横比が維持されます。

#### 5.2.2 異なるサイズの画像を提供 ⭐

`srcset` 属性を使うと、異なる解像度や画面サイズに合わせて異なる画像を提供できます。

```html
<img src="small.jpg"
     srcset="small.jpg 500w,
             medium.jpg 1000w,
             large.jpg 1500w"
     alt="レスポンシブ画像の例">
```

この例では:

* `500w` などの値は、画像の実際の幅（ピクセル単位）を示します
* ブラウザは画面のサイズに基づいて最適な画像を選択します

### 5.3 背景画像

CSSを使用すると、要素の背景に画像を設定できます。

#### 5.3.1 基本的な背景画像

```css
.hero {
    background-image: url('background.jpg');
    background-size: cover;
    background-position: center;
    height: 300px;
}
```

**主な背景プロパティ**:

* `background-image`: 背景画像を指定
* `background-size`: 画像のサイズ（`cover`, `contain`, または具体的なサイズ）
* `background-position`: 画像の位置
* `background-repeat`: 画像の繰り返し方法（`repeat`, `no-repeat`, など）

#### 5.3.2 複数の背景画像 ⭐

複数の背景画像を重ねて表示することも可能です。

```css
.element {
    background-image: url('overlay.png'), url('background.jpg');
    background-position: center, top left;
    background-repeat: no-repeat, repeat;
    background-size: contain, cover;
}
```

リストの最初の値が最前面に表示されます。

### 5.4 画像の最適化

Web上での画像使用において重要なのは、適切なファイル形式の選択と最適化です。

#### 5.4.1 主な画像形式

| 形式       | 特徴                  | 最適な用途              |
| -------- | ------------------- | ------------------ |
| JPEG/JPG | 圧縮率が高い、写真に適している     | 写真、複雑な画像           |
| PNG      | 透明度をサポート、圧縮率は中程度    | ロゴ、イラスト、透明背景が必要な画像 |
| GIF      | アニメーションをサポート、色数制限あり | 単純なアニメーション、アイコン    |
| SVG      | ベクター形式、拡大しても品質が落ちない | ロゴ、アイコン、シンプルなイラスト  |
| WebP     | 高圧縮・高品質、透明度もサポート    | 最新のブラウザ向けの汎用フォーマット |

#### 5.4.2 画像最適化のポイント

1. **適切なファイル形式を選ぶ**
2. **画像サイズを必要以上に大きくしない**
3. **画像の圧縮ツールを使用する**（TinyPNG, Squoosh など）
4. **レスポンシブ画像技術を活用する**

> 📝 **ポイント**: 画像は一般的にWebページの中で最もファイルサイズが大きい要素です。最適化することで、ページの読み込み速度を大幅に改善できます。

### 5.5 その他のメディア要素

#### 5.5.1 audio要素

音声ファイルを再生するための要素です。

```html
<audio controls>
    <source src="music.mp3" type="audio/mpeg">
    <source src="music.ogg" type="audio/ogg">
    お使いのブラウザは音声要素をサポートしていません。
</audio>
```

**主な属性**:

* `controls`: 再生コントロールを表示
* `autoplay`: 自動再生（多くのブラウザではミュートが必要）
* `loop`: ループ再生

#### 5.5.2 video要素

動画を再生するための要素です。

```html
<video width="320" height="240" controls>
    <source src="movie.mp4" type="video/mp4">
    <source src="movie.webm" type="video/webm">
    お使いのブラウザは動画要素をサポートしていません。
</video>
```

**主な属性**:

* `controls`: 再生コントロールを表示
* `width`, `height`: サイズ指定
* `poster`: 再生前に表示するサムネイル画像

> 📝 **ポイント**: video要素やaudio要素では、複数の形式を指定することで、ブラウザの互換性を高めることができます。

#### 5.5.3 iframe要素 ⭐

外部コンテンツ（YouTubeの動画など）を埋め込むための要素です。

```html
<iframe width="560" height="315" 
        src="https://www.youtube.com/embed/VIDEO_ID" 
        frameborder="0" 
        allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" 
        allowfullscreen>
</iframe>
```

**セキュリティ上の注意点**:

* 信頼できるソースからのコンテンツのみを埋め込む
* 必要に応じて `sandbox` 属性を使用して制限をかける

### 演習問題

#### 問題 5-1: 基本的な画像の挿入

「my-portfolio」プロジェクトに、以下の手順で画像を追加してください：

1. プロジェクト内に「images」フォルダを作成
2. プロフィール写真を追加（または任意の画像を用意）
3. HTML内のプロフィールセクションに画像を挿入
4. 適切な `alt` テキストを設定

\<details> \<summary>ヒント\</summary>

```html
<!-- フォルダ構造 -->
my-portfolio/
├── index.html
├── css/
│   └── style.css
└── images/
    └── profile.jpg

<!-- index.html内のコード -->
<div class="profile">
    <h2>プロフィール</h2>
    <img src="images/profile.jpg" alt="私のプロフィール写真" width="200">
    <p>はじめまして。私は[あなたの名前]です。現在HTML/CSSを学習中です。</p>
</div>
```

\</details>

#### 問題 5-2: レスポンシブな画像スタイルの適用

CSSファイルに、以下のルールを追加してすべての画像をレスポンシブにしてください：

1. すべての画像の最大幅を親要素の100%に設定
2. 高さを自動に設定して縦横比を維持
3. 画像に軽い影効果と角丸を追加

\<details> \<summary>ヒント\</summary>

```css
/* css/style.css に追加 */
img {
    max-width: 100%;
    height: auto;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

/* プロフィール画像用に特別なスタイル */
.profile img {
    display: block;
    margin: 0 auto 15px;
}
```

\</details>

#### 問題 5-3: 背景画像を使ったヘッダーの作成 ⭐

ページ上部にヒーローセクションを作成し、背景画像を追加してください：

1. ヘッダー要素を追加
2. 背景画像を設定（サンプル画像または色のグラデーション）
3. テキストを中央に配置

\<details> \<summary>ヒント\</summary>

```html
<!-- index.html に追加 -->
<header class="hero">
    <h1>私のポートフォリオ</h1>
    <p>Web開発の学習記録</p>
</header>
```

```css
/* style.css に追加 */
.hero {
    background-image: linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url('images/header-bg.jpg');
    background-size: cover;
    background-position: center;
    height: 300px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    color: white;
    margin-bottom: 30px;
}

/* 背景画像がない場合はグラデーションのみでも良い */
.hero {
    background-image: linear-gradient(to right, #3498db, #2c3e50);
    /* 他のプロパティは同じ */
}
```

\</details>
