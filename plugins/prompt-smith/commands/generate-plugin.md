---
description: Generate a Claude Code Plugin with proper directory structure and configuration
argument-hint: "plugin description or requirements"
allowed-tools: ["Read", "Write", "Glob", "Grep", "Bash(mkdir:*)", "Bash(chmod:*)", "Skill", "AskUserQuestion"]
---

# Plugin Generation Command

Generate a properly structured Claude Code Plugin based on user requirements.

## Input

**Requirements:** $ARGUMENTS

## Process

### Step 1: Gather Requirements

**If $ARGUMENTS is provided:**
- Parse the description to understand plugin purpose
- Identify what components (skills, agents, commands, hooks) are needed

**If $ARGUMENTS is empty or unclear, ask:**
1. What is the plugin's purpose?
2. What components does it need? (skills, agents, commands, hooks)
3. Who is the target audience? (personal, team, community)
4. Does it need MCP or LSP server integration?

### Step 2: Load Reference

Load the prompt-creation skill for guidance:
- Use Skill tool to load `prompt-smith:prompt-creation`

### Step 3: Design Plugin Structure

Determine:
- **name**: kebab-case identifier (used as skill namespace `plugin-name:skill-name`)
- **Components**: Which of skills/, agents/, commands/, hooks/ are needed
- **Hooks**: What lifecycle events to hook into (if any)
- **MCP/LSP**: Whether external server integrations are needed
- **Scripts**: Whether utility scripts are needed

### Step 4: Generate Files

#### 4a: Create Directory Structure

```bash
mkdir -p {plugin-name}/.claude-plugin
mkdir -p {plugin-name}/commands
mkdir -p {plugin-name}/agents     # if needed
mkdir -p {plugin-name}/skills     # if needed
mkdir -p {plugin-name}/hooks      # if needed
mkdir -p {plugin-name}/scripts    # if needed
```

Only create directories for components the plugin actually needs.

#### 4b: Generate plugin.json

```json
{
  "name": "{plugin-name}",
  "version": "0.1.0",
  "description": "{Brief description of plugin purpose}",
  "author": {
    "name": "{author name}",
    "email": "{author email}"
  },
  "license": "MIT"
}
```

If custom component paths are needed, add them:
```json
{
  "commands": "./custom/commands/",
  "agents": "./custom/agents/",
  "hooks": "./hooks/hooks.json"
}
```

#### 4c: Generate README.md

```markdown
# {plugin-name}

{Description of what this plugin does.}

## Components

### Commands
- `/{plugin-name}:{command}` - {description}

### Skills
- `{plugin-name}:{skill}` - {description}

### Agents
- `{plugin-name}:{agent}` - {description}

## Installation

```bash
claude plugin install {plugin-name}@{source}
```

## Local Development

```bash
claude --plugin-dir ./{plugin-name}
```
```

#### 4d: Generate CHANGELOG.md

```markdown
# Changelog

## [0.1.0] - {today's date}
- 初期リリース
```

#### 4e: Generate LICENSE (MIT)

Standard MIT license with current year and author name.

#### 4f: Generate hooks.json (if hooks needed)

```json
{
  "hooks": {
    "{EventName}": [
      {
        "matcher": "{ToolPattern}",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/{script-name}.sh"
          }
        ]
      }
    ]
  }
}
```

If hook scripts are generated, set executable permissions:
```bash
chmod +x {plugin-name}/scripts/*.sh
```

#### 4g: Generate Stub Components

For each component the user requested, generate a minimal stub:

**Skill stub** (`skills/{skill-name}/SKILL.md`):
```yaml
---
name: {skill-name}
description: This skill should be used when the user asks to "{phrase 1}", "{phrase 2}" or needs guidance on {topic}.
---
```

**Agent stub** (`agents/{agent-name}.md`):
```yaml
---
name: {agent-name}
description: |
  Use this agent when {condition}.
model: inherit
tools: ["Read", "Grep", "Glob"]
---
```

**Command stub** (`commands/{command-name}.md`):
```yaml
---
description: {Short description}
argument-hint: "{args}"
---
```

### Step 5: Validate

Check the generated plugin against criteria:
- [ ] `.claude-plugin/plugin.json` exists with valid `name`
- [ ] `name` is kebab-case, no spaces
- [ ] `version` follows semver
- [ ] Components are at plugin root (not inside `.claude-plugin/`)
- [ ] All paths in plugin.json are relative (`./`)
- [ ] Scripts have executable permissions (if any)
- [ ] No agent uses `hooks`, `mcpServers`, or `permissionMode`
- [ ] `${CLAUDE_PLUGIN_ROOT}` used for script references

### Step 6: Output

Present the generated plugin structure and files, then ask if user wants to:
1. Write all files to disk
2. Add more components (skills, agents, commands)
3. Configure hooks
4. Adjust plugin metadata
5. Test with `claude --plugin-dir ./{plugin-name}`
