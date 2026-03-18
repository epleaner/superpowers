---
name: design-annotation-cycle
description: Use when a design doc contains human inline comments prefixed with <<>> and the design must be revised to signoff before plan writing.
---

# Design Annotation Cycle

## Overview

Resolve human inline notes in design documents until the design is clean, explicit, and ready for plan writing.

Inline note format:

- Any line starting with `<<>>`

Core principle: every `<<>>` is either resolved into the design doc or explicitly clarified. None are ignored.

## Scope

- Design phase only
- Runs on the design doc, not the implementation plan
- Planning MUST NOT begin until the design doc contains zero `<<>>` comments

## Process

### 1) Scan

Scan the design document for all lines starting with `<<>>`.

### 2) Resolve

For each `<<>>` note:

- Apply the requested change to the nearest relevant section
- Keep edits concrete and minimal
- Preserve the design structure
- Update the paired research doc if the decision or evidence changes

### 3) Remove

After applying the change, delete that `<<>>` line.

### 4) Clarify If Ambiguous

If a `<<>>` note is ambiguous:

- Ask one direct clarifying question
- Leave that `<<>>` line in place until resolved

### 5) Repeat

Re-scan and repeat until no `<<>>` lines remain.

## Completion Gate

Design is not signed off while any `<<>>` lines remain.

Do not invoke `superpowers:writing-plans` until all `<<>>` notes are resolved and removed from the design doc.

## Red Flags

- Writing the implementation plan before design annotations are resolved
- Editing around a `<<>>` note without resolving it
- Converting `<<>>` notes into backlog items instead of deciding the design now
- Treating plan review as the primary design signoff loop

## Integration

- Called by `superpowers:brainstorming` after the research doc and design doc are written
- Blocks `superpowers:writing-plans` until the design doc is clean
- Works with `superpowers:research-before-planning` so design revisions stay evidence-backed
