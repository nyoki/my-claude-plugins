# my-claude-plugins

個人用に作成した Claude Code プラグインのコレクションです。用途が合えば自由にお使いください。

## プラグイン一覧

| プラグイン | 説明 |
|-----------|------|
| [prompt-smith](./plugins/prompt-smith/) | Skill/Agent/Command/Plugin の作成・レビューツール |
| [claude-config-doctor](./plugins/claude-config-doctor/) | ~/.claude/ の設定監査・改善提案ツール |
| [aws-knowledge](./plugins/aws-knowledge/) | AWS 関連の質問・調査時に AWS Knowledge MCP Server を自動活用 |

## インストール

### マーケットプレイス経由（推奨）

```bash
claude install gh:nyoki/my-claude-plugins
```

### シンボリックリンク（開発・カスタマイズ用）

ローカルでプラグインを編集しながら使いたい場合はシンボリックリンクが便利です。

```bash
# リポジトリをクローン
git clone https://github.com/nyoki/my-claude-plugins.git

# prompt-smith をインストール
ln -s /path/to/my-claude-plugins/plugins/prompt-smith ~/.claude/plugins/prompt-smith

# claude-config-doctor をインストール
ln -s /path/to/my-claude-plugins/plugins/claude-config-doctor ~/.claude/plugins/claude-config-doctor

# aws-knowledge をインストール
ln -s /path/to/my-claude-plugins/plugins/aws-knowledge ~/.claude/plugins/aws-knowledge
```

### インストール確認

```bash
# Claude Code を起動してバージョン確認
/prompt-smith:version

# 設定監査を実行
/claude-config-doctor:doctor

# AWS 関連の質問をすると自動で aws-expert エージェントが起動
```

## リポジトリ構造

```
my-claude-plugins/
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   ├── prompt-smith/
│   ├── claude-config-doctor/
│   └── aws-knowledge/
└── README.md
```
