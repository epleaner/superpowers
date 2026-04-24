---
name: executing-plans
description: Use when you have a written implementation plan to execute end-to-end, usually after an autonomous handoff from writing-plans.
---

# Executing Plans

Load the plan, review it critically, execute it to completion sprint by sprint, ticket by ticket, and subtask by subtask, keep the plan doc updated inline as work progresses, and stop only for blockers or specific user direction.

**Core principle:** `docs/plans/<slug>/plan.md` is the source of truth for execution status. `docs/plans/<slug>/index.md` is the durable thread-state entrypoint for phase, resume, next action, and blocker state.

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
6. If the plan has unresolved `<<>>` notes, treat that as stale forbidden content. Do not continue execution. Escalate immediately because the plan violates the no-plan-annotation workflow.
7. If concerns exist, revise the plan or raise only the questions that truly require a user decision.
8. If no concerns exist, create TodoWrite and proceed.

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

Also treat `docs/plans/<slug>/index.md` as the durable repo-state summary for cross-session recovery. Update it at meaningful execution boundaries, especially when:
- the phase changes, such as planning -> executing, executing -> blocked, or executing -> done
- you finish a sprint or other major phase boundary
- you stop for a blocker or for required user input
- you are about to call `auto_handoff` after a substantial unit of work
- the next action or best resume target has changed materially

At those boundaries, update `index.md` with the smallest useful durable state:
- `phase`
- `last_active`
- `next_action`
- `blocker`
- `resume_from` when helpful

Keep ownership clean:
- execution detail, inline ticket progress, verification notes, and commit metadata stay in `plan.md`
- durable thread state and resume guidance stay in `index.md`
- do not treat `auto_handoff` as a substitute for updating `index.md`

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
10. At any meaningful boundary, make the corresponding minimal `index.md` update before moving on or handing off.

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

### Step 3: Keep Going With Aggressive Context Control
Default reporting behavior:
- do NOT stop for routine approval
- do NOT emit a milestone-only message whose only purpose is to say "next step is to continue"
- continue silently when no user action is required
- surface blockers immediately and specific user decisions only when you truly need them

Context-control rule:
- **REQUIRED SUB-SKILL:** use `auto-handoff` for goal-writing and post-call behavior.
- You MUST use `auto_handoff` between every sprint.
- `auto_handoff` between sprints is a CONTINUATION rule, not a stopping rule.
- Finishing a sprint means you continue into the next sprint, either in the same session or via immediate `auto_handoff`.
- Sprint boundaries are context-management boundaries, NOT stopping boundaries.
- You MUST NOT stop merely because a sprint finished.
- After each sprint, you MUST either:
  1. continue directly into the next sprint in the same session, or
  2. use `auto_handoff` with an explicit goal that starts the next sprint immediately.
- Before a sprint-boundary handoff, you MUST update `index.md` with the current `phase`, `last_active`, `next_action`, and `resume_from`, plus `blocker` if present.
- You MUST pause at a sprint boundary only when the user explicitly instructs a stop, a real blocker is present, or a required product/policy decision is missing.
- If a sprint is large enough that a single ticket or cluster of tickets materially bloats context, you SHOULD use `auto_handoff` between those long-running tasks as well.
- Before those intra-sprint handoffs, you SHOULD make the minimal `index.md` update needed for durable resume.
- You SHOULD prefer an earlier handoff over carrying bloated context forward.
- Each `goal` MUST name the exact next sprint or task outcome.
- After invoking `auto_handoff`, you MUST NOT add duplicate narration.

Examples of good handoff goals:
- `Execute Sprint 2 of docs/plans/foo/plan.md to completion, updating inline statuses and verification as you go.`
- `Finish Ticket 3.4 in docs/plans/foo/plan.md, then continue the remaining Sprint 3 tasks with the same execution discipline.`

### Step 4: Continue Until Completion
Keep executing the plan until one of these is true:
- every planned item is complete and verified
- you are blocked
- you need specific user direction to resolve an intentional product, policy, or design decision
- the user explicitly tells you to stop after a named boundary

Do not stop merely because a small batch finished.
Do not stop merely because a sprint finished.
If one sprint completes and another sprint remains planned, the default action is to continue.

### Step 5: Complete Development

After all tasks complete and verification is clean:
- If the packet is now done or cancelled, move its file/folder from `docs/plans/` to `docs/archived/`, mark the archived outcome explicitly (`done` or `cancelled`), and update the live/archive indexes plus any still-live parent roadmap docs before branch-finishing steps.
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
- the fundamental approach needs rethinking
- the plan is updated materially during execution
- you discover unresolved design drift that requires `superpowers:brainstorming` or `superpowers:writing-plans`

Do not force through blockers.

## Remember
- Review the plan critically first.
- Treat `plan.md` as a living execution artifact, not a static checklist.
- Treat `index.md` as the durable thread-state entrypoint for repo-based resume while the thread is active.
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
- Use `auto_handoff` between every sprint.
- Treat sprint boundaries as continuation points, not pause points.
- Update `index.md` at meaningful execution boundaries with `phase`, `last_active`, `next_action`, `blocker`, and `resume_from` when helpful.
- When a thread stops being active because it is done or cancelled, archive it in the same task instead of leaving stale completion state under `docs/plans/`.
- Use `auto_handoff` inside a sprint whenever context is starting to balloon.
- Use `auto_handoff` for session compaction and focus only; do not use it instead of durable repo-state updates in `index.md`.
- Stop only when blocked, when specific user direction is required, or when the user explicitly tells you to stop at a boundary; do not guess.
- Never start implementation on main/master branch without explicit user consent.

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
- **auto-handoff** - REQUIRED for sprint-to-sprint and long-task context compaction
