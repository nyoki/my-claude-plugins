---
description: Generate a Claude Code Agent with proper structure and triggering examples
argument-hint: "agent description or requirements"
allowed-tools: ["Read", "Write", "Glob", "Grep", "Skill", "AskUserQuestion"]
---

# Agent Generation Command

Generate a properly structured Claude Code Agent based on user requirements.

## Input

**Requirements:** $ARGUMENTS

## Process

### Step 1: Gather Requirements

**If $ARGUMENTS is provided:**
- Parse the description to understand agent purpose
- Identify expertise domain and task type

**If $ARGUMENTS is empty or unclear, ask:**
1. What is the agent's primary task?
2. What expertise should it have?
3. Is it for analysis, creation, or review?

### Step 2: Load Reference

Load the prompt-creation skill for guidance:
- Use Skill tool to load `prompt-smith:prompt-creation`

### Step 3: Design Agent Structure

Determine:
- **name**: lowercase, hyphen-separated, 3-50 chars
- **Expertise**: Domain of specialization
- **Task type**: Analysis / Creation / Review
- **Model**: Based on task complexity
  - `sonnet`: Creation, design, implementation
  - `opus`: Precise review, bug detection
  - `haiku`: Exploration, simple checks
- **Tools**: Based on needs
  - Analysis: `["Read", "Grep", "Glob"]`
  - Creation: `["Write", "Read", "Edit"]`
  - Full access: Omit tools field
- **Trigger scenarios**: 2-4 specific situations

### Step 4: Generate File

#### Agent Template

```yaml
---
name: {agent-name}
description: |
  Use this agent when {triggering condition}.
  {Additional context about when to use}.

  Examples:

  <example>
  Context: {Situation 1}
  user: "{User message 1}"
  assistant: "{Response before triggering}"
  <commentary>
  {Why this agent should be triggered}
  </commentary>
  assistant: "I'll use the {agent-name} agent to {action}."
  </example>

  <example>
  Context: {Situation 2}
  user: "{User message 2}"
  assistant: "{Response before triggering}"
  <commentary>
  {Why this agent should be triggered}
  </commentary>
  assistant: "I'll use the {agent-name} agent to {action}."
  </example>
model: {sonnet/opus/haiku/inherit}
color: {blue/cyan/yellow/green/red/magenta}
tools: {["Tool1", "Tool2"] or omit for full access}
---

You are an expert {expertise} specializing in {specialization}.

## Core Responsibilities

1. {Primary responsibility}
2. {Secondary responsibility}
3. {Tertiary responsibility}

## Process

### Step 1: {Initial Step}
{Detailed instructions for first step}

### Step 2: {Analysis/Execution Step}
{Detailed instructions}

### Step 3: {Output Step}
{Detailed instructions for producing output}

## Quality Standards

- {Standard 1 with specific criteria}
- {Standard 2 with measurable threshold}
- {Standard 3}

## Output Format

```markdown
## {Report Title}

### Summary
[Key findings with metrics if applicable]

### {Section 1}
[Details]

### {Section 2}
[Details]

### Recommendations
1. **[Priority 1]**: {Description and rationale}
2. **[Priority 2]**: {Description and rationale}
3. **[Priority 3]**: {Description and rationale}
```

## Edge Cases

- **{Edge case 1}**: {How to handle}
- **{Edge case 2}**: {How to handle}
- **{Edge case 3}**: {How to handle}
```

### Step 5: Validate

Check the generated agent against criteria:
- [ ] name is lowercase, hyphens, 3-50 characters
- [ ] description has 2-4 `<example>` blocks
- [ ] Each example has Context, user, assistant, commentary
- [ ] Body starts with expert persona
- [ ] Has Core Responsibilities, Process, Quality Standards, Output Format
- [ ] Edge Cases section present
- [ ] Model selection matches task type

### Step 6: Output

Present the generated agent and ask if user wants to:
1. Write to file
2. Adjust model/tools selection
3. Add more example scenarios
4. Refine the process steps

## Model Selection Guide

| Task Type | Model | Examples |
|-----------|-------|----------|
| Creation/Design | sonnet | code-architect, agent-creator |
| Precise Review | opus | code-reviewer, security-scanner |
| Exploration | haiku | file-finder, quick-checker |
| Context-dependent | inherit | general-purpose agents |

## Color Guide

| Color | Use Case |
|-------|----------|
| blue/cyan | Analysis, review, validation |
| green | Creation, generation, building |
| yellow | Exploration, warnings |
| red | Security, critical issues |
| magenta | Transformation, creative tasks |
