---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

Load the plan, review it critically, execute tasks in batches, keep the plan doc updated inline as work progresses, and report for review between batches.

**Core principle:** The plan is the source of truth for current execution status, not just a static checklist.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. Read the plan file.
2. Review critically and identify questions or concerns.
3. Identify top-level grouped work that must stay sequential unless the plan explicitly allows parallelism.
4. Confirm the plan uses this inline progress vocabulary:
   - `[ ]` not started
   - `[-]` in progress
   - `[x]` done
   - `[!]` blocked
5. Confirm the plan uses this inline metadata vocabulary:
   - done items: `commit`, `verification`, `note`
   - blocked items: `blocker`
   - `branch` OPTIONAL only when it materially helps resume in-progress or blocked work
6. If concerns exist, raise them before starting.
7. If no concerns exist, create TodoWrite and proceed.

### Step 2: Execute Batch
**Default: first 3 tasks**

Before executing, treat the plan doc as a living record. Update the relevant inline task status whenever work state changes:
- mark `[-]` when you start a task
- mark `[x]` only after delivery is complete and `verification` has passed
- mark `[!]` immediately when blocked, with a specific `blocker`
- keep inline metadata current so a new session can resume from the plan alone

Subagent rule:
- You MUST use the installed `pi-subagents` tools: `Agent`, `get_subagent_result`, and `steer_subagent`.
- You MUST call `Agent` with `subagent_type`, `prompt`, and `description`.
- You MUST NOT write legacy `subagent` payloads or rely on the removed `agent`/`kind`/`label` schema.
- Use built-in types like `general-purpose`, `Explore`, and `Plan` unless the repo provides a better custom type in `.pi/agents/`.

For each task:
1. Update the plan doc inline from `[ ]` to `[-]` when starting.
2. If the task is a grouped top-level phase, execute its child tasks sequentially unless the plan explicitly allows top-level parallelism.
3. If the task is a leaf task, dispatch a focused `Agent` worker with the exact task text and context.
4. If the task can safely run in parallel with other leaf tasks, you MAY dispatch it in the background and later collect it with `get_subagent_result`.
5. Use `steer_subagent` to answer questions or correct drift instead of restarting good runs.
6. Run verifications as specified and record inline `verification`.
7. When delivered, update the plan doc inline to `[x]` and record `commit`, `verification`, and `note`.
8. If blocked, update the plan doc inline to `[!]`, record the exact `blocker`, and include `branch` only if it materially helps resume the work.

Canonical inline examples:
- `[ ] Ticket 2.1: Add retry banner`
- `[-] Ticket 2.1: Add retry banner — note: component scaffolded; wiring CTA state`
- `[x] Ticket 2.1: Add retry banner — commit: abc1234; verification: bin/yarn workspace baraza test RetryBanner && npx -y react-doctor@latest . --verbose --diff; note: banner rendered with retry CTA`
- `[!] Ticket 2.2: Remove legacy toast — blocker: need product decision on fallback UX; branch: ep/retry-banner-cleanup`

Example implementation dispatch:

```json
{
  "subagent_type": "general-purpose",
  "description": "Implement retry banner",
  "prompt": "Implement Ticket 2.1 from the provided plan excerpt. Current branch: ep/retry-banner. Do not create or switch branches. Follow TDD. Run the required verification commands. Return a concise summary of changes, verification, and any open questions.\n\n[insert task text and context here]"
}
```

Example background collection:

```json
{
  "agent_id": "agent_123",
  "wait": true
}
```

### Step 3: Report
When the batch is complete:
- show what was implemented
- show verification output
- say: "Ready for feedback."

### Step 4: Continue
Based on feedback:
- apply changes if needed
- execute the next batch
- repeat until complete

### Step 5: Complete Development

After all tasks complete and verification is clean:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, and execute the chosen path

## When to Stop and Ask for Help

STOP executing immediately when:
- you hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- the plan has critical gaps preventing safe execution
- you do not understand an instruction
- verification fails repeatedly

Ask for clarification rather than guessing.

## When to Revisit Earlier Steps

Return to Review (Step 1) when:
- the partner updates the plan based on your feedback
- the fundamental approach needs rethinking
- you discover new or unresolved `>>` notes while executing; refresh the plan via `superpowers:writing-plans` and/or `superpowers:plan-annotation-cycle`, then continue

Do not force through blockers.

## Remember
- Review the plan critically first.
- Treat the plan doc as a living handoff artifact, not a static checklist.
- Keep inline task status current: `[ ]`, `[-]`, `[x]`, `[!]`.
- Use the same inline metadata vocabulary throughout: `commit`, `verification`, `note`, `blocker`, and optional `branch`.
- For delivered items, record `commit`, `verification`, and `note`.
- For blocked items, record `blocker`; use `branch` only when it materially helps the next session resume.
- Use `Agent` for focused execution units.
- Use `get_subagent_result` to collect background work.
- Use `steer_subagent` to unblock or redirect running agents.
- Never write instructions for the removed legacy `subagent` tool.
- Follow plan steps exactly.
- Do not skip verifications.
- Reference skills when the plan says to.
- Between batches: report and wait.
- Stop when blocked; do not guess.
- Never start implementation on main/master branch without explicit user consent.

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
