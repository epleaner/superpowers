---
name: design-annotation-cycle
description: Use when a design doc contains human inline comments prefixed with <<>> and the design must be revised to signoff before plan writing.
---

# Design Annotation Cycle

## Overview

Resolve human inline notes in `docs/plans/<slug>/design.md` until the design is clean, explicit, and ready for plan writing.

Inline note format:

- Any line starting with `<<>>`

Core principle: every `<<>>` is either resolved into the design doc or explicitly clarified. None are ignored.

## Scope

- Design phase only
- Runs in place on `docs/plans/<slug>/design.md`, not on the implementation plan
- Planning MUST NOT begin until the design doc contains zero `<<>>` comments
- This workflow supersedes old dated flat-file design conventions for active work in this repo

## Process

### 1) Scan

Scan `docs/plans/<slug>/design.md` for all lines starting with `<<>>`.

### 2) Resolve

For each `<<>>` note:

- Apply the requested change to the nearest relevant section of `docs/plans/<slug>/design.md`
- Keep edits concrete and minimal
- Preserve the design structure and stable path
- Update the paired `docs/plans/<slug>/research.md` if the decision or evidence changes
- Update `docs/plans/<slug>/index.md` if the design-phase thread state, next action, or resume target changes materially

### 3) Remove

After applying the change, delete that `<<>>` line.

### 4) Clarify If Ambiguous

If a `<<>>` note is ambiguous:

- Ask one direct clarifying question
- Leave that `<<>>` line in place until resolved

### 5) Repeat

Re-scan `docs/plans/<slug>/design.md` and repeat until no `<<>>` lines remain.

## Completion Gate

Design is not signed off while any `<<>>` lines remain in `docs/plans/<slug>/design.md`.

Do not invoke `superpowers:writing-plans` until all `<<>>` notes are resolved and removed from the in-place design doc.

Do not start implementation either. Design signoff is a real hard gate before planning or execution for any work that required design review.

## Red Flags

- Writing the implementation plan before design annotations are resolved
- Editing around a `<<>>` note without resolving it
- Converting `<<>>` notes into backlog items instead of deciding the design now
- Treating plan review as the primary design signoff loop

## Integration

- Called by `superpowers:brainstorming` after `docs/plans/<slug>/research.md` and `docs/plans/<slug>/design.md` are written
- Blocks `superpowers:writing-plans` until the design doc is clean
- Works with `superpowers:research-before-planning` so design revisions stay evidence-backed
- Keeps `docs/plans/<slug>/design.md` as the stable current-path design file described in `docs/agent-doc-system.md`
