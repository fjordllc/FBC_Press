# FBC Press Theme Customization

この文書は、FBC Press 系書籍の `theme/` 配下にある CSS / JavaScript の役割と運用ルールを定義します。  
`tailwind-css-textbook-ja` と `hotwire-textbook-ja` を基準にしています。

## 基本方針

- `theme/` は mdBook の見た目と共通 UI を調整する場所とする
- 本文に書かない共通導線は、必要に応じて `theme/` の JS で注入する
- 書籍固有 CSS と共通 CSS の役割を分ける
- JavaScript は最小限に留める

## テーマの導入(mdbook-book-jp)

`mdbook-book-core.css` と `mdbook-book-jp.css` は、共有テーマ **[mdbook-book-jp](https://github.com/machida/mdbook-book-jp)** から導入します。日本語書籍向けのタイポグラフィとレイアウトを揃えるための共通 CSS で、FBC Press の全書籍がこれを土台にします。

**新しい書籍を作ったら、まずこのテーマを導入してください。** 既刊(`tailwind-css-textbook-ja` / `hotwire-textbook-ja`)はこれを入れて見た目を統一していますが、テーマ未導入のまま書き始めると、シリーズで見た目が揃わなくなります。

### 初回導入

```bash
# テーマを一度どこかに clone しておく
git clone https://github.com/machida/mdbook-book-jp.git ~/dev/mdbook-book-jp

# 対象の書籍ディレクトリに導入する
bash ~/dev/mdbook-book-jp/bin/install.sh ~/dev/your-textbook-ja
```

`install.sh` は次を行います。

- `theme/mdbook-book-core.css` と `theme/mdbook-book-jp.css` を対象リポジトリにコピーする
- `book.toml` に `language = "ja"` と `additional-css = ["theme/mdbook-book-jp.css"]` を追加する

導入後、書籍固有 CSS とフッター JS を `additional-css` / `additional-js` に足します(下記「`book.toml` での読み込み」を参照)。

### 更新

テーマ側が改善されたら、各書籍で再適用します。

```bash
bash ~/dev/mdbook-book-jp/bin/update.sh ~/dev/your-textbook-ja
```

### 編集してはいけないもの

- `mdbook-book-core.css` と `mdbook-book-jp.css` は **テーマからのコピー** です。直接編集しない(更新時に上書きされます)。
- テーマ本体への改善は [mdbook-book-jp](https://github.com/machida/mdbook-book-jp) 側で行う。
- その書籍だけの見た目調整は、必ず **書籍固有 CSS**(`[book-specific].css`)に書く。

## 推奨構成

```text
theme/
├── README.md
├── mdbook-book-core.css   # mdbook-book-jp から導入(直接編集しない)
├── mdbook-book-jp.css     # mdbook-book-jp から導入(直接編集しない)
├── [book-specific].css
└── textbook-footer.js
```

## 各ファイルの役割

### `mdbook-book-core.css`

- 日本語テーマの共通レイアウト層
- 書籍固有ではない、シリーズで再利用できる見た目を置く

### `mdbook-book-jp.css`

- 日本語タイポグラフィ層
- 見出し、本文、表、リストなどの読みやすさを調整する

### 書籍固有 CSS

例:

- `tailwind-css-textbook.css`
- `hotwire-textbook.css`

役割:

- その本だけに必要な見た目調整
- その本のフッターや部品の見た目

### `textbook-footer.js`

役割:

- 各ページの `main` 末尾に、共通の短いフッターを注入する
- 本書が FjordBootCamp の教材であることを示す

**実装は既刊(Hotwire / Tailwind)と一字一句そろえる。** 過去に、文言だけ合わせて要素・リンク・スタイルが違うフッターが作られ、見た目がシリーズから外れた。下のコードを標準とし、書籍ごとに作り直さない。

```js
// 全ページ共通フッター: 本書が FjordBootCamp の教材であることを示す。
// mdBook は章ごとに完全なページを読み込むため、各ページの読み込み時に挿入する。
(function () {
  function addFooter() {
    var main = document.querySelector("main");
    if (!main || document.querySelector(".textbook-footer")) {
      return;
    }
    var footer = document.createElement("footer");
    footer.className = "textbook-footer";
    var link = document.createElement("a");
    link.href = "https://bootcamp.fjord.jp/";
    link.textContent = "FjordBootCamp（フィヨルドブートキャンプ）";
    footer.appendChild(document.createTextNode("本書は "));
    footer.appendChild(link);
    footer.appendChild(document.createTextNode(" の教材です。"));
    main.appendChild(footer);
  }

  if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", addFooter);
  } else {
    addFooter();
  }
})();
```

必須の性質(ここを外すと他書と揃わない):

- 要素は **`<footer class="textbook-footer">`**。`<p>` などにしない。
- 「FjordBootCamp（フィヨルドブートキャンプ）」は **`https://bootcamp.fjord.jp/` へのリンク**にする。プレーンテキストにしない。
- 文言は `本書は ` + リンク + ` の教材です。` の組み立て。前後の半角スペースも含めて変えない。
- `main` を見つけて末尾に追加。すでに `.textbook-footer` があれば二重挿入しない。
- `readyState` を見て、読み込み済みなら即時、`loading` 中なら `DOMContentLoaded` で実行する。
- **見た目を JS に書かない。** `style.xxx` でのインライン指定は禁止。装飾はすべて書籍固有 CSS の `.textbook-footer` に置く(次項)。

### フッターの見た目(標準 CSS)

フッターの装飾は **書籍固有 CSS**(`[book-specific].css`)に次の形で置く。既刊と同じ体裁にするための標準であり、色だけ各書のアクセントに合わせてよい。

```css
/* 全ページ共通フッター（textbook-footer.js が各ページに挿入する） */
.textbook-footer {
  margin-block: 6rem -4rem;
  padding-block: 4rem 3rem;
  border-top: 1px solid color-mix(in srgb, currentColor 18%, transparent);
  font-size: 0.85em;
  text-align: center;
  opacity: 0.85;
}

.textbook-footer a {
  text-decoration: underline;
}
```

- 中央寄せ・上罫線の区切り・`font-size: 0.85em`・`opacity: 0.85` は揃える。
- リンク色を変えたい場合は `.textbook-footer a { color: var(--book-accent); }` のように書籍固有変数で上書きしてよい(Hotwire はこの方式)。
- `.textbook-footer` の定義が CSS に無いと、JS が要素を挿入しても素のテキストのまま表示され、他書と体裁が合わなくなる。**CSS 定義は必須。**

## `book.toml` での読み込み

既刊では、次のように `additional-js` と `additional-css` で読み込んでいる。

```toml
[output.html]
additional-css = ["theme/mdbook-book-jp.css", "theme/book-specific.css"]
additional-js = ["theme/textbook-footer.js"]
```

## フッター JS の運用ルール

- 役割は**短い共通導線の注入**に限定する
- 長文コンテンツや複雑な UI を入れない
- 依存ライブラリを追加しない
- ページごとに異なる内容を出し分けない
- DOM 構造への依存を増やしすぎない

## フッター文言の扱い

既刊では、フッターは次の 1 文だけ。

- `本書は FjordBootCamp（フィヨルドブートキャンプ） の教材です。`

ただし **「FjordBootCamp（フィヨルドブートキャンプ）」の部分は `https://bootcamp.fjord.jp/` へのリンク**にする(プレーンテキストにしない)。この文言とリンクはシリーズ内で大きく揺らさない。  
詳しい紹介は `afterword.md` に置く。

## theme/README.md に書くべきこと

- 各 CSS/JS ファイルの役割
- どれが共通層で、どれが書籍固有か
- フッター JS が存在する場合、その目的

## 避けること

- 本文の重要情報を JS 注入に頼る
- フッター JS に本ごとの長文宣伝を埋め込む
- 書籍固有 CSS に共通 CSS の役割まで混ぜる
- 何のための CSS/JS か分からないままファイルを増やす
