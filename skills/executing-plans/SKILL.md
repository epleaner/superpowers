---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, keep the plan doc updated inline as work progresses, report for review between batches.

**Core principle:** The plan is the source of truth for current execution status, not just a static checklist.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. Identify sprint-scoped or other top-level grouped work
4. Identify grouped parent tasks that already contain child subitems
5. Confirm the plan uses this inline progress vocabulary for deliverables/tasks:
   - `[ ]` not started
   - `[-]` in progress
   - `[x]` done
   - `[!]` blocked
6. Confirm the plan uses this inline metadata vocabulary:
   - done items: `commit`, `verification`, `note`
   - blocked items: `blocker`
   - `branch` OPTIONAL only when it materially helps resume in-progress or blocked work
7. If concerns: Raise them with your human partner before starting
8. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default: First 3 tasks**

Before executing, treat the plan doc as a living record. Update the relevant inline task status whenever work state changes using the same vocabulary the plan was written with:
- mark `[-]` when you start a task
- mark `[x]` only after delivery is complete and `verification` has passed
- mark `[!]` immediately when blocked, with a specific `blocker`
- keep inline metadata current so a new session can resume from the plan alone

Literal subagent invocation rule:
- The `agent` field MUST name the real subagent to run.
- `kind` is display metadata only.
- For grouped top-level work, use `agent: "sprint-orchestrator"`.
- For grouped parent-task work, use `agent: "task-orchestrator"`.
- `task-orchestrator` MUST dispatch leaf agents for concrete work and MUST NOT recurse into another `task-orchestrator` for the same ticket/work item.
- A nested `task-orchestrator` is allowed only for an explicitly distinct subgroup/workstream with a distinct label.
- You MUST NOT use `agent: "worker"` with orchestrator `kind` values as a shortcut.

For each task:
1. Update the plan doc inline from `[ ]` to `[-]` when starting
2. If the task is sprint-scoped or other top-level grouped work, dispatch `sprint-orchestrator`
3. If the task contains child subitems below that level, dispatch `task-orchestrator` rather than managing each child directly from the controller
4. If the task is a leaf task, follow each step exactly (plan has bite-sized steps)
5. Update the inline `note` whenever meaningful progress changes what the next session needs to know
6. Run verifications as specified and record inline `verification`
7. When delivered, update the plan doc inline to `[x]` and record `commit`, `verification`, and `note`
8. If blocked, update the plan doc inline to `[!]`, record the exact `blocker`, and include `branch` only if it materially helps resume the work

Canonical inline examples:
- `[ ] Ticket 2.1: Add retry banner`
- `[-] Ticket 2.1: Add retry banner — note: component scaffolded; wiring CTA state`
- `[x] Ticket 2.1: Add retry banner — commit: abc1234; verification: bin/yarn workspace baraza test RetryBanner && npx -y react-doctor@latest . --verbose --diff; note: banner rendered with retry CTA`
- `[!] Ticket 2.2: Remove legacy toast — blocker: need product decision on fallback UX; branch: ep/retry-banner-cleanup`

Example grouped-work payloads:

```json
{
  "agent": "sprint-orchestrator",
  "kind": "sprint-orchestrator",
  "label": "Sprint A",
  "task": "Execute Sprint A using the provided grouped plan."
}
```

```json
{
  "agent": "task-orchestrator",
  "kind": "task-orchestrator",
  "label": "Task 2.1",
  "parentLabel": "Sprint A",
  "task": "Execute Task 2.1 using the provided child subtasks."
}
```

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
- Treat the plan doc as a living handoff artifact, not a static checklist
- Keep inline task status current: `[ ]`, `[-]`, `[x]`, `[!]`
- Use the same inline metadata vocabulary throughout: `commit`, `verification`, `note`, `blocker`, and optional `branch`
- For delivered items, record `commit`, `verification`, and `note`
- For blocked items, record `blocker`; use `branch` only when it materially helps the next session resume
- Detect sprint/top-level grouped work before executing
- Use `sprint-orchestrator` for sprint-scoped or other top-level grouped work
- Use `task-orchestrator` for any grouped parent task that already contains child subitems, whether child execution is sequential or parallel
- Use the real orchestrator agent names in the `agent` field; `kind` is only for display
- Never use `agent: "worker"` to impersonate `task-orchestrator` or `sprint-orchestrator`
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
