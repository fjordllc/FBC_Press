# FBC Press Repository Structure

この文書は、FBC Press 系リポジトリのディレクトリ構成と命名規則を定義します。  
目的は、書籍ごとの差を減らし、原稿、図版、設定、公開物の場所を迷わないようにすることです。

## 基本方針

- 原稿、図版、設定、補助文書を分ける
- 人が読むファイルとビルド用ファイルを混在させすぎない
- 書籍ごとに構成を大きく変えない
- ファイル名から役割が分かるようにする

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

- 章ファイルは `chapter1.md` のように連番で統一する
- 部の導入は `index.md` にする
- 部末演習は `exercises.md` にする
- 図版は `fig-4-1.svg` のように章番号と図番号を含める
- 付録は `a.md` `b.md` のように短く固定するか、運用を決めて統一する

## 避けること

- 書籍ごとにディレクトリ名を大きく変える
- 図版を複数の場所に分散させる
- 一時ファイルやエクスポート済み画像を原稿と同列に置く
- 命名規則を途中から変える
