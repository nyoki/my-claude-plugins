# prompt-smith

Claude Code用のSkill/Agent/Commandプロンプト作成・レビューツールキット。

公式リポジトリ（anthropics/claude-code）のプロンプトパターンを分析し、ベストプラクティスに基づいたプロンプト作成とレビューを支援します。

## コマンド

### `/prompt-smith:review-prompt`

プロンプトファイル（Skill/Agent/Command）の品質をレビューします。

```bash
# 単一ファイル
/prompt-smith:review-prompt @./my-plugin/skills/my-skill/SKILL.md

# バッチレビュー
/prompt-smith:review-prompt @./my-plugin/agents/*.md --mode batch

# 一貫性チェック
/prompt-smith:review-prompt @./my-plugin/ --mode consistency
```

### `/prompt-smith:generate-skill`

Skillファイルを対話形式で生成します。

```bash
/prompt-smith:generate-skill "Docker containerization best practices"
```

### `/prompt-smith:generate-agent`

Agentファイルを対話形式で生成します。

```bash
/prompt-smith:generate-agent "code optimizer"
```

### `/prompt-smith:generate-command`

Commandファイルを対話形式で生成します。

```bash
/prompt-smith:generate-command "deploy workflow"
```

### `/prompt-smith:version`

プラグインのバージョンとメタデータを表示します。

```bash
/prompt-smith:version
```

## スキル

### prompt-creation

「スキルを作りたい」「エージェントを作成したい」「コマンドを追加したい」といった発言で自動的にトリガーされます。Claude Codeのプロンプトパターンとベストプラクティスに関するガイダンスを提供します。

## エージェント

### prompt-reviewer

複数のプロンプトファイルのバッチレビューや、ディレクトリ全体の品質チェックに使用されるサブエージェントです。`/review-prompt` コマンドからも呼び出されます。

## インストール

[リポジトリの README](../../README.md) を参照してください。

## バージョン管理

- バージョン情報: `.claude-plugin/plugin.json`
- バージョン確認: `/prompt-smith:version`
- 変更履歴: [CHANGELOG.md](./CHANGELOG.md)
