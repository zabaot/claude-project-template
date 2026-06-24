# プロジェクト名

<!-- プロジェクトの一言説明 -->

---

> **このテンプレートの使い方**（新規プロジェクト作成後にこのセクションを削除してください）
>
> ```bash
> gh repo create my-new-project \
>   --template zabaot/claude-project-template \
>   --private \
>   --clone
>
> cd my-new-project
> # CLAUDE.md と .env.example を編集して使い始める
> claude
> ```
>
> テンプレートの詳細: [zabaot/claude-project-template](https://github.com/zabaot/claude-project-template)

---

## 概要

<!-- このプロジェクトが何をするものか、なぜ作ったかを2〜3行で説明 -->

## 含まれるファイルの構成

```text
./
├── .claude/
│   ├── settings.json      # Claude Code の権限・モデル設定（チーム共有）
│   └── skills/
│       └── review/
│           └── skill.md   # /review スラッシュコマンド
├── .env.example           # 環境変数のサンプル（実際の値は .env に記載）
├── .gitignore
├── CLAUDE.md              # Claude への指示ファイル
└── README.md              # このファイル
```

## セットアップ

### 1. リポジトリをクローン

```bash
git clone <repository-url>
cd <project-name>
```

### 2. 環境変数を準備

```bash
cp .env.example .env
```

`.env` を開き、必要な値を設定してください。`.env` は `.gitignore` により git 管理外になっています。

### 3. 依存をインストール

```bash
# プロジェクトに合わせて書き換えてください
# 例: npm install / pip install -r requirements.txt
```

### 4. Claude Code をインストール（未インストールの場合）

```bash
npm install -g @anthropic-ai/claude-code
```

## Claude Code で使う

### インタラクティブモード（通常の開発作業）

```bash
claude
```

起動後は自然言語でタスクを指示できます。`CLAUDE.md` に書かれたプロジェクト情報を Claude が自動的に読み込みます。

### 非インタラクティブモード（スクリプト・CI連携）

```bash
claude -p "テストを実行して結果を要約して"
```

### カスタムスラッシュコマンド

このテンプレートには `/review` コマンドが含まれています：

```text
/review
```

コードの変更内容をレビューし、バグ・セキュリティ・パフォーマンス上の問題を指摘します。
`.claude/skills/` にMarkdownファイルを追加することで独自コマンドを増やせます。

## カスタマイズ

### CLAUDE.md — Claude への指示を書く

`CLAUDE.md` はプロジェクト固有の情報を Claude に伝えるファイルです。以下を記述しておくと精度が上がります：

- プロジェクトの目的・背景
- 使用技術スタックとバージョン
- ビルド・テスト・Lint のコマンド
- やってはいけない操作（本番DBへの直接接続禁止など）

### .claude/settings.json — 権限設定

デフォルトでは git・ファイル検索・読み取り系のコマンドを許可しています。
プロジェクトに応じて `permissions.allow` にコマンドを追加してください：

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run build)"
    ]
  }
}
```

`settings.json` はチームで共有するため git 管理します。個人的な設定は `settings.local.json`（git 除外済み）に書いてください。

### .claude/skills/ — スラッシュコマンドを追加する

新しいコマンドを追加するには `skill.md` を作成します：

```bash
mkdir -p .claude/skills/deploy
touch .claude/skills/deploy/skill.md
```

`skill.md` にはコマンドの手順を Markdown で記述します。`claude` を起動後に `/deploy` で呼び出せます。

## ライセンス

このプロジェクトは [Apache License 2.0](LICENSE) のもとで公開されています。
