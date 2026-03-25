---
name: prompt-creation
description: This skill should be used when the user asks to "create a skill", "create an agent", "create a command", "create a plugin", "scaffold a plugin", "write a prompt for Claude Code", "make a plugin component", or needs guidance on Claude Code prompt patterns and best practices.
version: 1.2.0
---

# Claude Code Prompt Creation Guide

## Overview

This skill provides guidance for creating high-quality Claude Code components: Skills, Agents, Commands, and Plugins. Based on analysis of official repositories and official Claude Code documentation.

## Component Types

| Component | Purpose | Trigger |
|-----------|---------|---------|
| **Skill** | Domain knowledge & guidance | Auto (phrase match in description) |
| **Agent** | Autonomous task execution | Task tool invocation |
| **Command** | User-initiated workflow | `/command` explicit execution |
| **Plugin** | Skills/Agents/Commands のパッケージング・配布 | `claude plugin install` |

## Quick Reference

### Skill Structure

```yaml
---
name: skill-name
description: This skill should be used when the user asks to "phrase 1", "phrase 2"...
version: 0.1.0  # optional
---
```

**Key Rules:**
- `name`: optional (uses directory name if omitted), kebab-case, max 64 chars
- `description`: recommended (not required). Starts with "This skill should be used when the user asks to..." or "Use when..."
- List 3-6 specific trigger phrases in quotes
- Body uses imperative form ("Configure the server.", not "You should configure...")
- Best Practices section uses ✅/❌ emoji format
- Keep SKILL.md under 500 lines; details go to separate files (references/, examples/)
- Use `disable-model-invocation: true` for side-effect workflows (`/commit`, `/deploy`)

### Agent Structure

```yaml
---
name: agent-name
description: |
  Use this agent when [conditions].

  <example>
  Context: [situation]
  user: "[message]"
  assistant: "[response]"
  <commentary>[reasoning]</commentary>
  </example>
model: inherit  # inherit (57%), sonnet (37%), opus (7%)
color: blue     # semantic color
tools: ["Read", "Grep", "Glob"]  # omit for full access
---
```

**Key Rules:**
- description starts with "Use this agent when..."
- Include 2-4 `<example>` blocks with Context, user, assistant, commentary
- Body starts with expert persona
- Include: Core Responsibilities, Process, Quality Standards, Output Format
- Include "What NOT to Focus On" section for scope control
- Include Edge Cases section
- Default model is `inherit`; use `sonnet` for architecture/analysis (37%), `opus` for code quality (7%)
- `color` is universally used in repo agents (undocumented in formal spec, but recommended)

### Command Structure

```yaml
---
description: Short action-oriented description
argument-hint: "[args]"
allowed-tools: ["Read", "Write", "Bash(git:*)"]
---
```

**Key Rules:**
- Use `$ARGUMENTS` placeholder for user input (with conditional for empty case)
- Dynamic context with `!`backtick`` (e.g., `!`git status``)
- Define clear phases for complex workflows (7+ steps)
- Include validation steps and error handling
- Reference skills with `Skill` tool for detailed guidance
- User confirmation at major decision boundaries

### Plugin Structure

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json           # マニフェスト（name 必須）
├── skills/                   # スキル
├── agents/                   # エージェント
├── commands/                 # コマンド
├── hooks/
│   └── hooks.json            # フック設定
├── scripts/                  # ユーティリティスクリプト
├── LICENSE
└── CHANGELOG.md
```

**plugin.json 最小構成:**
```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief description"
}
```

**Key Rules:**
- `name`: kebab-case、一意識別子。スキル名前空間（`plugin-name:skill-name`）として使用
- `.claude-plugin/` には `plugin.json` のみ配置。コンポーネントはプラグインルートに置く
- 全パスは `./` で始まるプラグインルートからの相対パス
- セマンティックバージョニング（`MAJOR.MINOR.PATCH`）に従う
- プラグイン内のエージェントは `hooks`, `mcpServers`, `permissionMode` を使用不可（セキュリティ制限）
- スクリプト参照には `${CLAUDE_PLUGIN_ROOT}` 環境変数を使用
- `claude plugin validate` でバリデーション可能
- `claude --plugin-dir ./my-plugin` でローカルテスト可能

## Best Practices

✅ DO:
- Use specific, concrete examples
- Include Good/Bad comparisons with explanations
- Define clear completion criteria
- Consider edge cases
- Use quantitative scoring for review agents (confidence 0-100 or rating 1-10)

❌ DON'T:
- Use vague descriptions
- Skip examples
- Forget validation steps
- Mix writing styles (imperative vs. second person)
- Give agents full tool access when restricted access suffices

## Additional Resources

For detailed patterns, templates, and checklists:
- **`references/patterns.md`** - Category-specific patterns, tool sets, and validation checklists
