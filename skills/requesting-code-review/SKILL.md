---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Request code review by dispatching a focused review subagent with `Agent`.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After each task in subagent-driven development
- After completing a major feature
- Before merge to main

**Optional but valuable:**
- When stuck and you want a fresh perspective
- Before refactoring
- After fixing a complex bug

## How to Request

**1. Get git SHAs**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Choose the reviewer type**
- If the repo defines a custom reviewer agent type in `.pi/agents/`, use it.
- Otherwise use `general-purpose` with a review-specific prompt.

**3. Dispatch the review subagent**
You MUST use the installed `Agent` tool shape.

Example:

```json
{
  "subagent_type": "general-purpose",
  "description": "Review retry banner",
  "prompt": "Review the diff from BASE_SHA to HEAD_SHA. Use the Definition of Good Code in AGENTS.md as the rubric. Return: strengths, issues by severity, exact file-path evidence, and a final assessment. Do not make edits.\n\nWHAT_WAS_IMPLEMENTED: [insert summary]\nPLAN_OR_REQUIREMENTS: [insert requirements]\nBASE_SHA: [insert base sha]\nHEAD_SHA: [insert head sha]"
}
```

**4. Act on feedback**
- Fix Critical issues immediately.
- Fix Important issues before proceeding.
- Note Minor issues for later only if they are truly non-blocking.
- Push back when the reviewer is wrong, but only with technical reasoning and evidence.

## Definition of Good Code Rubric (Required)

Use the canonical Definition of Good Code in `AGENTS.md` as the review rubric.

Require explicit checks for applicable items:
- code works and solves the intended problem
- evidence proves it works now (tests and validation output)
- error handling is predictable and informative
- implementation is minimal and maintainable
- tests protect changed behavior and regressions
- documentation is updated where behavior changed
- relevant quality attributes are covered (for example accessibility, security, reliability)

## Example

```text
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch Agent]
  subagent_type: general-purpose
  description: Review verification function
  prompt: review diff from BASE_SHA to HEAD_SHA against Task 2 requirements only

[Subagent returns]
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Not ready yet

You: [Fix progress indicators]
[Optionally re-run review]
```

## Integration with Workflows

**Subagent-Driven Development:**
- review after EACH task
- catch issues before they compound
- fix before moving to the next task

**Executing Plans:**
- review after each batch or at the batch boundary the plan requires
- apply feedback, then continue

**Ad-Hoc Development:**
- review before merge
- review when stuck

## Red Flags

Never:
- skip review because the change seems simple
- ignore Critical issues
- proceed with unfixed Important issues unless the user explicitly accepts the tradeoff
- let the reviewer edit code when the request is for review only
- write prompts for the removed legacy `subagent` tool or a non-existent `superpowers:code-reviewer` agent

If the reviewer is wrong:
- push back with technical reasoning
- show code and tests that prove it
- request clarification when needed
