# FBC Press Build And Publish

この文書は、FBC Press 系書籍のビルドと公開運用を整理するための基準です。

## 基本方針

- ローカルで再現できること
- ビルド手順が 1 人依存にならないこと
- 公開前に最低限の検証を通すこと
- 手作業を残す場合でも、その位置を明示すること

## 最低限ドキュメント化すべき項目

- 必要なツール
- セットアップ手順
- ローカルプレビュー手順
- 本番ビルド手順
- 公開手順
- 公開後の確認項目

## 推奨項目

### 1. セットアップ

- 必要ツール名(mdBook など)
- 必要バージョン
- インストール方法
- 共有テーマ [mdbook-book-jp](https://github.com/machida/mdbook-book-jp) の導入。
  新規書籍では最初に `bin/install.sh` でテーマを入れ、`book.toml` の
  `additional-css` を設定する(手順は `THEME_CUSTOMIZATION.md`)

### 2. ローカル確認

- 原稿変更時のプレビュー方法
- 図版変更時の確認方法
- リンク確認方法

### 3. 本番ビルド

- 生成コマンド
- 生成先
- 失敗時の切り分け

### 4. 公開

- どこへ公開するか
- どのブランチや成果物を使うか
- 手動作業があるなら何か

### 5. 公開後確認

- トップページが開く
- 目次リンクが機能する
- 図版が崩れていない
- 内部リンク切れがない
- モバイル表示に致命的崩れがない

## Cloudflare Pages への公開(mdBook・Git 連携)

mdBook 本を Cloudflare Pages に載せ、`main` への push で自動ビルド・自動公開する構成。実績のあるレシピとしてそのまま流用してよい。

### 勘所

- **Cloudflare のビルド環境には mdBook が入っていない**。そのため、バージョンを固定した公式リリースバイナリを取得してから `mdbook build` するビルドスクリプトをリポジトリに置き、ダッシュボードのビルドコマンドはそれ一本に絞る。`cargo install mdbook` はビルドのたびに数分かかるので避け、プリビルドバイナリの取得(数秒)にする。
- **バージョンはローカルと固定一致させる**。ローカルの mdBook を上げたらスクリプトの `MDBOOK_VERSION` も合わせて更新する。CI とローカルで出力が食い違う事故を防ぐ。
- **出力ディレクトリは `book.toml` の `build-dir` と一致させる**(既定は `book`)。`book/` は `.gitignore` に入れ、リポジトリには含めない(ビルドのたびに生成する)。
- **共有テーマは submodule ではなくファイル実体で同梱する**。`additional-css` / `additional-js` が相対パスで解決でき、CI 側で追加取得が要らない状態にしておく。

### ビルドスクリプト(`scripts/cloudflare-build.sh`)

```bash
#!/usr/bin/env bash
set -euo pipefail

# ローカルで確認しているバージョンに固定する。
MDBOOK_VERSION="${MDBOOK_VERSION:-0.4.52}"

ARCHIVE="mdbook-v${MDBOOK_VERSION}-x86_64-unknown-linux-gnu.tar.gz"
URL="https://github.com/rust-lang/mdBook/releases/download/v${MDBOOK_VERSION}/${ARCHIVE}"

echo "==> Downloading mdBook v${MDBOOK_VERSION}"
curl -fsSL "${URL}" -o "${ARCHIVE}"   # -f: HTTP エラーで失敗させる
tar -xzf "${ARCHIVE}"

echo "==> Building book"
./mdbook build
```

### ダッシュボード設定(初回のみ)

Git 連携プロジェクトの作成は Cloudflare ダッシュボードで行う(**wrangler CLI では Git 連携プロジェクトを作れない**。手作業になる位置はここ)。

Workers & Pages → Create → Pages → Connect to Git → リポジトリを選択 → 次を入力:

| 項目 | 値 |
|---|---|
| Production branch | `main` |
| Framework preset | `None` |
| Build command | `bash scripts/cloudflare-build.sh` |
| Build output directory | `book` |

Save and Deploy で初回ビルドが走り、`https://<project>.pages.dev` で公開される。以降は `main` に push すれば自動でビルド・公開される。

### 状態確認(任意・wrangler CLI)

Git 連携そのものに wrangler login は不要だが、デプロイ状況を CLI で見たいときは使える。

```sh
npx wrangler login
npx wrangler pages deployment list --project-name <project>
```

## 避けること

- README に断片的にだけ書いて、本手順が存在しない状態
- ローカルではできないが CI だけで通す運用
- 誰かのローカル環境依存の手順
- 公開後確認をしないまま完了扱いにする
- Cloudflare のビルドで `cargo install mdbook` に頼る(毎回数分かかる。プリビルドバイナリを取得する)
- ビルドスクリプトの mdBook バージョンをローカルと食い違わせたまま放置する
