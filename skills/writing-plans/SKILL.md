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

````markdown
## Sprint N: [Sprint Goal]

**Demo Increment:** [What can be run/tested/demoed at sprint end]

### Ticket N.M: [Atomic Ticket Name]

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
````

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits
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

## Execution Handoff

After saving the first draft, request user `<<>>` annotations.
After user annotations are provided (or the user explicitly opts out), resolve all inline `<<>>` notes via `superpowers:plan-annotation-cycle`.

Only when zero `<<>>` notes remain, offer execution choice.

Required draft prompt before annotation cycle:

**"Draft plan saved to `docs/plans/<filename>.md`. Add any inline feedback using `<<>>` comments directly in the file, then tell me when to resolve them."**

Final handoff prompt (after annotation cycle is clean):

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans

## Integration

**Required workflow skills:**
- **superpowers:research-before-planning** - Required pre-planning (unknown resolution + hardening pass)
- **superpowers:plan-annotation-cycle** - Required before execution handoff (must resolve all `<<>>`)
- **superpowers:brainstorming** - Produces design and decision questions
