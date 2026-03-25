---
description: Generate a Claude Code Command with proper workflow structure
argument-hint: "command description or requirements"
allowed-tools: ["Read", "Write", "Glob", "Grep", "Skill", "AskUserQuestion"]
---

# Command Generation Command

Generate a properly structured Claude Code Command based on user requirements.

## Input

**Requirements:** $ARGUMENTS

## Process

### Step 1: Gather Requirements

**If $ARGUMENTS is provided:**
- Parse the description to understand command purpose
- Identify workflow complexity and steps

**If $ARGUMENTS is empty or unclear, ask:**
1. What task should this command perform?
2. Does it need arguments?
3. What tools does it need access to?
4. Is it a simple, medium, or complex workflow?

### Step 2: Load Reference

Load the prompt-creation skill for guidance:
- Use Skill tool to load `prompt-smith:prompt-creation`

### Step 3: Determine Complexity

| Complexity | Steps | Pattern |
|------------|-------|---------|
| Simple | 1-3 | Pattern C - Direct instructions |
| Medium | 4-6 | Pattern B - Procedural list |
| Complex | 7+ | Pattern A - Phase structure |

### Step 4: Design Command Structure

Determine:
- **name**: command identifier (becomes `/name`)
- **description**: Short help text
- **argument-hint**: Format hint for arguments
- **allowed-tools**: Necessary tools only
- **Workflow steps**: Based on complexity

### Step 5: Generate File

#### Pattern A: Complex (Phase Structure)

```yaml
---
description: {Short description for help}
argument-hint: "{argument format}"
allowed-tools: ["Read", "Write", "Bash(specific:*)", "TodoWrite"]
---

# {Command Name}

{Brief description of what this command does}

## Input

**Request:** $ARGUMENTS

## Phase 1: {Discovery/Setup}

**Goal**: {What this phase accomplishes}

**Actions**:
1. {Action 1}
2. {Action 2}

**Output**: {Expected result}

**Wait for user confirmation before proceeding.**

## Phase 2: {Execution/Implementation}

**Goal**: {What this phase accomplishes}

**Actions**:
1. {Action 1}
2. {Action 2}
3. {Action 3}

**Output**: {Expected result}

## Phase 3: {Validation/Completion}

**Goal**: Verify everything works correctly

**Actions**:
1. Run validation: `{validation command}`
2. Fix any issues found
3. Re-validate until passing

**DO NOT proceed until validation passes.**

## Error Handling

**If {error scenario 1}:**
1. {Recovery action}

**If {error scenario 2}:**
1. {Recovery action}
```

#### Pattern B: Medium (Procedural List)

```yaml
---
description: {Short description}
argument-hint: "{argument format}"
allowed-tools: ["Read", "Write", "Bash(specific:*)"]
---

# {Command Name}

{Brief description}

## Input

**Request:** $ARGUMENTS

## Process

Follow these steps precisely:

1. **{Step 1 Name}**
   {Detailed instructions}

2. **{Step 2 Name}**
   {Detailed instructions}

3. **{Step 3 Name}**
   {Detailed instructions}

4. **{Step 4 Name}**
   {Detailed instructions}

## Validation

Before completing:
- [ ] {Check 1}
- [ ] {Check 2}
- [ ] {Check 3}
```

#### Pattern C: Simple (Direct Instructions)

```yaml
---
description: {Short description}
allowed-tools: Bash(specific command:*)
---

## Context

- Current status: !`{context command}`

## Task

{Direct task description}

Do not use any other tools.
```

### Step 6: Validate

Check the generated command against criteria:
- [ ] description is clear and concise
- [ ] argument-hint shows expected format (if args needed)
- [ ] allowed-tools permits only necessary tools
- [ ] Workflow follows appropriate complexity pattern
- [ ] $ARGUMENTS handling is clear
- [ ] Validation steps included
- [ ] Error handling considered (for complex commands)

### Step 7: Output

Present the generated command and ask if user wants to:
1. Write to file
2. Adjust complexity level
3. Add/remove workflow phases
4. Modify tool permissions

## Common Tool Patterns

```yaml
# Git operations
allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git push:*)

# File operations
allowed-tools: ["Read", "Write", "Edit", "Glob"]

# Build/Test
allowed-tools: ["Bash(npm run:*)", "Bash(npx:*)", "Read"]

# Full workflow
allowed-tools: ["Read", "Write", "Bash", "TodoWrite", "Task"]
```

## Dynamic Context Patterns

```markdown
## Context

- Current git status: !`git status`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -5`
- Package info: !`cat package.json | head -20`
```

## Example

**Input:** "deploy to staging environment"

**Generated (Pattern A - Complex):**

```yaml
---
description: Deploy to staging environment with validation
argument-hint: "[environment] (default: staging)"
allowed-tools: ["Read", "Bash(npm:*)", "Bash(git:*)", "TodoWrite"]
---

# Deploy to Staging

Deploy the application to staging environment with pre-flight checks.

## Input

**Environment:** $ARGUMENTS (default: staging)

## Phase 1: Pre-flight Checks

**Goal**: Ensure deployment is safe to proceed

**Actions**:
1. Check current branch: !`git branch --show-current`
2. Check for uncommitted changes: !`git status --porcelain`
3. Verify tests pass: `npm run test`

**If not on main branch, warn user and ask to confirm.**

...
```
