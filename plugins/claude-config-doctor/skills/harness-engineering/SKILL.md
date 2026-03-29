---
name: harness-engineering-guide
description: |
  This skill should be used when the user asks about "Harness Engineering", "HE成熟度", "ハーネスエンジニアリング", "hooks のベストプラクティス", "AIエージェントの制御", "harness maturity", or needs guidance on implementing harness engineering patterns in their Claude Code setup. This skill provides conceptual guidance and best practices. For auditing the current HE maturity level, use the `/doctor --he` command instead.
version: 0.2.0
---

# Harness Engineering ガイド

Harness Engineering（ハーネスエンジニアリング）のコンセプトと、Claude Code における実践方法のガイド。

> **⚠️ Experimental**: HE モードは実験的機能です。本ガイドは HE 関連記事群のコンセプトを Claude Code 向けに整理したものであり、元記事の主張と本プラグイン独自の解釈を区別して記載しています。

## Harness Engineering とは

**[HE原則]** AIコーディングエージェントが「正しく動作せざるを得ない環境」を構築する手法。
Bouchard の3層分類で位置づけられる:

| 層 | 問い | Claude Code での対応 |
|---|---|---|
| Prompt Engineering | 何を聞くか | ユーザーの入力 |
| Context Engineering | 何を渡すか | CLAUDE.md, skills, agents, MCP |
| Harness Engineering | どう動作させるか | hooks, linters, evaluator agents, progress files, tests |

> 出典: Bouchard "Harness Engineering: The Missing Layer Behind AI Agents"

> **Note**: このスキルは HE の考え方・ベストプラクティスを提供するガイダンスです。現在の設定の HE 成熟度を診断するには、`/doctor --he` コマンドを使用してください。

## HE 成熟度モデル（本プラグイン独自の整理）

> **Note**: この5段階モデルは元記事群のコンセプトを参考に本プラグインが独自に段階化したものです。元記事にこのモデル自体は存在しません。詳細は `references/he-checklist.md` を参照。

| Level | Name | 概要 |
|-------|------|------|
| 1 | Prompt Engineering | CLAUDE.md が存在し基本的な指示がある |
| 2 | Context Engineering | skills/agents で必要な情報をオンデマンド提供 |
| 3 | Safety Harness | 安全ゲートで危険な操作を構造的にブロック |
| 4 | Feedback Loop | ツール実行後に linter/formatter を自動フィードバック |
| 5 | Full Harness | evaluator パターン、セッション継続性、自動改善ループ |

## 主要原則

### 1. CLAUDE.md はポインター文書であるべき [HE原則]

- 50-60行以下を目標とする（出典: HumanLayer）
- 詳細は skills/、docs/、ADR に分離しリンクする（出典: OpenAI — "agents start with a small, stable entry point"）
- 願望ではなく、コードベースの現実を反映する（出典: HumanLayer — ETH Zurich 研究で実態と乖離した CLAUDE.md は逆効果）

### 2. 安全ゲートとフィードバックループ [HE原則 + Claude Code固有]

**[HE原則]** 元記事では custom linter、構造テスト、アーキテクチャ制約など幅広い手段が言及されている。

**[Claude Code固有]** Claude Code では hooks API を通じて以下のように実装できる:

| Hook | 配置 | 用途 |
|---|---|---|
| PreToolUse | グローバル推奨 | 安全ゲート（destructive操作ブロック） |
| PostToolUse | プロジェクトごと | 品質フィードバック（formatter, linter, 型チェック） |
| Stop | 場合による | セッション永続化、テスト通過確認 |

> **Note**: hooks の配置戦略（グローバル vs プロジェクト）は元記事では言及されておらず、Claude Code の設定構造に基づく本プラグインの推奨です。

**[HE原則]** 設計原則: 成功時は silent、エラー時のみ agent に通知（出典: HumanLayer）。

### 3. 評価を生成から分離する [HE原則]

- Generator agent と Evaluator agent を分離する（出典: Anthropic — "Separating the agent doing the work from the agent judging it proves to be a strong lever"）
- 自己評価バイアスは構造的問題であり、外部 evaluator の方が品質を厳しく評価できる（出典: Anthropic engineering blog）
- Claude Code では sub-agent や pr-review-toolkit 等で実現可能
- **コスト意識**: Full Harness は naive approach の 20倍以上のコストになる場合がある（出典: Anthropic — $9 vs $200）。トレードオフを意図的に選択すべき

### 4. セッション継続性を設計する [HE原則]

- progress file（JSON 推奨）でセッション間の状態を引き継ぐ（出典: Anthropic）
- git commit メッセージを memory substrate として活用（出典: Anthropic）
- セッション開始時に `git log` + progress file を読む手順を CLAUDE.md に記述（出典: Anthropic）
- Feature list（JSON）を検証コントラクトとして使用し、一度に1つの feature だけ作業する（出典: Anthropic）

### 5. 繰り返し違反を仕組みで防ぐ [HE原則]

- 同じ違反が繰り返し発生したら、ドキュメントから linter/hook に昇格する
- 出典: Mitchell Hashimoto — "anytime you find an agent makes a mistake, you take the time to engineer a solution such that the agent never makes that mistake again"
- ハーネスはモデルの進化に伴い陳腐化する。定期的に再評価し、不要になったコンポーネントは積極的に削除する（出典: Anthropic — "Every component in a harness encodes an assumption about what the model can't do on its own"）

### 6. コンテキスト劣化に備える [HE原則]

- 長時間セッションではコンテキスト劣化（context anxiety）が発生する（出典: Anthropic, HumanLayer）
- 不要な中間結果がコンテキストを汚染し、非線形に性能が低下する（出典: HumanLayer の引用研究）
- sub-agent によるコンテキスト隔離が有効（出典: HumanLayer — sub-agents as "context firewall"）

### 7. ブラウザベース検証 [HE原則]

- UI を持つアプリケーションでは、ブラウザベースの E2E テストをフィードバックループに組み込む
- 出典: OpenAI — Chrome DevTools MCP; Anthropic — Playwright MCP, Puppeteer MCP

## 段階的な導入ステップ（独自ガイダンス）

> **Note**: 以下の導入順序は元記事の示唆に基づく本プラグインの推奨であり、唯一の正しい順序ではありません。

### Week 1: Level 3 達成（Safety Harness）
1. グローバル settings.json に PreToolUse 安全ゲートを追加 [Claude Code固有]
2. CLAUDE.md にセッション開始手順を追加

### Week 2-3: Level 4 達成（Feedback Loop）
3. プロジェクトの settings.json に PostToolUse formatter/linter hook を追加 [Claude Code固有]
4. Stop hook にテスト通過確認を追加 [Claude Code固有]

### Month 2+: Level 5 を目指す
5. evaluator agent/plugin の導入（pr-review-toolkit 等）
6. progress file パターンの標準化
7. 繰り返し違反の linter/hook 昇格運用を開始

## 参考リソース

### 公式・権威的記事
- [Harness Engineering (OpenAI, 2026/2)](https://openai.com/index/harness-engineering/) — 原典。100万行のプロダクトを 0-code で構築した事例
- [Harness Engineering (Martin Fowler / Böckeler, 2026/2)](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html) — OpenAI 記事の批評的考察
- [Harness Design for Long-Running Apps (Anthropic, 2026/3)](https://www.anthropic.com/engineering/harness-design-long-running-apps) — Generator/Evaluator パターン
- [Effective Harnesses for Long-Running Agents (Anthropic, 2025/11)](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — セッション継続性の設計

### 実践ガイド
- [Skill Issue: Harness Engineering (HumanLayer, 2026/3)](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents) — Claude Code 向けの実践的 Tips
- [Harness Engineering: The Missing Layer (Bouchard, 2026/3)](https://www.louisbouchard.ai/harness-engineering/) — Prompt / Context / Harness の3層分類
