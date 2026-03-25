---
description: Review Claude Code prompts (Skill/Agent/Command/Plugin) for quality and best practices
argument-hint: "@path/to/file.md or @path/to/directory/ [--mode single|batch|consistency]"
allowed-tools: ["Read", "Glob", "Grep", "Task", "TodoWrite"]
---

# Prompt Review Command

Review Claude Code prompts for quality, structure, and best practices compliance.

## Input

**Target:** $ARGUMENTS

## Review Modes

- **single** (default): Detailed review of a single file
- **batch**: Summary + individual reports for multiple files
- **consistency**: Check naming, structure, and style consistency across files

## Process

### Step 1: Determine Target and Mode

Parse $ARGUMENTS to identify:
- Target type: single file, directory, or glob pattern
- Review mode: single, batch, or consistency (default: single for one file, batch for multiple)

### Step 2: Detect Category

For each file/directory, detect category from content:
- **Plugin**: Target is a directory containing `.claude-plugin/plugin.json`
- **Skill**: description starts with "This skill should be used when" or "Use when"; may have `version:`, `context:`, `effort:` in frontmatter
- **Agent**: Has `model:`, `color:`, or `tools:` in frontmatter, description starts with "Use this agent when", contains `<example>` blocks; may have `memory:`, `background:`, `isolation:` fields
- **Command**: Has `description:` and optionally `argument-hint:` or `allowed-tools:`; no `<example>` blocks

### Step 3: Apply Review Criteria

#### Skill Criteria
- [ ] Frontmatter has `name` (recommended, kebab-case, max 64 chars) and `description` (recommended)
- [ ] description starts with "This skill should be used when the user asks to..." or "Use when..."
- [ ] Specific trigger phrases in quotes (3-6 phrases)
- [ ] Body uses imperative form (not "You should...")
- [ ] Best Practices section uses ✅/❌ emoji format
- [ ] SKILL.md under 500 lines (detailed info in separate files)
- [ ] Additional Resources section with subsections if references exist
- [ ] `disable-model-invocation: true` set for side-effect workflows
- [ ] `context: fork` skills have explicit instructions (not just guidelines)

#### Agent Criteria
- [ ] Frontmatter has `name` (required) and `description` (required)
- [ ] description starts with "Use this agent when..."
- [ ] 2-4 `<example>` blocks present
- [ ] Each example has Context, user, assistant, commentary
- [ ] Body starts with expert persona
- [ ] Has Core Responsibilities, Process, Quality Standards, Output Format
- [ ] "What NOT to Focus On" section present (recommended)
- [ ] Edge Cases considered
- [ ] Model selection appropriate (default: inherit)
- [ ] `color` semantically matches purpose (recommended, universally used)
- [ ] Plugin agents do NOT use `hooks`, `mcpServers`, or `permissionMode`

#### Command Criteria
- [ ] description is clear, action-oriented
- [ ] argument-hint shows usage (if args expected)
- [ ] allowed-tools restricts to necessary tools only
- [ ] Workflow logically ordered
- [ ] $ARGUMENTS handling clear (with conditional for empty case)
- [ ] User confirmation at major decision boundaries
- [ ] Error handling for external dependencies
- [ ] Validation steps included

#### Plugin Criteria (directory review)

When target is a plugin directory, review the overall plugin structure:

**Structure (Critical)**
- [ ] `.claude-plugin/plugin.json` exists
- [ ] `plugin.json` has valid `name` (kebab-case, no spaces)
- [ ] Components (`commands/`, `agents/`, `skills/`) are at plugin root, not inside `.claude-plugin/`
- [ ] `.claude-plugin/` contains only `plugin.json`

**Configuration (Major)**
- [ ] `version` follows semantic versioning
- [ ] `description` clearly describes plugin purpose
- [ ] All paths in `plugin.json` are relative (`./` prefix)
- [ ] Scripts referenced by hooks use `${CLAUDE_PLUGIN_ROOT}`
- [ ] Hook scripts have executable permissions

**Security (Critical)**
- [ ] Plugin agents do NOT use `hooks`, `mcpServers`, or `permissionMode` fields
- [ ] No secrets or credentials in any file

**Quality (Minor)**
- [ ] LICENSE file exists
- [ ] CHANGELOG.md exists
- [ ] README.md exists with installation instructions
- [ ] `claude plugin validate` would pass (check structure)

**Components (Major)**
- [ ] Each skill/agent/command inside the plugin also passes its respective criteria
- [ ] Naming is consistent across components (plugin namespace)

### Step 4: Generate Report

#### Single Mode Output

```markdown
# Prompt Review Result

## Summary
- **File**: [path]
- **Category**: [Skill/Agent/Command/Plugin]
- **Rating**: [A/B/C/D]
- **Critical Issues**: [count]

## Strengths
1. [strength 1]
2. [strength 2]

## Issues

### Critical (Must Fix)
1. **[Issue Title]**
   - Current: [current state]
   - Problem: [why it's a problem]
   - Fix: [specific fix]

### Minor (Recommended)
1. **[Issue Title]**
   - Current: [current state]
   - Suggestion: [improvement]

## Corrected Sample
[Show corrected version of problematic sections]

## Checklist Results
| Item | Status | Comment |
|------|--------|---------|
| ... | OK/NG | ... |
```

#### Batch Mode Output

```markdown
# Batch Review Results

## Summary

| File | Category | Rating | Critical | Minor |
|------|----------|--------|----------|-------|
| ... | ... | ... | ... | ... |

## Statistics
- **Total Files**: [count]
- **Average Rating**: [A/B/C/D]
- **Total Critical Issues**: [count]

## Common Patterns
1. **[Pattern]**: [description] - Files: [list]

---

## Individual Reports
[Single mode format for each file]
```

#### Consistency Mode Output

```markdown
# Consistency Review Results

## Target Directory
`[path]`

## Detected Categories
- Plugins: [count] directories
- Skills: [count] files
- Agents: [count] files
- Commands: [count] files

## Consistency Check

### Naming Conventions
| Item | Status | Details |
|------|--------|---------|
| File naming | OK/NG | [details] |
| Frontmatter name | OK/NG | [details] |

### Structure Consistency
| Item | Status | Details |
|------|--------|---------|
| Section structure | OK/NG | [details] |
| Example format | OK/NG | [details] |

### Style Consistency
| Item | Status | Details |
|------|--------|---------|
| description format | OK/NG | [details] |
| Body person | OK/NG | [details] |

## Recommended Actions
1. [action 1]
2. [action 2]
```

## Rating Criteria

| Rating | Description |
|--------|-------------|
| A | Meets all criteria, follows best practices |
| B | Minor issues only, no functional impact |
| C | Some important issues, but basically functional |
| D | Critical issues, needs major revision |
