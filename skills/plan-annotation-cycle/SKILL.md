---
name: plan-annotation-cycle
description: Use when plan documents contain human inline comments prefixed with >> during planning, to resolve each comment into plan updates before execution handoff.
---

# Plan Annotation Cycle

## Overview

Resolve human inline notes in plan files until the plan is clean and executable.

Inline note format:

- Any line starting with `>>`

Core principle: every `>>` is either resolved into the plan or explicitly clarified. None are ignored.

## Scope

- Planning phase only
- Not an execution-phase workflow

By execution start, plans should contain zero `>>` comments.

## Process

### 1) Scan

Scan plan document for all lines starting with `>>`.

### 2) Resolve

For each `>>` note:

- Apply the requested change to the nearest relevant ticket/step/acceptance criteria
- Keep edits concrete and minimal
- Preserve plan structure and numbering

### 3) Remove

After applying the change, delete that `>>` line.

### 4) Clarify If Ambiguous

If a `>>` note is ambiguous:

- Ask one direct clarifying question
- Leave that `>>` line in place until resolved

### 5) Repeat

Re-scan and repeat until no `>>` lines remain.

## Completion Gate

Plan is not complete while any `>>` lines remain.

Do not hand off to execution (`executing-plans` or `subagent-driven-development`) until all `>>` notes are resolved and removed.

## Red Flags

- Editing around a `>>` note without resolving it
- Converting `>>` notes into open backlog items instead of deciding now
- Beginning execution with unresolved `>>` comments

## Integration

- Called by `superpowers:writing-plans` before presenting plan as final
- Works with `superpowers:research-before-planning` so plan decisions stay evidence-backed
