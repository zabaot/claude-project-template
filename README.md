# プロジェクト名

<!-- プロジェクトの一言説明 -->

---

> **このテンプレートの使い方**（プロジェクト作成後にこのセクションは削除してください）
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

---

## セットアップ

```bash
# 1. リポジトリをクローン
git clone <repository-url>
cd <project-name>

# 2. 環境変数を準備
cp .env.example .env
# .env を編集して実際の値を設定

# 3. 依存をインストール
# （プロジェクトに合わせて書き換える）
```

## Claude Code で使う

```bash
# インタラクティブモード
claude

# 非インタラクティブモード（CI・スクリプト等）
claude -p "タスクの内容"
```

### 初回のみ: パーミッション確認

`.claude/settings.json` にプロジェクト共有のパーミッション設定が入っています。
ビルド・テストコマンドを追加する場合は `permissions.allow` に追記してください。

## ライセンス

<!-- LICENSE を選択して記述 -->
