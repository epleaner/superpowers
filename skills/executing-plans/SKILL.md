---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. Identify sprint-scoped or other top-level grouped work
4. Identify grouped parent tasks that already contain child subitems
5. If concerns: Raise them with your human partner before starting
6. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. If the task is sprint-scoped or other top-level grouped work, dispatch `sprint-orchestrator`
3. If the task contains child subitems below that level, dispatch `task-orchestrator` rather than managing each child directly from the controller
4. If the task is a leaf task, follow each step exactly (plan has bite-sized steps)
5. Run verifications as specified
6. Mark as completed

### Step 3: Report
When batch complete:
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."

### Step 4: Continue
Based on feedback:
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking
- You discover new or unresolved `>>` notes while executing; refresh the plan via `superpowers:writing-plans` and/or `superpowers:plan-annotation-cycle`, then continue

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Detect sprint/top-level grouped work before executing
- Use `sprint-orchestrator` for sprint-scoped or other top-level grouped work
- Use `task-orchestrator` for any grouped parent task that already contains child subitems, whether child execution is sequential or parallel
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Between batches: just report and wait
- Stop when blocked, don't guess
- Never start implementation on main/master branch without explicit user consent

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
