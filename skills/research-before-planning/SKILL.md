---
name: research-before-planning
description: Use when brainstorming surfaces decision-critical unknowns or before writing implementation plans, requiring a paired `docs/plans/<slug>/research.md` evidence document and aligned `index.md` thread state.
---

# Research Before Planning

## Overview

Use research to answer concrete design decisions, not to collect generic context.

Core principle: state-based progression. Move forward only when decision-critical unknowns are resolved.

## Required Output

For any feature with planning work, maintain a paired thread folder under `docs/plans/<slug>/` and keep its research document current:

- `docs/plans/<slug>/research.md`
- `docs/plans/<slug>/index.md`

`research.md` is required before the design annotation cycle can close and before handoff to `superpowers:writing-plans`. `index.md` MUST reflect the current phase, next action, and resume target whenever research changes materially.

## Process

### 1) Enter from Brainstorming

When brainstorming reveals an unknown that can change architecture, contracts, interfaces, testing strategy, or rollout risk:

- Write the unknown as a decision question
- Mark it unresolved

### 2) Run Focused Research Loops

For each unresolved decision question:

- Gather only evidence needed to answer that question
- Record findings in `docs/plans/<slug>/research.md`
- Record decision impact on architecture and implementation scope
- Update `docs/plans/<slug>/index.md` when findings materially change the phase, next action, or resume target
- Mark question resolved or keep unresolved with explicit blocker

### 3) Return to Brainstorming

Use research findings to choose the design path. Continue loop until decision-critical unknowns are resolved or explicitly de-scoped.

### 4) Write the Research Doc Before Design Signoff

Before design signoff:

- Save the current evidence and decisions into `docs/plans/<slug>/research.md`
- Keep the research doc aligned with `docs/plans/<slug>/design.md` during annotation resolution
- Update `docs/plans/<slug>/index.md` whenever research changes the active phase or next action
- Update the research doc whenever design feedback changes a decision or evidence trail

### 5) Mandatory Hardening Pass (After Design Stabilizes, Before Planning)

After the design is stable enough to document, run a thorough hardening pass before planning:

- Validate file touchpoints and dependency surfaces
- Validate interface and contract constraints
- Validate edge cases and failure modes
- Validate migration/rollback and compatibility concerns
- Validate test and verification impact

If hardening reveals architecture-level conflicts, reopen brainstorming.

If no conflicts remain and the design annotation cycle is clean, planning can proceed.

## Red Flags

- Research that does not answer a specific decision question
- Moving to `writing-plans` without `docs/plans/<slug>/research.md`
- Treating unresolved decision-critical unknowns as "good enough"
- Skipping hardening pass because design "looks right"
- Letting `docs/plans/<slug>/research.md` drift away from the annotated design doc
- Failing to update `docs/plans/<slug>/index.md` after material research changes

## Integration

- Called during `superpowers:brainstorming` whenever unknowns appear
- Required before `superpowers:writing-plans`
- Pairs with `superpowers:design-annotation-cycle` so design revisions stay evidence-backed
