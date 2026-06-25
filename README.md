# claude-project-template

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

Claude Code を使ったプロジェクト開発をすぐに始めるための GitHub テンプレートリポジトリです。
クローンした瞬間から Claude Code が正しく動作する設定ファイル一式を提供します。

---

## このテンプレートについて

### Claude Code とは

[Claude Code](https://claude.ai/code) は Anthropic が提供する AI 開発支援 CLI ツールです。
プロジェクトのソースコードや設定ファイルを読み込み、コード生成・レビュー・デバッグ・テストなどを
自然言語の指示で実行できます。

Claude Code はプロジェクトルートの `CLAUDE.md` と `.claude/` ディレクトリを自動的に読み込み、
プロジェクト固有のルール・コマンド・権限設定を把握した上で動作します。
**この設定なしに使い始めると、Claude が毎回コマンド実行の確認を求めたり、
プロジェクトの文脈を理解できず的外れな提案をすることがあります。**

### このテンプレートが提供するもの

| 課題 | このテンプレートの対処 |
| ---- | ---------------------- |
| 毎回同じ設定ファイルを手で作る手間 | すぐに使えるファイル一式を提供 |
| Claude Code の権限設定でつまずく | 安全なデフォルトを設定済み |
| CLAUDE.md に何を書けばいいかわからない | 穴埋め式の雛形を用意 |
| チームでのベースライン不足 | `settings.json` を git 管理してチーム共有 |
| 環境変数の管理方法が属人化する | `.env.example` による標準化 |

### テンプレートの構成と Claude Code の関係

```mermaid
graph TD
    subgraph template["プロジェクトリポジトリ（このテンプレートから作成）"]
        CM["CLAUDE.md\nプロジェクト指示・コマンド・制約"]
        ST["`.claude/settings.json`\n権限・モデル設定"]
        SK["`.claude/skills/review/skill.md`\n/review コマンド定義"]
        EX["`.env.example`\n環境変数テンプレート"]
    end

    CM -->|"起動時に自動読み込み"| CC["Claude Code"]
    ST -->|"権限・モデルを設定"| CC
    SK -->|"/review で呼び出し"| CC
    EX -->|"cp して .env を作成"| APP["アプリケーション実行"]
    CC -->|"コード生成・レビュー・デバッグ"| DEV["開発作業"]
```

---

## 含まれるファイル

```text
./
├── .claude/
│   ├── settings.json          # 権限ホワイトリスト・モデル・hooks（チーム共有）
│   └── skills/
│       └── review/
│           └── skill.md       # /review スラッシュコマンド（コードレビュー）
├── .env.example               # 環境変数のサンプル（実際の値は .env に記入）
├── .gitignore                 # Claude 固有ファイル・OS・エディタ・言語成果物を除外
├── .markdownlint.json         # Markdown linting 設定
├── CLAUDE.md                  # Claude への指示ファイル（穴埋め式）
├── LICENSE                    # Apache License 2.0
└── README.md                  # このファイル
```

### 各ファイルの役割詳細

#### `CLAUDE.md`

Claude Code が**プロジェクト起動時に必ず読み込む**指示ファイルです。
ここに書いた内容が Claude の行動ルールになります。

記載すべき内容：

- プロジェクトの目的・背景（Claude がコードの意図を理解するために必要）
- 使用技術スタックとバージョン（適切なコード生成のために必要）
- ビルド・テスト・Lint のコマンド（Claude がコマンドを実行する際に参照）
- やってはいけない操作（本番 DB への直接接続禁止など）
- 外部サービスの一覧と認証情報の取得方法

#### `.claude/settings.json`

Claude Code の**権限設定**と**モデル設定**を記述します。
チームで共有するため git 管理します。

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": ["Bash(git *)", "Bash(find . *)", "Bash(grep *)"],
    "deny":  ["Bash(rm -rf *)", "Bash(git push --force *)"]
  },
  "hooks": {}
}
```

- `permissions.allow` に追加したコマンドは確認プロンプトなしに実行されます
- `permissions.deny` に追加したコマンドは実行を完全にブロックします
- 個人的な上書き設定は `settings.local.json`（`.gitignore` 除外済み）に書きます

#### `.claude/skills/review/skill.md`

`/review` と入力すると呼び出せるカスタムスラッシュコマンドです。
コードの変更内容に対して、バグ・セキュリティ・パフォーマンス・可読性の観点でレビューを行います。

スキルは `.claude/skills/<name>/skill.md` の形式で追加するだけで
自動的に `/<name>` コマンドとして使えるようになります。

#### `.env.example`

チームで共有する環境変数の**サンプルファイル**です。
実際の値は `.env` に記載し、`.gitignore` で git 管理外にします。

新メンバーはこのファイルを見て必要な環境変数と取得方法を把握できます。

#### `.gitignore`

以下のカテゴリを除外設定済みです：

- Claude Code のセッション固有ファイル（`.claude/projects/`, `.claude/runs/` など）
- macOS / Linux の OS ファイル
- 主要エディタのファイル（vim, JetBrains, VSCode）
- 認証情報・秘密鍵（`.env`, `*.pem`, `*.key` など）
- Python / Node.js / Go / Rust のビルド成果物

---

## クイックスタート

### 前提条件

| ツール | バージョン | インストール |
| ------ | ---------- | ------------ |
| Node.js | v18 以上 | [nodejs.org](https://nodejs.org) |
| git | 最新版 | `brew install git` / `apt install git` |
| gh CLI | 最新版 | `brew install gh` / [GitHub CLI](https://cli.github.com) |
| Claude Code | 最新版 | `npm install -g @anthropic-ai/claude-code` |

Claude Code の初回起動時に Anthropic アカウントへのログインが必要です。

```bash
claude login
```

### テンプレートから新規プロジェクトを作成する

> [!TIP]
> GitHub の「Use this template」ボタンを使う方法と gh CLI を使う方法があります。
> どちらでも同じ結果になります。

#### gh CLI を使う場合（推奨）

```bash
gh repo create my-new-project \
  --template zabaot/claude-project-template \
  --private \
  --clone

cd my-new-project
```

#### GitHub Web UI を使う場合

1. [zabaot/claude-project-template](https://github.com/zabaot/claude-project-template) を開く
2. 右上の「Use this template」→「Create a new repository」をクリック
3. リポジトリ名・公開範囲を設定して「Create repository」
4. ローカルにクローン: `git clone git@github.com:<your-username>/my-new-project.git`

---

## セットアップ（クローン後）

### ステップ 1 — CLAUDE.md を編集する

`CLAUDE.md` を開き、`<!-- -->` のコメント部分をプロジェクトの実際の内容に書き換えます。

```bash
# エディタで開く
vim CLAUDE.md   # または code CLAUDE.md / nano CLAUDE.md
```

最低限これだけ書くと Claude の精度が大きく上がります：

```markdown
## プロジェクト概要
このプロジェクトは〇〇を実現するための API サーバーです。

## 技術スタック
Python 3.12, FastAPI, PostgreSQL 16

## コマンド
\`\`\`bash
# テスト
pytest tests/

# Lint
ruff check .
\`\`\`
```

### ステップ 2 — 環境変数を準備する

```bash
cp .env.example .env
```

`.env` を開き、必要な値を設定します。
`.env` は `.gitignore` により git 管理外です。チームメンバーには別途共有してください。

### ステップ 3 — 依存をインストールする

プロジェクトの技術スタックに合わせてコマンドを実行します。

```bash
# Python の場合
pip install -r requirements.txt

# Node.js の場合
npm install

# Go の場合
go mod download
```

### ステップ 4 — settings.json の権限を確認・調整する

`.claude/settings.json` を開き、プロジェクトで使うコマンドが `permissions.allow` に含まれているか確認します。
含まれていなければ追加してください。

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(find . *)",
      "Bash(grep *)",
      "Bash(ls *)",
      "Bash(cat *)",
      "Bash(echo *)",
      "Bash(pytest *)",
      "Bash(npm test)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

### ステップ 5 — Claude Code を起動する

```bash
claude
```

`CLAUDE.md` の内容を Claude が読み込み、プロジェクトの文脈を理解した状態で対話が始まります。

---

## Claude Code の使い方

### インタラクティブモード（通常の開発）

```bash
claude
```

起動後、自然言語でタスクを指示します。

```text
> テストが全部通るようにバグを直して
> src/auth.py のコードをレビューして
> README.md を英語に翻訳して
```

### 非インタラクティブモード（スクリプト・CI 連携）

```bash
# 標準入力からプロンプトを渡す
claude -p "変更されたファイルの一覧を要約して"

# パイプで渡す
git diff HEAD~1 | claude -p "この差分のリスクを評価して"
```

### `/review` コマンド（同梱スキル）

```text
/review
```

直近の変更に対して以下の観点でレビューを行います：

1. **バグ・ロジックの誤り** — 実行時エラーや論理的な問題
2. **セキュリティ** — SQLインジェクション・XSS・認証漏れなど
3. **パフォーマンス** — N+1 クエリ・不要なループなど
4. **可読性** — 命名の問題・複雑すぎる処理など

出力形式: 問題がある場合は `ファイル名:行番号 — 問題の説明`、問題がない場合は「問題なし」

---

## カスタマイズガイド

### スキルを追加する

`/deploy` コマンドを追加する例：

```bash
mkdir -p .claude/skills/deploy
```

`.claude/skills/deploy/skill.md` を作成：

```markdown
# /deploy

本番環境へのデプロイ前チェックリストを実行してください。

## チェック項目

1. テストがすべて通ること: `pytest tests/`
2. Lint エラーがないこと: `ruff check .`
3. マイグレーションが適用済みであること
4. 環境変数が本番用に設定されていること
```

`claude` を起動後に `/deploy` で呼び出せます。

### Hooks を設定する

`settings.json` の `hooks` にイベント駆動のコマンドを設定できます。

```json
{
  "hooks": {
    "sessionStart": [
      {
        "type": "command",
        "command": "echo 'Claude Code セッション開始'"
      }
    ]
  }
}
```

利用可能なイベント: `sessionStart`, `beforePermissionPrompt` など

### settings.local.json で個人設定を上書きする

チーム共有の `settings.json` を変えずに個人設定を追加したい場合は
`.claude/settings.local.json` を作成します（`.gitignore` 除外済み）。

```json
{
  "model": "opus",
  "theme": "dark"
}
```

---

## 注意事項

> [!WARNING]
> **CLAUDE.md に機密情報を書かない**
>
> `CLAUDE.md` は git 管理されます。API キー・パスワード・接続文字列などは
> `.env`（git 除外済み）に記載してください。
> CLAUDE.md には「.env の `DATABASE_URL` を参照」のように書くだけで十分です。

> [!CAUTION]
> **`settings.json` の過剰な allow は危険**
>
> `"Bash(*)"` のようにワイルドカードで全コマンドを許可すると、
> Claude が意図しないシステム破壊的なコマンドを実行する可能性があります。
> 使用するコマンドを具体的に列挙してください。

> [!NOTE]
> **このREADMEはプロジェクト作成後に書き換えてください**
>
> テンプレートから作成した新規プロジェクトでは、
> この README をプロジェクト固有の内容に完全に書き換えてください。
> このファイルはあくまでテンプレートリポジトリの説明です。

> [!NOTE]
> **settings.json が Claude Code によって自動更新されることがある**
>
> セッション中に Claude Code がパーミッション設定を `settings.json` に追記する場合があります。
> コミット前に `git diff .claude/settings.json` で意図しない変更がないか確認してください。

---

## ライセンス

このテンプレートは [Apache License 2.0](LICENSE) のもとで公開されています。
テンプレートから作成したプロジェクトには本ライセンスは適用されません。
プロジェクトごとに適切なライセンスを選択してください。
