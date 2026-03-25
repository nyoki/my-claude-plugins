---
description: Generate a Claude Code Skill with proper structure and best practices
argument-hint: "skill description or requirements"
allowed-tools: ["Read", "Write", "Glob", "Grep", "Skill", "AskUserQuestion"]
---

# Skill Generation Command

Generate a properly structured Claude Code Skill based on user requirements.

## Input

**Requirements:** $ARGUMENTS

## Process

### Step 1: Gather Requirements

**If $ARGUMENTS is provided:**
- Parse the description to understand skill purpose
- Identify target users and trigger scenarios

**If $ARGUMENTS is empty or unclear, ask:**
1. What is the skill's purpose?
2. What trigger phrases should activate it?
3. What domain knowledge should it provide?

### Step 2: Load Reference

Load the prompt-creation skill for guidance:
- Use Skill tool to load `prompt-smith:prompt-creation`

### Step 3: Design Skill Structure

Determine:
- **name**: lowercase, hyphen-separated identifier
- **Trigger phrases**: 3-5 specific phrases users would say
- **Key concepts**: Core knowledge to provide
- **Main sections**: Topic-specific content areas
- **Best practices**: DO/DON'T guidance

### Step 4: Generate Files

Create the following structure:

```
{skill-name}/
├── SKILL.md
└── references/
    └── detailed-patterns.md (if needed)
```

#### SKILL.md Template

```yaml
---
name: {skill-name}
description: This skill should be used when the user asks to "{phrase1}", "{phrase2}", "{phrase3}" or needs guidance on {topic}.
version: 0.1.0
---

# {Skill Title}

## Overview

{1-2 paragraphs explaining the skill's purpose and what guidance it provides}

## Key Concepts

- **{Concept 1}**: {Description}
- **{Concept 2}**: {Description}

## {Main Content Section 1}

{Detailed guidance with examples}

## {Main Content Section 2}

{More detailed guidance}

## Best Practices

**DO:**
- {Recommendation 1}
- {Recommendation 2}

**DON'T:**
- {Avoid 1}
- {Avoid 2}

## Quick Reference

| {Column 1} | {Column 2} | {Column 3} |
|------------|------------|------------|
| ... | ... | ... |

## Troubleshooting

**{Problem 1}**: {Solution}

**{Problem 2}**: {Solution}

## Additional Resources

### Reference Files
- **`references/detailed-patterns.md`** - {Description}
```

### Step 5: Validate

Check the generated skill against criteria:
- [ ] description starts with "This skill should be used when the user asks to..."
- [ ] Trigger phrases are specific and in quotes
- [ ] Body uses imperative form
- [ ] Under 5,000 words
- [ ] Includes concrete examples
- [ ] Has Best Practices section

### Step 6: Output

Present the generated skill and ask if user wants to:
1. Write to file
2. Make modifications
3. Generate additional reference files
4. Test and optimize with skill-creator (if installed)

**If user selects option 4:**
- Inform the user that [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) is Anthropic's official skill for test-driven skill development
- skill-creator provides A/B blind comparison, quantitative benchmarking, and description optimization
- Suggest the user invoke skill-creator to evaluate and iterate on the generated skill

## Example

**Input:** "API testing best practices for REST endpoints"

**Generated:**

```yaml
---
name: api-testing
description: This skill should be used when the user asks to "write API tests", "test REST endpoints", "create integration tests" or needs guidance on API testing patterns.
version: 0.1.0
---

# API Testing Guide

## Overview

Provides guidance for writing effective API tests for REST endpoints, including unit tests, integration tests, and end-to-end tests.

## Key Concepts

- **Unit Tests**: Test individual functions in isolation
- **Integration Tests**: Test API endpoints with real dependencies
- **E2E Tests**: Test complete user workflows

...
```
