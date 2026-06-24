# FBC Press Theme Customization

この文書は、FBC Press 系書籍の `theme/` 配下にある CSS / JavaScript の役割と運用ルールを定義します。  
`tailwind-css-textbook-ja` と `hotwire-textbook-ja` を基準にしています。

## 基本方針

- `theme/` は mdBook の見た目と共通 UI を調整する場所とする
- 本文に書かない共通導線は、必要に応じて `theme/` の JS で注入する
- 書籍固有 CSS と共通 CSS の役割を分ける
- JavaScript は最小限に留める

## 推奨構成

```text
theme/
├── README.md
├── mdbook-book-core.css
├── mdbook-book-jp.css
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

既刊の実装では、次のような性質がある。

- `main` 要素を見つけて追加する
- すでに `.textbook-footer` があれば二重挿入しない
- `DOMContentLoaded` 後に処理する
- 文言は短く固定する

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

既刊では、JS に埋め込まれている文言は次の 1 文だけ。

- `本書は FjordBootCamp（フィヨルドブートキャンプ） の教材です。`

この文言はシリーズ内で大きく揺らさない。  
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
