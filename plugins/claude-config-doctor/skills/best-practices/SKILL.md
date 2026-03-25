---
name: claude-config-guide
description: This skill should be used when the user asks to "Claude Code の設定方法を知りたい", "~/.claude/ のベストプラクティスを教えて", ".claude/ の設定を整理したい", "how to configure Claude Code", "CLAUDE.md の書き方", or needs guidance on structuring their ~/.claude/ or project .claude/ directory for optimal Claude Code usage.
version: 0.2.0
---

# Claude Code Configuration Guide

`~/.claude/`（グローバル）および `.claude/`（プロジェクト）ディレクトリの各ファイルの役割と、効果的な設定方法のガイド。

## Overview

Claude Code の設定は2つのレベルに分かれる:
- **グローバル設定** (`~/.claude/`): 個人のワークフロー・習慣。すべてのプロジェクトに影響
- **プロジェクト設定** (`.claude/`): プロジェクト固有のルール。チームで git 共有

この2つを適切に使い分けることで、開発体験が大きく変わる。

このガイドは、Claude Code の作成者やコミュニティの知見を評価・統合し、実践的なベストプラクティスとしてまとめたもの。詳細は `references/` を参照。

## グローバル設定 (~/.claude/) の構成

```
~/.claude/
├── CLAUDE.md              # グローバル指示（全プロジェクト共通の行動ルール）
├── settings.json          # 権限・許可設定
├── agents/                # カスタムエージェント定義
├── skills/                # グローバルスキル
├── plugins/               # プラグイン（シンボリックリンク or コピー）
├── keybindings.json       # キーバインド設定
└── memory/                # 自動メモリ（Claude が自動管理）
```

## プロジェクト設定 (.claude/) の構成

```
.claude/
├── CLAUDE.md              # プロジェクト固有の指示（コーディング規約、技術スタック等）
├── settings.json          # チーム共有の権限設定（git 管理）
├── settings.local.json    # 個人の権限設定（.gitignore に追加）
├── commands/              # プロジェクト固有のコマンド
├── agents/                # プロジェクト固有のエージェント
└── skills/                # プロジェクト固有のスキル
```

## Key Concepts

### グローバル設定 vs プロジェクト設定の使い分け

| 設定 | グローバル (~/.claude/) | プロジェクト (.claude/) |
|---|---|---|
| 目的 | 個人のワークフロー・習慣 | プロジェクト固有のルール |
| 共有 | 個人専用 | チームで git 共有 |
| CLAUDE.md | 言語設定、出力スタイル、汎用ルール | コーディング規約、禁止事項、技術スタック |
| agents/ | 汎用エージェント（レビュー、調査等） | プロジェクト固有エージェント |
| skills/ | 個人のワークフロー | プロジェクトのワークフロー |
| settings.json | 個人の権限設定 | チーム共有の権限設定 |

グローバルとプロジェクトのどちらに置くかは、チームの運用方針やプロジェクトの性質に応じて判断する。

### CLAUDE.md の設計原則

**グローバル CLAUDE.md に書くべきもの:**
- 言語設定（対話言語、コメント言語）
- 出力スタイル（タイムゾーン、日付フォーマット）
- ワークフロー規約（タスクサイズに応じた進め方、サブエージェントの使い方）
- 汎用ルール（セキュリティ原則、コード品質の基準）

**プロジェクト CLAUDE.md に書くべきもの:**
- 技術スタック・フレームワーク情報
- ビルド・テストコマンド
- ディレクトリ構造の説明
- コーディング規約・命名規則
- プロジェクト固有の禁止事項

**どちらにも書くべきでないもの:**
- 一時的なタスク情報 → memory/ に自動管理させる
- 抽象的な精神論（「Senior engineer として振る舞え」等）

### settings.json vs settings.local.json

プロジェクトでは2つの設定ファイルを使い分ける:
- **settings.json**: チーム全体で共有する権限設定。git にコミット
- **settings.local.json**: 個人的な権限設定。`.gitignore` に追加

### 簡潔さについて

グローバル CLAUDE.md は全セッションで読み込まれるため、肥大化するとコンテキストを圧迫する。定期的に見直し、不要な記述を削除することで品質を維持できる傾向がある。エージェント定義やスキル定義も同様に、スコープを絞り明確な指示にすることが推奨される。

## Best Practices

✅ DO:
- グローバル CLAUDE.md は「どのプロジェクトでも共通する自分の好み」に限定する
- プロジェクト CLAUDE.md にはチームメンバーが知るべき情報を集約する
- エージェントには具体的な example を2-4個含める
- memory/ は Claude に管理を任せ、手動で編集しない
- 定期的に設定を見直し、古くなった情報を除去する
- 個人設定は settings.local.json に分離し、.gitignore に追加する

❌ DON'T:
- グローバル CLAUDE.md にプロジェクト固有の情報を入れる
- プロジェクト CLAUDE.md に個人の好みを入れる
- 未使用のエージェント・スキルを放置する（コンテキストを無駄に消費する可能性）
- settings.json で過剰に権限を許可する
- memory/ と CLAUDE.md に同じ情報を重複させる
- グローバルとプロジェクトで同じ内容を重複させる

## Additional Resources

### Reference Files
- **`references/checklist.md`** - 監査チェックリストの詳細基準
