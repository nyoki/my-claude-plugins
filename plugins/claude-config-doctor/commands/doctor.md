---
description: Audit your Claude Code configuration and get improvement suggestions
argument-hint: "[scope] [component] [--he] — scope: user|project|all, component: claude-md|settings|agents|skills|memory|all, --he: Harness Engineering maturity assessment (experimental)"
allowed-tools: ["Agent", "AskUserQuestion"]
---

Audit the user's Claude Code configuration against best practices.

## Mode Detection

Parse `$ARGUMENTS` for the `--he` flag first:

- If `--he` is present in any position → **HE モード**（Harness Engineering 成熟度評価 — experimental）
- Otherwise → **通常モード**（従来の構成品質監査）

Remove `--he` from arguments before parsing scope/component.

## Scope Resolution

Parse remaining `$ARGUMENTS` to determine scope and component:

**Scope (first argument):**
- `user` → `~/.claude/` のみ
- `project` → カレントディレクトリの `.claude/` のみ
- `all` → 両方
- コンポーネント名が直接指定された場合 → スコープ未指定として扱う
- 引数なし → スコープ未指定

**Component (second argument, or first if scope is omitted):**
- 通常モード: `claude-md`, `settings`, `agents`, `skills`, `memory`, `keybindings`, `plugins`
- HE モード: コンポーネント指定は無視される（全体評価のみ）
- 省略時は `all`

**スコープ未指定の場合:**
AskUserQuestion ツールを使って、以下の選択肢をユーザーに提示する:

```
監査対象を選択してください:
  1. user    — ~/.claude/（グローバル設定）
  2. project — カレントディレクトリの .claude/（プロジェクト設定）
  3. all     — 両方
```

ユーザーの回答（`1`, `2`, `3`, `user`, `project`, `all`）に応じてスコープを決定する。

## Execution

### 通常モード

Launch the `cc-doctor` agent with the following prompt:

"Audit the user's Claude Code configuration. Scope: {scope}. Focus on: {component or 'all components'}. Refer to ${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/checklist.md for audit criteria."

### HE モード

Launch the `cc-he-doctor` agent with the following prompt:

"Assess the Harness Engineering maturity of the user's Claude Code configuration. Scope: {scope}. Refer to ${CLAUDE_PLUGIN_ROOT}/skills/harness-engineering/references/he-checklist.md for the maturity model and audit criteria."

## Post-Audit

### 通常モード
- If there are High priority recommendations, ask if the user wants help implementing them
- For CLAUDE.md improvements, offer to load the `claude-config-guide` skill for guidance

### HE モード
- Display the maturity level and next steps prominently
- If the user is at Level 1-2, offer to load the `harness-engineering-guide` skill for implementation guidance
- If the user is at Level 3+, provide specific, actionable recommendations for the next level
