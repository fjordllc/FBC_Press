# FBC Press Repository Structure

この文書は、FBC Press 系リポジトリのディレクトリ構成と命名規則を定義します。  
目的は、書籍ごとの差を減らし、原稿、図版、設定、公開物の場所を迷わないようにすることです。

## 基本方針

- 原稿、図版、設定、補助文書を分ける
- 人が読むファイルとビルド用ファイルを混在させすぎない
- 書籍ごとに構成を大きく変えない
- ファイル名から役割が分かるようにする

## リポジトリ名のルール

- 書籍リポジトリ名は原則 `kebab-case` にする
- 日本語書籍の原稿リポジトリは、原則として末尾に `-ja` を付ける
- 単語は内容が分かる英語でつなぐ
- 空白、大文字、日本語、曖昧な略称は避ける
- シリーズ内で `book` / `textbook` などの語を混在させる場合は、意味の違いがあるときだけにする

### 既存例

- `hotwire-textbook-ja`
- `tailwind-css-textbook-ja`
- `html-trivia-book-ja`

### 運用上の解釈

- `textbook` は体系的に学ぶ教科書型の本に向く
- `book` は教科書より広い形式でも使える
- どちらを採用するかは企画時に決め、同じ本の関連リポジトリで揺らさない
- 将来的に英語版を作るなら、`-ja` に対して `-en` のように言語コードで対応できる形を保つ

## book.toml のメタデータ規則

`book.toml` の表示タイトルと著者は、シリーズで揃える(mdBook のヘッダやタブに出るため、揺れると別シリーズに見える)。

- **`title` は `FBC Press: <書名>` 形式にする。** 接頭辞 `FBC Press: ` を必ず付ける。
  - 例: `FBC Press: Tailwind CSS` / `FBC Press: Hotwire` / `FBC Press: 仕事に役立たない HTML の雑学`
- 書名部分に半角英数字と日本語が混じるときは、**和欧間スペース**を入れる(`HTMLの` ではなく `HTML の`。`STYLE_GUIDE.md` 1.1 に準拠)。
- **`authors` はシリーズで統一する**(既刊は `["FjordBootCamp"]`)。書籍ごとに表記を変えない。
- `language = "ja"` を入れる(日本語テーマの前提)。
- リポジトリ直下に **`LICENSE` を置き、シリーズで統一する**(既刊は MIT License、`Copyright (c) <年> FjordBootCamp`)。第三者画像を入れる場合はそのライセンスとの整合も確認する(`ASSET_RULES.md`)。

## 推奨構成

```text
book-project/
├── README.md
├── book.toml
├── manuscript/
│   ├── SUMMARY.md
│   ├── preface.md
│   ├── part1/
│   │   ├── index.md
│   │   ├── chapter1.md
│   │   └── exercises.md
│   ├── part2/
│   ├── appendix/
│   │   ├── index.md
│   │   ├── a.md
│   │   └── afterword.md
│   └── figures/
├── theme/
├── scripts/
├── docs/
└── book/
```

## 役割

- `manuscript/`
  原稿本体を置く
- `manuscript/partX/`
  各部の原稿を置く
- `manuscript/appendix/`
  付録を置く
- `manuscript/figures/`
  図版ファイルを置く
- `theme/`
  mdBook などのテーマ差分を置く
- `scripts/`
  ビルドや補助スクリプトを置く
- `docs/`
  執筆補助や運用メモを置く
- `book/`
  ビルド生成物を置く

## 命名規則

- ディレクトリ名は原則として `kebab-case` か、既存運用に合わせた短い固定名を使う
- 空白は使わない
- 日本語ディレクトリ名は避ける
- 大文字を含むディレクトリ名は避ける
- 単数形か複数形かを途中で混在させない
- 一時用途の曖昧な名前を避ける
- 章ファイルは `chapter1.md` のように連番で統一する
- 部の導入は `index.md` にする
- 部末演習は `exercises.md` にする
- 図版は `fig-4-1.svg` のように章番号と図番号を含める
- 付録は `a.md` `b.md` のように短く固定するか、運用を決めて統一する

## ディレクトリ名の具体例

良い例:

- `manuscript`
- `part1`
- `appendix`
- `figures`
- `build-scripts`
- `editorial-docs`

避ける例:

- `New Folder`
- `図版`
- `Manuscript`
- `tmp2`
- `misc`
- `final-final`

## 避けること

- 書籍ごとにディレクトリ名を大きく変える
- 図版を複数の場所に分散させる
- 一時ファイルやエクスポート済み画像を原稿と同列に置く
- 命名規則を途中から変える
- ディレクトリ名だけでは役割が分からない状態にする
