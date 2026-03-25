# Detailed Patterns Reference

## Skill Patterns

### YAML Frontmatter

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No (recommended) | Skill identifier (kebab-case, max 64 chars). Uses directory name if omitted. 100% of repo skills include it |
| `description` | Recommended | Trigger conditions (third-person). Claude uses this for auto-invocation. 100% of repo skills include it |
| `version` | No | Semantic version (33% of official skills use this) |
| `allowed-tools` | No | Tool array for skills needing specific capabilities (25% of skills) |
| `disable-model-invocation` | No | Prevent Claude from auto-loading (3% of skills). Default: `false` |
| `user-invocable` | No | Set to `false` to hide from `/` menu (17% of skills). Default: `true` |
| `model` | No | Model override when skill is active (11% of skills) |
| `effort` | No | Effort level: `low`, `medium`, `high`, `max` (Opus 4.6 only) |
| `context` | No | Set to `fork` to run in a forked subagent context |
| `agent` | No | Subagent type when `context: fork` is set |
| `hooks` | No | Hooks scoped to this skill's lifecycle |
| `argument-hint` | No | Autocomplete hint. Example: `[issue-number]` (6% of skills) |
| `license` | No | License reference (rare, 3%) |

### Description Writing

```yaml
# Pattern 1: Trigger-Based (72% - recommended)
description: This skill should be used when the user asks to "create a hook", "add a PreToolUse hook", or mentions hook events (PreToolUse, PostToolUse).

# Pattern 2: Use-Case (20%)
description: Best practices for building Stripe integrations. Use when implementing payment processing, checkout flows, or webhooks.

# Incorrect
description: Use this skill when you want to create hooks.
```

**Key conventions:**
- Third-person perspective (never "You should" or "I provide")
- Specific trigger phrases in quotes (3-6 phrases)
- 50-200 characters typical length

### Body Structure

```markdown
# [Skill Name]

## Overview
[1-2 paragraphs about purpose]

## Key Concepts
- **Concept 1**: Description
- **Concept 2**: Description

## [Main Content]
[Skill-specific details]

## Best Practices

✅ DO:
- Recommendation 1
- Recommendation 2

❌ DON'T:
- Avoid 1
- Avoid 2

## Quick Reference
[Table format reference]

## Additional Resources

### Reference Files
- **`references/file.md`** - Description

### Example Files
- **`examples/sample`** - Description

### Utility Scripts
- **`scripts/tool.sh`** - Description
```

### Progressive Disclosure

```
skill-name/
├── SKILL.md          # 1,500-2,000 words (max 5,000)
├── references/       # Detailed docs (loaded on demand)
├── examples/         # Working code examples
└── scripts/          # Utility scripts
```

### Writing Style

- Imperative/infinitive form: "Configure X", "Validate Y"
- NOT second person: Avoid "You should", "You can"
- NOT first person: Avoid "I provide"
- Objective instructional language

---

## Agent Patterns

### YAML Frontmatter

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Agent identifier (lowercase letters and hyphens) |
| `description` | Yes | Trigger conditions + 2-4 examples |
| `model` | No | `sonnet`, `opus`, `haiku`, full model ID, or `inherit` (default: `inherit`) |
| `tools` | No | Tool allowlist (omit for full access). 57% of repo agents restrict tools |
| `disallowedTools` | No | Tool denylist (removed from inherited/specified list) |
| `color` | No | Visual identifier. Undocumented in formal spec but used in 100% of repo agents. Values: `blue`, `cyan`, `green`, `yellow`, `magenta`, `red`, `pink` |
| `permissionMode` | No | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan`. NOT supported in plugin agents |
| `maxTurns` | No | Maximum agentic turns before stopping |
| `skills` | No | Skills to preload into agent context at startup |
| `mcpServers` | No | MCP servers available to this agent. NOT supported in plugin agents |
| `hooks` | No | Lifecycle hooks. NOT supported in plugin agents |
| `memory` | No | Persistent memory scope: `user`, `project`, `local` |
| `background` | No | Set to `true` to always run as background task. Default: `false` |
| `effort` | No | Effort level: `low`, `medium`, `high`, `max` |
| `isolation` | No | Set to `worktree` for isolated git worktree execution |

### Model Selection

| Model | Use Case | Frequency |
|-------|----------|-----------|
| `inherit` | Default, context-dependent | 57% |
| `sonnet` | Architecture, code analysis, deep understanding | 37% |
| `opus` | Complex code quality, precise transformation | 7% |
| `haiku` | Exploration, simple checks | Rare (used via built-in Explore agent) |

### Tool Patterns

```yaml
# Analysis (read-only)
tools: ["Read", "Grep", "Glob"]

# Extended analysis (with notebook/web support)
tools: ["Glob", "Grep", "Read", "NotebookRead", "WebFetch", "TodoWrite", "WebSearch"]

# Creation (write-enabled)
tools: ["Write", "Read", "Edit"]

# Validation
tools: ["Read", "Grep", "Glob", "Bash"]

# Conversation analysis (minimal)
tools: ["Read", "Grep"]

# Full access (complex tasks)
# Omit tools field
```

### Color Semantics

| Color | Use Case |
|-------|----------|
| `blue`/`cyan` | Analysis, review, validation |
| `green` | Generation, creation, simplification |
| `yellow` | Caution, validation, warning |
| `red` | Critical, security-focused |
| `magenta` | Transformation, creative work |
| `pink` | Special analysis |

### Example Block Structure

```yaml
description: |
  Use this agent when [conditions].

  <example>
  Context: [Situation description]
  user: "[User message]"
  assistant: "[Response before triggering]"
  <commentary>
  [Why this agent should trigger]
  </commentary>
  assistant: "I'll use the agent-name agent to [action]."
  </example>
```

### Body Structure

```markdown
You are an expert [expertise] specializing in [specialization].

## Core Responsibilities

1. [Responsibility 1]
2. [Responsibility 2]
3. [Responsibility 3]

## Process

### Step 1: [Name]
[Details]

### Step 2: [Name]
[Details]

## Quality Standards

- [Standard 1]
- [Standard 2]

## Output Format

[Exact template for results]

## What NOT to Focus On

- [Exclusion 1 to prevent scope creep]
- [Exclusion 2 to prevent false positives]

## Edge Cases

- **[Case 1]**: [Handling]
- **[Case 2]**: [Handling]
```

### Confidence/Rating Patterns

Agents performing review can use quantitative scoring:

```markdown
## Confidence Scoring
- Scale: 0-100
- Threshold: Only report issues ≥ 80
- Categories: 0-25 (low), 26-50 (medium), 51-75 (high), 76-100 (very high)
```

```markdown
## Rating System
- Scale: 1-10 per dimension
- Dimensions: [Dimension 1], [Dimension 2], [Dimension 3]
```

---

## Command Patterns

### YAML Frontmatter

| Field | Required | Description |
|-------|----------|-------------|
| `description` | Recommended | Help text (short, action-oriented). 100% of repo commands include it |
| `argument-hint` | No | Argument format hint (40% of commands) |
| `allowed-tools` | No | Tool allowlist (69% of commands) |
| `model` | No | Force specific model (rare, 6%) |
| `disable-model-invocation` | No | Prevent Claude from auto-loading (rare, 3%) |
| `hide-from-slash-command-tool` | No | Legacy field, replaced by `user-invocable: false` (11% of commands) |

### allowed-tools Patterns

```yaml
# Specific bash commands only
allowed-tools: Bash(git add:*), Bash(git commit:*)

# Tool array
allowed-tools: ["Read", "Write", "TodoWrite"]

# Plugin-relative path
allowed-tools: ["Bash(${CLAUDE_PLUGIN_ROOT}/scripts/deploy.sh)"]

# Interactive tools
allowed-tools: ["Glob", "Read", "Edit", "AskUserQuestion", "Skill"]

# Dynamic context injection (! prefix)
# !`git status` injects command output into prompt at load time
```

### $ARGUMENTS Handling

```markdown
# Pattern 1: Direct substitution
**Initial request:** $ARGUMENTS

# Pattern 2: Conditional check
**If $ARGUMENTS is provided:**
- User has given specific instructions: `$ARGUMENTS`
- Proceed with: [action]

**If $ARGUMENTS is empty:**
- [default action or ask user]

# Pattern 3: Argument parsing with options
1. Parse $ARGUMENTS for:
   - Target: file path or directory
   - Flags: --mode, --flag
   - If not provided, use defaults
```

### Dynamic Context

```markdown
## Context

- Current git status: !`git status`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -5`
```

### Skill Loading Pattern

Commands can reference skills for detailed guidance:

```markdown
**FIRST: Load the [plugin]:[skill] skill** for format guidance.
```

### Complexity Patterns

| Complexity | Steps | Pattern |
|------------|-------|---------|
| Simple | 1-3 | Pattern C (simple instructions) |
| Medium | 4-6 | Pattern B (procedural list) |
| Complex | 7+ | Pattern A (phase structure) |

### Phase Structure (Complex)

```markdown
## Phase 1: [Name]
**Goal**: [Objective]
**Actions**:
1. [Action 1]
2. [Action 2]
**Output**: [Expected result]

**Wait for user confirmation before proceeding.**

## Phase 2: [Name]
...
```

### User Interaction Patterns

```markdown
# User confirmation (complex workflows)
**Wait for user confirmation before proceeding.**
**DO NOT START WITHOUT USER APPROVAL**

# Interactive questions (AskUserQuestion tool)
Use AskUserQuestion with structured JSON for multi-choice questions.
```

---

## Plugin Patterns

### Directory Structure

```
my-plugin/
├── .claude-plugin/           # メタデータディレクトリ
│   └── plugin.json           # プラグインマニフェスト（必須）
├── commands/                 # コマンド（デフォルト場所、legacy — 新規は skills/ 推奨）
├── agents/                   # エージェント（デフォルト場所）
├── skills/                   # スキル（デフォルト場所）
├── hooks/                    # フック設定
│   └── hooks.json
├── settings.json             # プラグイン有効時のデフォルト設定（現在 `agent` キーのみ対応）
├── .mcp.json                 # MCP サーバー定義
├── .lsp.json                 # LSP サーバー設定（コードインテリジェンス）
├── scripts/                  # フック・ユーティリティスクリプト
├── LICENSE
├── README.md
└── CHANGELOG.md
```

**Important**: `.claude-plugin/` には `plugin.json` のみ配置。`commands/`, `agents/`, `skills/`, `hooks/` は全てプラグインルートに置く。

### plugin.json Schema

#### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | 一意識別子（kebab-case、スペースなし）。スキル名前空間として使用 |

#### Metadata Fields

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `version` | string | セマンティックバージョン | `"1.0.0"` |
| `description` | string | プラグインの目的 | `"Deployment automation tools"` |
| `author` | object | 作者情報（`name`, `email`, `url`） | `{"name": "Dev", "email": "dev@co.com"}` |
| `homepage` | string | ドキュメント URL | `"https://docs.example.com"` |
| `repository` | string | ソースコード URL | `"https://github.com/user/plugin"` |
| `license` | string | ライセンス識別子 | `"MIT"` |
| `keywords` | array | 発見用タグ | `["deployment", "ci-cd"]` |

#### Component Path Fields

| Field | Type | Description |
|-------|------|-------------|
| `commands` | string\|array | 追加コマンドファイル/ディレクトリ |
| `agents` | string\|array | 追加エージェントファイル |
| `skills` | string\|array | 追加スキルディレクトリ |
| `hooks` | string\|array\|object | フック設定パスまたはインライン設定 |
| `mcpServers` | string\|array\|object | MCP 設定パスまたはインライン設定 |
| `outputStyles` | string\|array | 追加出力スタイルファイル/ディレクトリ |
| `lspServers` | string\|array\|object | LSP サーバー設定（コードインテリジェンス: 定義へ移動、参照検索等） |

カスタムパスはデフォルトディレクトリに**加えて追加**される（置き換えではない）。全パスは `./` で始まりプラグインルートからの相対パス。

### Environment Variables

| Variable | Description |
|----------|-------------|
| `${CLAUDE_PLUGIN_ROOT}` | プラグインのインストールディレクトリへの絶対パス。スクリプト参照に使用 |
| `${CLAUDE_PLUGIN_DATA}` | 更新をまたいで永続するデータ用ディレクトリ（`~/.claude/plugins/data/{id}/`） |

### Hooks Configuration

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format-code.sh"
          }
        ]
      }
    ]
  }
}
```

**Hook Events:**
- `SessionStart`, `SessionEnd`
- `UserPromptSubmit`
- `PreToolUse`, `PostToolUse`, `PostToolUseFailure`
- `PermissionRequest`
- `SubagentStart`, `SubagentStop`
- `Stop`, `StopFailure`
- `Notification`
- `TeammateIdle`
- `TaskCompleted`
- `InstructionsLoaded`, `ConfigChange`
- `WorktreeCreate`, `WorktreeRemove`
- `PreCompact`, `PostCompact`
- `Elicitation`, `ElicitationResult`

**Hook Types:**
- `command` — シェルコマンド/スクリプトを実行
- `http` — イベント JSON を URL に POST
- `prompt` — LLM でプロンプトを評価（`$ARGUMENTS` 使用）
- `agent` — ツールを持つエージェント型バリファイアを実行（複雑な検証タスク向け）

### Plugin Agent Security Restrictions

プラグイン提供のエージェントは以下のフィールドをサポート**しない**:
- `hooks`
- `mcpServers`
- `permissionMode`

### Version Management

- セマンティックバージョニング（`MAJOR.MINOR.PATCH`）に従う
- バージョンを上げないとキャッシュにより既存ユーザーに変更が届かない
- `1.0.0` から開始
- 変更は `CHANGELOG.md` に記録
- プレリリース版: `2.0.0-beta.1`

### Install Scopes

| Scope | Settings File | Use Case |
|-------|--------------|----------|
| `user` | `~/.claude/settings.json` | 個人プラグイン（デフォルト） |
| `project` | `.claude/settings.json` | チーム共有プラグイン |
| `local` | `.claude/settings.local.json` | gitignore されるプロジェクト固有 |

### Testing & Debugging

```bash
# ローカルテスト
claude --plugin-dir ./my-plugin

# 複数プラグイン
claude --plugin-dir ./plugin-one --plugin-dir ./plugin-two

# ホットリロード（再起動不要）
/reload-plugins

# バリデーション
claude plugin validate
```

### Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| プラグインが読み込まれない | 無効な `plugin.json` | `claude plugin validate` を実行 |
| コマンドが表示されない | ディレクトリ構造の誤り | `commands/` がルートにあるか確認 |
| フックが発火しない | スクリプトに実行権限なし | `chmod +x script.sh` |
| MCP サーバーが失敗 | パス解決エラー | `${CLAUDE_PLUGIN_ROOT}` を使用 |
| パスエラー | 絶対パスの使用 | `./` からの相対パスに変更 |

---

## Checklists

### Skill Checklist

- [ ] Frontmatter has `name` (recommended) and `description` (recommended)
- [ ] `name` is kebab-case, max 64 chars (uses directory name if omitted)
- [ ] description starts with "This skill should be used when the user asks to..." or "Use when..."
- [ ] Specific trigger phrases listed in quotes (3-6 phrases)
- [ ] Body uses imperative form (not "You should...")
- [ ] Best Practices use ✅/❌ emoji format
- [ ] SKILL.md under 500 lines (detailed info in separate files)
- [ ] Detailed info separated to references/
- [ ] Additional Resources section with subsections (Reference Files, Examples, Scripts)
- [ ] `disable-model-invocation: true` set for side-effect workflows (`/commit`, `/deploy`)
- [ ] `context: fork` skills have explicit instructions (not just guidelines)

### Agent Checklist

- [ ] `name` is lowercase letters and hyphens
- [ ] `description` starts with "Use this agent when..."
- [ ] `description` has 2-4 `<example>` blocks
- [ ] Each example has Context, user, assistant, commentary
- [ ] Body starts with expert persona
- [ ] Core Responsibilities, Process, Quality Standards, Output Format defined
- [ ] "What NOT to Focus On" section for scope control (recommended)
- [ ] Edge Cases considered
- [ ] Model selection appropriate (default: inherit)
- [ ] `color` semantically matches purpose (recommended, undocumented in formal spec but universally used)
- [ ] Plugin agents do NOT use `hooks`, `mcpServers`, or `permissionMode` (security restriction)

### Command Checklist

- [ ] description is clear, action-oriented
- [ ] argument-hint shows usage format (if args expected)
- [ ] allowed-tools restricts to necessary tools only
- [ ] Workflow logically ordered
- [ ] $ARGUMENTS handling clear (with conditional for empty case)
- [ ] User confirmation at major decision boundaries
- [ ] Error handling for external dependencies
- [ ] Validation steps included
- [ ] Dynamic context (`!` backtick) used for runtime state where needed
- [ ] Consider using `disable-model-invocation: true` for side-effect workflows

### Plugin Checklist

- [ ] `.claude-plugin/plugin.json` が存在する
- [ ] `name` フィールドが kebab-case で設定されている
- [ ] `version` がセマンティックバージョニングに従っている
- [ ] `description` がプラグインの目的を簡潔に述べている
- [ ] コンポーネント（`commands/`, `agents/`, `skills/`）がプラグインルートに配置されている
- [ ] `.claude-plugin/` に `plugin.json` 以外のファイルがない
- [ ] スクリプト参照に `${CLAUDE_PLUGIN_ROOT}` を使用している
- [ ] 全パスが `./` からの相対パスである
- [ ] プラグイン内エージェントが `hooks`, `mcpServers`, `permissionMode` を使用していない
- [ ] フック用スクリプトに実行権限がある
- [ ] LICENSE ファイルが存在する
- [ ] CHANGELOG.md が存在する（推奨）
- [ ] `claude plugin validate` でエラーがない
