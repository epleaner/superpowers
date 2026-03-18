---
name: plan-annotation-cycle
description: Use when maintaining an older workflow where a plan doc still contains human inline comments prefixed with <<>>; prefer design-annotation-cycle for the current workflow.
---

# Plan Annotation Cycle

## Overview

This is a legacy compatibility skill.

Current workflow: annotate the design doc, not the implementation plan. Use `superpowers:design-annotation-cycle` for all new work.

Use this skill only when you are maintaining an older workflow or resuming a legacy plan file that already contains `<<>>` comments.

## Scope

- Legacy planning workflow only
- Not the default signoff path for new work
- New workflows MUST move the human annotation loop to the design doc

## Process

### 1) Scan

Scan the legacy plan document for all lines starting with `<<>>`.

### 2) Resolve

For each `<<>>` note:

- Apply the requested change to the nearest relevant ticket, step, or acceptance criteria
- Keep edits concrete and minimal
- Preserve plan structure and numbering

### 3) Remove

After applying the change, delete that `<<>>` line.

### 4) Clarify If Ambiguous

If a `<<>>` note is ambiguous:

- Ask one direct clarifying question
- Leave that `<<>>` line in place until resolved

### 5) Repeat

Re-scan and repeat until no `<<>>` lines remain.

## Completion Gate

A legacy plan is not clean while any `<<>>` lines remain.

Do not execute a legacy plan until all `<<>>` notes are resolved and removed.

## Red Flags

- Using this skill as the default review loop for new work
- Writing a new workflow that asks for plan annotations instead of design annotations
- Beginning execution with unresolved `<<>>` comments in a legacy plan

## Integration

- Legacy fallback for old plans only
- Superseded by `superpowers:design-annotation-cycle` in the current workflow
