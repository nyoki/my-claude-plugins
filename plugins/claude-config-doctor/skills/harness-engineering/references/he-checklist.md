# Harness Engineering 監査チェックリスト

`/doctor --he` コマンドおよび `cc-he-doctor` エージェントが参照する HE 成熟度の監査基準。

> **⚠️ Experimental**: HE モードは実験的機能です。本チェックリストは、HE 関連記事群（OpenAI, Anthropic, Martin Fowler, HumanLayer 等）のコンセプトを **本プラグイン独自に Claude Code 向けへ段階化・体系化したもの** であり、業界標準のフレームワークではありません。元記事の主張と本プラグイン独自の解釈を区別するため、各項目にソースラベルを付与しています。

## ラベル体系

**重要度:**
- **[必須]** — そのレベルの達成に不可欠
- **[推奨]** — 達成していると品質が向上する
- **[要確認]** — 状況によっては該当しない場合もある

**ソース:**
- **[HE原則]** — 元記事で明確に主張されている概念
- **[Claude Code固有]** — Claude Code の機能に合わせた本プラグイン独自のマッピング
- **[独自解釈]** — 元記事から合理的に推論した本プラグイン独自のガイダンス

---

# HE 成熟度モデル（本プラグイン独自の整理）

> **Note**: この5段階モデルは、元記事群のコンセプト（特に Bouchard の3層分類 [Prompt → Context → Harness] や、各記事で言及されるハーネスの構成要素）を参考に、**本プラグインが Claude Code ユーザー向けに独自に段階化したもの** です。元記事にこの段階モデル自体は存在しません。

| Level | Name | 概要 | 主な参考元 |
|-------|------|------|-----------|
| 1 | Prompt Engineering | CLAUDE.md が存在し、基本的な指示がある | Bouchard 3層分類の第1層 |
| 2 | Context Engineering | skills, agents, MCP で必要な情報をオンデマンド提供 | Bouchard 第2層, OpenAI, HumanLayer |
| 3 | Safety Harness | 安全ゲートで危険な操作を構造的にブロック | OpenAI (linter), HumanLayer (hooks) |
| 4 | Feedback Loop | ツール実行後に linter/formatter の結果を自動フィードバック | OpenAI, HumanLayer, Martin Fowler |
| 5 | Full Harness | evaluator パターン、セッション継続性、自動改善ループ | Anthropic, OpenAI |

各レベルは前のレベルを前提とする（Level 3 を達成するには Level 1, 2 も満たす必要がある）。この順序は元記事群の「段階的に複雑さを増す」という示唆に基づく独自設計であり、唯一の正しい順序ではない。

---

# Level 1: Prompt Engineering

基本的な指示がコードベースに存在する段階。

## CLAUDE.md の存在と基本品質

- [ ] **[必須]** **[HE原則]** CLAUDE.md が存在する（グローバルまたはプロジェクト）
- [ ] **[必須]** **[HE原則]** 技術スタック、ビルドコマンド、コーディング規約のいずれかが記述されている
- [ ] **[推奨]** **[独自解釈]** 言語設定（対話言語、コメント言語）が明示されている

---

# Level 2: Context Engineering

必要な情報をオンデマンドで提供する仕組みがある段階。

## ポインター構造

- [ ] **[推奨]** **[HE原則]** CLAUDE.md が 60行以下に収まっている
  - 出典: HumanLayer — "under 60 lines"; OpenAI — AGENTS.md を "table of contents" として設計
- [ ] **[推奨]** **[HE原則]** 詳細情報が skills/ や docs/ にリンクで分離されている（Progressive Disclosure）
  - 出典: OpenAI — "agents start with a small, stable entry point and are taught where to look next"
- [ ] **[要確認]** **[HE原則]** CLAUDE.md が実際のコードベースのパターンと一致している（願望ではなく現実を反映）
  - 出典: HumanLayer — ETH Zurich 研究で、実態と乖離した CLAUDE.md は逆効果とされている

## Skills / Agents

- [ ] **[必須]** **[Claude Code固有]** skills/ または agents/ ディレクトリが存在し、1つ以上のファイルがある
- [ ] **[推奨]** **[HE原則]** 繰り返し行う操作が再利用可能な単位として定義されている
- [ ] **[要確認]** **[HE原則]** MCP サーバーの接続数が適切（不要な接続がコンテキストを圧迫していないか）
  - 出典: HumanLayer — "Too many tools push agents into 'the dumb zone'"

## セキュリティ意識

- [ ] **[要確認]** **[HE原則]** MCP サーバーやスキルの導入時にプロンプトインジェクションのリスクを考慮している
  - 出典: HumanLayer — MCP サーバー経由の prompt injection リスク、信頼できないスキルレジストリの危険性を指摘

---

# Level 3: Safety Harness

危険な操作を構造的にブロックする仕組みがある段階。元記事では custom linter、構造テスト、アーキテクチャ制約など幅広い手段が言及されている。以下は Claude Code の hook API を通じた実装パターン。

## 安全ゲート

- [ ] **[必須]** **[Claude Code固有]** settings.json の hooks に PreToolUse が1つ以上定義されている、または deny リストで同等の保護が実現されている
  - 元記事での対応概念: OpenAI — custom linter による architectural enforcement; HumanLayer — hooks による安全ゲート
- [ ] **[推奨]** **[Claude Code固有]** 以下のいずれかの安全ゲートが実装されている:
  - `--no-verify` フラグのブロック
  - `.env` / シークレットファイルの編集ブロック
  - linter/formatter 設定ファイルの改変ブロック
  - destructive コマンドのブロック

## 権限設定との整合性

- [ ] **[要確認]** **[Claude Code固有]** settings.json の deny リストが安全ゲートとして機能している
- [ ] **[要確認]** **[Claude Code固有]** PreToolUse hooks と deny リストの間に冗長な重複がないか（deny は静的ブロック、hooks は動的検証として役割分担）

---

# Level 4: Feedback Loop

ツール実行後に自動的なフィードバックを提供する仕組みがある段階。

## 自動フィードバック

- [ ] **[必須]** **[Claude Code固有]** settings.json の hooks に PostToolUse が1つ以上定義されている、または同等の自動フィードバック機構がある
  - 元記事での対応概念: OpenAI — "Error messages inject remediation instructions into agent context"; HumanLayer — hooks でビルド/型エラーを agent に注入
- [ ] **[推奨]** **[Claude Code固有]** 以下のいずれかのフィードバックループが実装されている:
  - formatter の自動実行（Edit/Write 後）
  - linter の自動実行（Edit/Write 後）
  - 型チェックの自動実行（Edit/Write 後）
- [ ] **[推奨]** **[HE原則]** hook の出力設計が「成功時は silent、エラー時のみ通知」になっている
  - 出典: HumanLayer — "context-efficient — success is silent, and only failures produce verbose output"

## Stop Hooks

- [ ] **[要確認]** **[Claude Code固有]** Stop hook でテスト通過確認が実装されている
- [ ] **[要確認]** **[Claude Code固有]** Stop hook でセッション状態の永続化が実装されている

## フィードバック品質

- [ ] **[推奨]** **[HE原則]** linter エラーメッセージに修正方法が含まれている
  - 出典: OpenAI — "Error messages inject remediation instructions into agent context"
- [ ] **[要確認]** **[Claude Code固有]** hook のエラー出力が agent にコンテキストとしてフィードバックされる設計になっている

## ブラウザベース検証

- [ ] **[要確認]** **[HE原則]** UI を持つアプリケーションの場合、ブラウザベースの E2E テスト（Playwright, Puppeteer 等）がフィードバックループに組み込まれている
  - 出典: OpenAI — Chrome DevTools MCP; Anthropic — Playwright MCP による evaluator のブラウザ操作; Anthropic (Effective Harnesses) — Puppeteer MCP による E2E テスト

---

# Level 5: Full Harness

評価・改善の自動化ループが確立された段階。

## Evaluator パターン

- [ ] **[要確認]** **[HE原則]** 生成と評価が分離されている（Generator + Evaluator の分離）
  - 出典: Anthropic — "Separating the agent doing the work from the agent judging it proves to be a strong lever"; 自己評価バイアスは構造的問題であり、外部の evaluator の方が品質を厳しく評価できる
  - PR レビュー用の agent/plugin が存在する
  - テスト品質を評価する仕組みがある
- [ ] **[要確認]** **[HE原則]** 評価基準（rubric）が明示的に定義されている
- [ ] **[要確認]** **[HE原則]** Feature list（JSON 推奨）が検証コントラクトとして機能している
  - 出典: Anthropic (Effective Harnesses) — JSON feature list with passing/failing status; 一度に1つの feature だけ作業する制約

## セッション継続性

- [ ] **[要確認]** **[HE原則]** progress file（JSON 推奨）のパターンが定義されている
  - 出典: Anthropic (Effective Harnesses) — `claude-progress.txt`; JSON は agent が不注意に書き換えにくい
- [ ] **[要確認]** **[HE原則]** CLAUDE.md にセッション開始時の手順が記述されている（git log 読み取り、progress file 確認等）
  - 出典: Anthropic (Effective Harnesses) — セッション開始時に git log + progress file を読む手順
- [ ] **[要確認]** **[HE原則]** git commit メッセージがセッション間の状態引き継ぎとして機能する設計になっている
  - 出典: Anthropic (Effective Harnesses) — "commit its progress to git with descriptive commit messages"

## 自動改善ループ

- [ ] **[要確認]** **[HE原則]** 繰り返し発生する違反を linter/hook に昇格する運用がある
  - 出典: Mitchell Hashimoto — "anytime you find an agent makes a mistake, you take the time to engineer a solution such that the agent never makes that mistake again"
- [ ] **[要確認]** **[HE原則]** 定期的なハーネス見直しの仕組みがある（garbage-collection agent、定期監査等）
  - 出典: OpenAI — "recurring cleanup processes"; Martin Fowler — "garbage collection"
- [ ] **[要確認]** **[HE原則]** モデルアップデート時にハーネスの必要性を再評価する運用がある
  - 出典: Anthropic — "Every component in a harness encodes an assumption about what the model can't do on its own, and those assumptions are worth stress testing"

## コスト意識

- [ ] **[要確認]** **[HE原則]** Full Harness のコスト対効果を認識している
  - 出典: Anthropic — full harness は naive single-agent approach の 20倍以上のコストがかかる場合がある（$9 vs $200）。ハーネスはコスト-品質のトレードオフであり、意図的に選択すべき

## コンテキスト劣化への対策

- [ ] **[要確認]** **[HE原則]** 長時間セッションでのコンテキスト劣化（context anxiety）への対策がある
  - 出典: Anthropic — 長時間実行エージェントにおける context anxiety; HumanLayer — 長いコンテキストでは性能が非線形に劣化する研究結果
  - sub-agent によるコンテキスト隔離、タスクの分割など

---

# Cross-Scope チェック（scope: all の場合）

## グローバル vs プロジェクトの配置

> **Note**: hooks の配置戦略（グローバル vs プロジェクト）は元記事では言及されていない。以下は Claude Code の設定構造に基づく本プラグイン独自の推奨。

- [ ] **[推奨]** **[独自解釈]** 安全ゲート（PreToolUse）はグローバルに配置されている（言語・プロジェクト非依存のため）
- [ ] **[推奨]** **[独自解釈]** 品質フィードバック（PostToolUse）はプロジェクトごとに配置されている（言語・ツールチェーン依存のため）
- [ ] **[要確認]** **[Claude Code固有]** グローバルとプロジェクトの hooks が競合していないか

---

# 評価基準

## レベル判定ルール

- そのレベルの **[必須]** 項目をすべて満たしていれば、そのレベルは「達成」
- **[推奨]** 項目の達成状況は補足情報として表示
- 下位レベルが未達成の場合、上位レベルは評価対象外（Level 1 が未達成なら Level 2 以降は「—」）

## 出力フォーマット

```
HE Maturity Assessment (Experimental)

  ⚠️ この評価は本プラグイン独自の成熟度モデルに基づいています。
  業界標準のフレームワークではありません。

  Level: {達成レベル} / 5 ({レベル名})

  | Level | Name | Status | Notes |
  |-------|------|--------|-------|
  | 1 | Prompt Engineering | ✅/❌ | {補足} |
  | 2 | Context Engineering | ✅/❌ | {補足} |
  | 3 | Safety Harness | ✅/❌ | {補足} |
  | 4 | Feedback Loop | ✅/❌ | {補足} |
  | 5 | Full Harness | ✅/❌ | {補足} |

  Next Steps:
  - {次のレベル達成に必要な具体的アクション}
```
