# claude-config-doctor

Claude Code の設定を監査し、改善提案を行うプラグイン。グローバル設定 (`~/.claude/`) とプロジェクト設定 (`.claude/`) の両方に対応。

## 背景

Claude Code の設定ディレクトリには、CLAUDE.md、エージェント、スキル、権限設定など、開発体験に直結する設定が集約されている。しかし、これらの設定は使い続けるうちに肥大化・陳腐化しやすく、定期的な見直しが必要になる。

このプラグインは、ベストプラクティスに基づいて設定を自動監査し、具体的な改善提案を行う。

## コマンド一覧

| コマンド | 種別 | 説明 |
|---|---|---|
| `/doctor` | Command + Agent | Claude Code の設定を監査する |
| `/claude-config-guide` | Skill | 設定のベストプラクティスガイドを参照する |

## 使い方

### `/doctor` — 設定の監査

引数なしで実行すると、監査対象をインタラクティブに選択:

```
> /doctor

監査対象を選択してください:
  1. user    — ~/.claude/（グローバル設定）
  2. project — カレントディレクトリの .claude/（プロジェクト設定）
  3. all     — 両方
```

スコープを直接指定:

```
> /doctor user              # ~/.claude/ を監査
> /doctor project           # カレントディレクトリの .claude/ を監査
> /doctor all               # 両方を監査
```

スコープ + コンポーネントを指定:

```
> /doctor user claude-md    # グローバル CLAUDE.md のみ
> /doctor project agents    # プロジェクトのエージェントのみ
> /doctor user settings     # グローバルの settings.json のみ
```

出力例:

```
Claude Code Configuration Audit (user)

  | # | Component       | Status | Issues |
  |---|-----------------|--------|--------|
  | 1 | CLAUDE.md       | ⚠️     | 2      |
  | 2 | settings.json   | ✅     | 0      |
  | 3 | agents/         | ⚠️     | 3      |
  | 4 | skills/         | ✅     | 0      |
  | 5 | memory/         | ⚠️     | 1      |
  | 6 | keybindings.json| ➖     | 0      |
  | 7 | plugins/        | ✅     | 0      |

  Recommendations:
  - [High] CLAUDE.md 238行 → 200行以内に整理
  - [Medium] agents/old-reviewer.md に example ブロックがない
  - [Medium] agents/code-checker.md と agents/code-reviewer.md の役割が重複
  - [Medium] agents/data-analyst.md の model が未指定
  - [Low] memory/MEMORY.md に CLAUDE.md と重複する情報がある
```

### `/claude-config-guide` — ベストプラクティスの参照

設定の書き方に迷ったとき、ベストプラクティスを確認する:

```
> /claude-config-guide

Claude: グローバル設定とプロジェクト設定のガイドです。
  何について知りたいですか？

User: グローバル CLAUDE.md とプロジェクト CLAUDE.md の使い分け

Claude: 原則は「2つ以上のプロジェクトで使うならグローバル」です...
```

## 監査観点

### User Scope (~/.claude/)

| 観点 | チェック内容 |
|---|---|
| **CLAUDE.md** | 行数、セクション構成、プロジェクト固有情報の混入、古い情報 |
| **settings.json** | 過剰な権限許可、危険なパターン |
| **agents/** | frontmatter の品質、example の有無、スコープ定義、役割の重複 |
| **skills/** | グローバル vs プロジェクトの適切さ、トリガー条件の明確さ |
| **memory/** | 行数、CLAUDE.md との重複、古い情報 |
| **keybindings.json** | デフォルトとの競合 |
| **plugins/** | 壊れたシンボリックリンク、未使用プラグイン |

### Project Scope (.claude/)

| 観点 | チェック内容 |
|---|---|
| **CLAUDE.md** | プロジェクト固有情報の有無、グローバルとの重複、古い情報 |
| **settings.json** | チーム共有設定の適切さ、過剰な権限許可 |
| **settings.local.json** | 個人設定の分離、.gitignore への登録 |
| **commands/** | frontmatter の品質、プロジェクト固有性 |
| **agents/** | frontmatter の品質、プロジェクト固有性、グローバルとの役割重複 |
| **skills/** | プロジェクト固有性、トリガー条件の明確さ |

### Cross-Scope (all)

| 観点 | チェック内容 |
|---|---|
| **重複検出** | CLAUDE.md 間の内容重複 |
| **配置の適切さ** | グローバルに置かれたプロジェクト固有設定、またはその逆 |
| **矛盾検出** | スコープ間の設定矛盾 |

## 設計方針

- **読み取り専用**: 監査エージェントはファイルを一切変更しない。改善はユーザーが判断する
- **プライバシー重視**: シークレットや認証情報が見つかった場合、値を公開せずに警告のみ行う
- **事実ベース**: 推測による指摘は行わない。明確に基準を満たさない項目のみ報告する

## Acknowledgments

このプラグインの監査基準は、[Claude Code の公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code)および
Claude Code の開発者である Boris Cherny 氏 (@bcherny) が X 上で公開した
[Claude Code Tips スレッド](https://x.com/bcherny/status/2017742741636321619)
をはじめとする、コミュニティの知見をもとに作成しました。
