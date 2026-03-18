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
- Design annotation cycle is complete via `superpowers:design-annotation-cycle`
- Paired research doc exists: `docs/plans/YYYY-MM-DD-<feature-name>-research.md`
- Mandatory hardening research pass is complete (from `superpowers:research-before-planning`)

If any of these are missing, stop and run `superpowers:research-before-planning` and/or return to `superpowers:brainstorming`.

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
- The plan is not a second human signoff artifact; the design doc already served that purpose

## Tracker Timing Rule

After the design doc is signed off, planning becomes execution prep rather than another approval phase.

That means:
- you MAY create or update beads/tasks to match the execution plan once the design annotation cycle is complete
- you MUST NOT pause for a separate plan-approval loop unless the user explicitly asks for one
- if tracker structure changes, keep it aligned with the final written plan rather than with intermediate drafts

## Plan Finalization

After writing the plan:

1. Tighten the document until it is execution-ready.
2. Save it to `docs/plans/YYYY-MM-DD-<feature-name>.md`.
3. Open the completed plan in Zed for user visibility.
4. Do not ask for inline plan annotations.
5. Continue directly into execution via `auto_handoff`.

Required transition:

**Call `auto_handoff` with a goal that tells the next turn to start `superpowers:executing-plans` on Sprint 1 and continue through the entire plan, using additional `auto_handoff` calls between sprints and long-running tasks to keep context focused.**

Do not stop with "next step is to execute the plan." Continue.

## Integration

**Required workflow skills:**
- **superpowers:research-before-planning** - Required pre-planning (unknown resolution + hardening pass)
- **superpowers:design-annotation-cycle** - Required before planning starts (must resolve all `<<>>` in the design doc)
- **superpowers:brainstorming** - Produces design and decision questions
- **superpowers:executing-plans** - Required autonomous execution target after plan writing
- **auto-handoff** - Required to move directly from plan writing into execution without pausing for another approval loop
