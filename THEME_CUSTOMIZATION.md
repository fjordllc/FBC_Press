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
