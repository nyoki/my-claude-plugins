# claude-config-doctor

Claude Code の設定を監査し、改善提案を行うプラグイン。グローバル設定 (`~/.claude/`) とプロジェクト設定 (`.claude/`) の両方に対応。

## 背景

Claude Code の設定ディレクトリには、CLAUDE.md、エージェント、スキル、権限設定など、開発体験に直結する設定が集約されている。しかし、これらの設定は使い続けるうちに肥大化・陳腐化しやすく、定期的な見直しが必要になる。

このプラグインは、ベストプラクティスに基づいて設定を自動監査し、具体的な改善提案を行う。

v0.2.0 より、**Harness Engineering（HE）成熟度評価モード（experimental）** を追加。hooks、フィードバックループ、evaluator パターンなどの動的な強制メカニズムの整備状況を5段階で評価する。HE モードは HE 関連記事群（OpenAI, Anthropic, Martin Fowler 等）のコンセプトを本プラグインが独自に段階化・体系化したものであり、業界標準のフレームワークではない。

## コマンド一覧

| コマンド | 種別 | 説明 |
|---|---|---|
| `/doctor` | Command + Agent | Claude Code の設定を監査する |
| `/doctor --he` | Command + Agent | Harness Engineering 成熟度を評価する（experimental） |
| `/claude-config-guide` | Skill | 設定のベストプラクティスガイドを参照する |
| `/harness-engineering-guide` | Skill | HE のコンセプトと実践方法を参照する |

## 使い方

### `/doctor` — 設定の監査（通常モード）

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

### `/doctor --he` — Harness Engineering 成熟度評価（experimental）

```
> /doctor --he              # スコープを選択
> /doctor user --he         # グローバル設定の HE 成熟度
> /doctor project --he      # プロジェクト設定の HE 成熟度
> /doctor all --he          # 両方
```

出力例:

```
HE Maturity Assessment (user) [Experimental]

  ⚠️ この評価は本プラグイン独自の成熟度モデルに基づいています。

  Level: 2 / 5 (Context Engineering)

  | Level | Name                  | Status | Notes                          |
  |-------|-----------------------|--------|--------------------------------|
  | 1     | Prompt Engineering    | ✅     | CLAUDE.md 88行                 |
  | 2     | Context Engineering   | ✅     | skills 3件, agents 2件         |
  | 3     | Safety Harness        | ❌     | PreToolUse hook 未設定         |
  | 4     | Feedback Loop         | —      | 前提レベル未達成               |
  | 5     | Full Harness          | —      | 前提レベル未達成               |

  Next Steps (Level 3 達成に向けて):
  - [High] settings.json に PreToolUse hook を追加（安全ゲート）
  - [Medium] deny リストとの相互補完を設計
```

## HE 成熟度モデル（本プラグイン独自の整理）

> **Note**: この5段階モデルは HE 関連記事群のコンセプトを参考に、本プラグインが Claude Code ユーザー向けに独自に段階化したものです。元記事にこのモデル自体は存在しません。

| Level | Name | 概要 |
|-------|------|------|
| 1 | Prompt Engineering | CLAUDE.md が存在し基本的な指示がある |
| 2 | Context Engineering | skills/agents で必要な情報をオンデマンド提供 |
| 3 | Safety Harness | PreToolUse hooks で危険な操作を構造的にブロック |
| 4 | Feedback Loop | PostToolUse hooks で linter/formatter を自動フィードバック |
| 5 | Full Harness | evaluator パターン、セッション継続性、自動改善ループ |

## 監査観点

### 通常モード

#### User Scope (~/.claude/)

| 観点 | チェック内容 |
|---|---|
| **CLAUDE.md** | 行数、セクション構成、プロジェクト固有情報の混入、古い情報 |
| **settings.json** | 過剰な権限許可、危険なパターン |
| **agents/** | frontmatter の品質、example の有無、スコープ定義、役割の重複 |
| **skills/** | グローバル vs プロジェクトの適切さ、トリガー条件の明確さ |
| **memory/** | 行数、CLAUDE.md との重複、古い情報 |
| **keybindings.json** | デフォルトとの競合 |
| **plugins/** | 壊れたシンボリックリンク、未使用プラグイン |

#### Project Scope (.claude/)

| 観点 | チェック内容 |
|---|---|
| **CLAUDE.md** | プロジェクト固有情報の有無、グローバルとの重複、古い情報 |
| **settings.json** | チーム共有設定の適切さ、過剰な権限許可 |
| **settings.local.json** | 個人設定の分離、.gitignore への登録 |
| **commands/** | frontmatter の品質、プロジェクト固有性 |
| **agents/** | frontmatter の品質、プロジェクト固有性、グローバルとの役割重複 |
| **skills/** | プロジェクト固有性、トリガー条件の明確さ |

#### Cross-Scope (all)

| 観点 | チェック内容 |
|---|---|
| **重複検出** | CLAUDE.md 間の内容重複 |
| **配置の適切さ** | グローバルに置かれたプロジェクト固有設定、またはその逆 |
| **矛盾検出** | スコープ間の設定矛盾 |

### HE モード（experimental）

> 各項目には [HE原則]（元記事で明確に主張）/ [Claude Code固有]（Claude Code API へのマッピング）/ [独自解釈]（本プラグイン独自のガイダンス）のラベルが付与されます。

| Level | チェック内容 |
|---|---|
| **L1: Prompt** | CLAUDE.md 存在、基本的な指示内容 |
| **L2: Context** | ポインター構造（60行以下）、skills/agents の有無、MCP接続数、セキュリティ意識 |
| **L3: Safety** | 安全ゲート（hooks/deny リスト）、権限設定との整合性 |
| **L4: Feedback** | 自動フィードバック（hooks等）、フィードバック品質、ブラウザベース検証 |
| **L5: Full** | Evaluator パターン、セッション継続性、自動改善ループ、コスト意識、コンテキスト劣化対策 |

## 設計方針

- **読み取り専用**: 監査エージェントはファイルを一切変更しない。改善はユーザーが判断する
- **プライバシー重視**: シークレットや認証情報が見つかった場合、値を公開せずに警告のみ行う
- **事実ベース**: 推測による指摘は行わない。明確に基準を満たさない項目のみ報告する
- **段階的評価**: HE モードではレベルを順に評価し、前提レベル未達成の場合は上位を評価しない

## Acknowledgments

このプラグインの監査基準は、[Claude Code の公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code)および
Claude Code の開発者である Boris Cherny 氏 (@bcherny) が X 上で公開した
[Claude Code Tips スレッド](https://x.com/bcherny/status/2017742741636321619)
をはじめとする、コミュニティの知見をもとに作成しました。

HE モードの監査基準は、以下の記事群をもとに設計しました:
- [Harness Engineering (OpenAI, 2026/2)](https://openai.com/index/harness-engineering/)
- [Harness Engineering (Martin Fowler, 2026/2)](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)
- [Harness Design for Long-Running Apps (Anthropic, 2026/3)](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [Effective Harnesses for Long-Running Agents (Anthropic, 2025/11)](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Skill Issue: Harness Engineering (HumanLayer, 2026/3)](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
