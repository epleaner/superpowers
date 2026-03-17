---
name: executing-plans
description: Use when you have a written implementation plan to execute end-to-end in a separate session
---

# Executing Plans

Load the plan, review it critically, execute it to completion sprint by sprint, ticket by ticket, and subtask by subtask, keep the plan doc updated inline as work progresses, and stop only for blockers or specific user direction.

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

### Step 2: Execute the Full Plan
Default behavior: continue through the entire plan without waiting for interim approval.

Execution order:
- work sprint by sprint
- within each sprint, work ticket by ticket
- within each ticket, work subtask by subtask
- preserve plan-defined sequencing unless the plan explicitly allows safe parallelism
- stop only for a real blocker, repeated verification failure, or a specific user decision you cannot make safely

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

For each execution unit:
1. Update the plan doc inline from `[ ]` to `[-]` when starting.
2. If the unit is a grouped sprint, phase, or ticket, execute its child items in order unless the plan explicitly allows parallelism.
3. If the unit is a leaf task, dispatch a focused `Agent` worker with the exact task text and context.
4. If sibling leaf tasks can safely run in parallel, you MAY dispatch them in the background and later collect them with `get_subagent_result`.
5. Use `steer_subagent` to answer questions or correct drift instead of restarting good runs.
6. Run verifications as specified and record inline `verification`.
7. When delivered, update the plan doc inline to `[x]` and record `commit`, `verification`, and `note`.
8. If blocked, update the plan doc inline to `[!]`, record the exact `blocker`, and include `branch` only if it materially helps resume the work.
9. After finishing one execution unit, continue directly into the next planned unit unless a stop condition applies.

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

### Step 3: Report Progress While Continuing
After each meaningful milestone, keep the user informed, but do not stop execution just to ask for routine feedback.

Default reporting behavior:
- report at sprint completion, major phase completion, or real blockers
- include what was implemented and the verification output
- state the next sprint, ticket, or subtask being executed
- continue automatically unless the user interrupts or a stop condition applies

Sprint-boundary rule:
- A sprint boundary means a sprint, phase, or similar plan-level transition, not an ordinary small task transition.
- If you are crossing a sprint boundary and the next execution phase needs compacted, focused context, you SHOULD use `auto_handoff` instead of carrying the full old context forward.
- **REQUIRED SUB-SKILL:** Use `auto-handoff` for goal-writing and post-call behavior.
- You MUST use `auto_handoff` between sprints when continuing execution would benefit from focused context.
- You MUST NOT use `auto_handoff` after every small task or subtask.
- The `goal` MUST state the exact next sprint outcome.
- After invoking `auto_handoff`, you MUST NOT add duplicate handoff summary narration.

### Step 4: Continue Until Completion
Keep executing the plan until one of these is true:
- every planned item is complete and verified
- you are blocked
- you need specific user direction to resolve an intentional product, policy, or design decision

Do not stop merely because a small batch finished.

### Step 5: Complete Development

After all tasks complete and verification is clean:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, and execute the chosen path

## When to Stop and Ask for Help

STOP executing immediately when:
- you hit a blocker mid-sprint, mid-ticket, or mid-subtask (missing dependency, test fails, instruction unclear)
- the plan has critical gaps preventing safe execution
- you do not understand an instruction
- verification fails repeatedly
- you need a specific user decision on product, UX, policy, or scope

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
- Continue automatically sprint by sprint, ticket by ticket, and subtask by subtask.
- Report progress without pausing for routine approval.
- At sprint boundaries, you SHOULD use `auto_handoff` when the next execution phase needs compacted, focused context.
- You MUST use `auto_handoff` between sprints when that keeps the next sprint focused.
- You MUST NOT use `auto_handoff` for every minor task transition.
- When using `auto_handoff`, the `goal` MUST name the exact next sprint outcome, and you MUST NOT add duplicate summary narration after the tool call.
- Stop when blocked or when specific user direction is required; do not guess.
- Never start implementation on main/master branch without explicit user consent.

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
