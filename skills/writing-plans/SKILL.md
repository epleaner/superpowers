---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI (You Aren't Gonna Need It). TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Required Inputs Before Planning

Before writing the implementation plan, verify:

- Design is locked from `superpowers:brainstorming`
- Paired research doc exists: `docs/plans/YYYY-MM-DD-<feature-name>-research.md`
- Mandatory hardening research pass is complete (from `superpowers:research-before-planning`)

If any of these are missing, stop and run `superpowers:research-before-planning`.

## Sprint Planning Requirements

When the user asks for a project plan (sprints/tasks/tickets), enforce these rules:

- Do not include timeline/date estimates unless explicitly requested.
- Organize work into sequential sprints.
- Every sprint must end in a runnable, testable, demoable software increment that builds on previous sprints.
- Be exhaustive and technical across relevant streams: application code, data model/migrations, API contracts, UI, infra/config, observability, docs, rollout/risk controls.
- Use atomic tickets only. A ticket is valid only if it can be completed and committed independently.
- Every ticket must include:
  - clear technical objective and scope boundary
  - explicit dependencies (or "none")
  - acceptance criteria
  - Definition of Good Code mapping to applicable AGENTS.md criteria (for example correctness, tests, docs, error handling, relevant quality attributes)
  - required automated tests; if tests do not fit, an explicit alternative validation method with commands/checks and expected outcomes
- Finish by reviewing and tightening the markdown plan before presenting it. Remove ambiguity, resolve internal inconsistencies, and ensure execution readiness.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

Plans SHOULD include inline execution tracking so `superpowers:executing-plans` can keep progress current in the plan itself. Use this shared inline vocabulary for deliverables/tickets/tasks:
- statuses: `[ ]` not started, `[-]` in progress, `[x]` done, `[!]` blocked
- metadata: `commit`, `verification`, `note`, `blocker`, and optional `branch`

Use the metadata consistently:
- done items: `commit`, `verification`, `note`
- blocked items: `blocker`
- `branch` OPTIONAL only when it materially helps resume in-progress or blocked work

````markdown
## Sprint N: [Sprint Goal]

**Demo Increment:** [What can be run/tested/demoed at sprint end]

### [ ] Ticket N.M: [Atomic Ticket Name]

**Objective:** [Single technical outcome]
**Dependencies:** [Ticket IDs or "none"]
**Acceptance Criteria:**
- [Concrete criterion]
- [Concrete criterion]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```

**Canonical Inline Status Examples:**
- `[ ] Ticket N.M: [Atomic Ticket Name]`
- `[-] Ticket N.M: [Atomic Ticket Name] — note: implementation in progress`
- `[x] Ticket N.M: [Atomic Ticket Name] — commit: abc1234; verification: pytest tests/path/test.py::test_name -v; note: minimal implementation shipped`
- `[!] Ticket N.M: [Atomic Ticket Name] — blocker: waiting on API contract decision; branch: ep/example-branch`
````

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits
- Write tickets/tasks so their inline status can be updated during execution without rewriting the plan structure
- Use the same inline vocabulary and canonical example shapes that `superpowers:executing-plans` expects: statuses `[ ]`, `[-]`, `[x]`, `[!]`; metadata `commit`, `verification`, `note`, `blocker`, optional `branch`
- Default ticket prefix SHOULD be `[ ]` so execution can move it to `[-]`, `[x]`, or `[!]`
- Done items SHOULD be able to record `commit`, `verification`, and `note`
- Blocked items SHOULD be able to record `blocker`; `branch` is OPTIONAL and only useful when it materially helps resume work
- If tests are not appropriate for a ticket, provide explicit validation steps and expected outcomes
- Each sprint must be demoable and build on previous sprints
- Before presenting a plan as final, **REQUIRED SUB-SKILL:** run `superpowers:plan-annotation-cycle`
- Do not hand off to execution with any unresolved `<<>>` inline notes

## Required Human Annotation Loop

After writing the first complete draft and saving it:

1. Present it explicitly as a **draft**, not final.
2. Ask the user to annotate directly in the plan with `<<>>` comments.
3. Wait for user annotations (or explicit opt-out).
4. Run `superpowers:plan-annotation-cycle` to resolve all `<<>>` notes.
5. Re-run scan until zero `<<>>` notes remain.

You MUST NOT offer execution handoff before step 4 is complete.

## Tracker Timing Rule

When writing or revising a plan, you MUST treat the plan as the proposal of record until the user approves it.

That means:
- you MUST NOT create, restructure, or bulk-update beads/tasks to mirror the draft plan before approval
- you SHOULD keep any pre-existing tracker state untouched while the plan is still under review
- you MAY reference existing bead IDs in the draft when needed for context
- only after explicit user approval SHOULD you create or update beads so tracker structure matches the approved plan

If the user asks you to sync tracker state before approval, do it explicitly and call out that the tracker is being updated ahead of normal workflow.

## Execution Handoff

After saving the first draft, request user `<<>>` annotations.
After user annotations are provided (or the user explicitly opts out), resolve all inline `<<>>` notes via `superpowers:plan-annotation-cycle`.

Only when zero `<<>>` notes remain, offer execution choice.

Required draft prompt before annotation cycle:

**"Draft plan saved to `docs/plans/<filename>.md`. Add any inline feedback using `<<>>` comments directly in the file, then tell me when to resolve them."**

Final handoff prompt (after annotation cycle is clean):

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I use `Agent` / `steer_subagent` / `get_subagent_result` for focused task execution and review between tasks

**2. Parallel Session (separate)** - Open new session with executing-plans, full end-to-end execution sprint by sprint with auto-handoff between sprints when helpful

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Focused `Agent` calls per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans

## Integration

**Required workflow skills:**
- **superpowers:research-before-planning** - Required pre-planning (unknown resolution + hardening pass)
- **superpowers:plan-annotation-cycle** - Required before execution handoff (must resolve all `<<>>`)
- **superpowers:brainstorming** - Produces design and decision questions
