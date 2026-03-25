---
description: Audit your Claude Code configuration and get improvement suggestions
argument-hint: "[scope] [component] — scope: user|project|all, component: claude-md|settings|agents|skills|memory|all"
allowed-tools: ["Agent"]
---

Audit the user's Claude Code configuration against best practices.

Launch the `cc-doctor` agent to perform the audit and return a structured report.

## Scope Resolution

Parse `$ARGUMENTS` to determine scope and component:

**Scope (first argument):**
- `user` → `~/.claude/` のみ
- `project` → カレントディレクトリの `.claude/` のみ
- `all` → 両方
- コンポーネント名が直接指定された場合 → スコープ未指定として扱う
- 引数なし → スコープ未指定

**Component (second argument, or first if scope is omitted):**
- `claude-md`, `settings`, `agents`, `skills`, `memory`, `keybindings`, `plugins`
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

Launch the cc-doctor agent with the following prompt:

"Audit the user's Claude Code configuration. Scope: {scope}. Focus on: {component or 'all components'}. Refer to ${CLAUDE_PLUGIN_ROOT}/skills/best-practices/references/checklist.md for audit criteria."

## Post-Audit

After displaying the audit results:
- If there are High priority recommendations, ask if the user wants help implementing them
- For CLAUDE.md improvements, offer to load the `claude-config-guide` skill for guidance
