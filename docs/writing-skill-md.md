# skill.md の書き方

このテンプレート ([zabaot/claude-project-template](https://github.com/zabaot/claude-project-template)) の詳細ガイドです。

[← README に戻る](../README.md)

---

## スキルとは

スキルは `/<name>` のカスタムスラッシュコマンドを定義する仕組みです。
`.claude/skills/<name>/skill.md` を作成するだけで、自動的に `/<name>` コマンドとして使えるようになります。

CLAUDE.md に書くには長すぎる**繰り返しの手順**や**チェックリスト**を切り出すのに適しています。
呼び出したときだけ読み込まれるため、コンテキストの節約にもなります。

---

## 基本構造

```markdown
---
description: このスキルの用途説明（Claude が自動選択する際に参照される）
---

# スキル名

ここに Claude への指示を自然言語で記述します。
```

ファイルの配置:

```text
my-project/
└── .claude/
    └── skills/
        └── review/           ← スキル名（= コマンド名）
            └── skill.md      ← このファイルに指示を書く
```

`/review` と入力するとこのスキルが呼び出されます。

---

## フロントマターの設定項目

| キー | 説明 | 例 |
| ---- | ---- | -- |
| `description` | スキルの用途。Claude が関連タスク時に自動選択する判断基準になる | `"PR の変更内容をレビューする"` |
| `disable-model-invocation: true` | 手動呼び出し専用にする（Claude が自動選択しなくなる） | `true` |
| `tools` | このスキル内で使えるツールを制限する | `["Bash", "Read"]` |
| `context: fork` | サブエージェントとして隔離実行する | `fork` |

---

## $ARGUMENTS — 引数を受け取る

`/<skill-name> 引数` の形で渡した文字列を `$ARGUMENTS` で参照できます。

```markdown
---
description: 指定したファイルをレビューする
---

# /file-review

$ARGUMENTS のコードを以下の観点でレビューしてください。

1. バグ・ロジックの誤り
2. セキュリティ上の問題
3. パフォーマンス上の問題
```

呼び出し例:

```text
/file-review src/auth.py
```

`$ARGUMENTS` が `src/auth.py` に展開されて実行されます。

---

## !`cmd` — コマンド出力をスキルに埋め込む

バッククォートで囲んだシェルコマンドの実行結果をスキルの本文に展開できます。

```markdown
---
description: 現在の変更状況を確認してレビューする
---

# /check

以下の情報をもとに変更内容を評価してください。

## 現在の差分
!`git diff HEAD`

## 最近のコミット
!`git log --oneline -10`
```

スキルが呼び出されると `git diff HEAD` と `git log --oneline -10` の出力がその場で展開されます。
動的な情報（ファイル差分・テスト結果など）をスキル内に自動で取り込むのに便利です。

---

## context: fork — サブエージェントとして隔離実行する

`context: fork` を指定するとスキルがサブエージェントとして起動し、
メインの会話コンテキストを汚さずに重いタスクを実行できます。

```markdown
---
description: テストを隔離実行して結果を報告する
context: fork
---

# /test-isolated

以下のコマンドでテストを実行し、失敗した項目のみを一覧で報告してください。

\`\`\`bash
pytest tests/ -v --tb=short
\`\`\`
```

使いどころ:

- 大量のファイルを処理するレビュー系スキル
- テスト実行・ビルド・デプロイなどの重い操作
- メイン会話の流れを中断したくないバッチ処理

---

## CLAUDE.md と skill.md の使い分け

| 内容 | 置き場所 |
| ---- | -------- |
| 常に参照が必要なルール・コマンド | `CLAUDE.md` |
| 繰り返す長い手順（デプロイ・レビューなど） | `.claude/skills/<name>/skill.md` |
| 特定の状況にのみ適用するルール | `skill.md`（description で Claude が自動判断） |
| 自分だけが使う個人メモ・手順 | `CLAUDE.local.md` |

> [!TIP]
> CLAUDE.md が 100 行を超えてきたら、長い手順を skill.md に切り出すサインです。
> skill.md はオンデマンド読み込みなので、どれだけ増やしてもコンテキストを消費しません。

---

## スキルの例集

### /deploy（デプロイ前チェック）

```markdown
---
description: 本番環境へのデプロイ前チェックを実行する
---

# /deploy

本番環境へのデプロイ前に以下を確認してください。

1. テストがすべて通ること: `pytest tests/`
2. Lint エラーがないこと: `ruff check .`
3. マイグレーションが適用済みであること
4. 環境変数が本番用に設定されていること（`.env.production` を確認）
5. CHANGELOG.md が更新されていること
```

### /summary（PR 要約）

```markdown
---
description: 現在のブランチの変更を要約して PR 本文を生成する
---

# /summary

以下の情報をもとに、この PR の日本語要約と変更の背景を作成してください。

## 差分
!`git diff main...HEAD`

## コミット履歴
!`git log main...HEAD --oneline`
```
