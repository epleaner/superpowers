---
name: research-before-planning
description: Use when brainstorming surfaces decision-critical unknowns or before writing implementation plans, requiring a paired docs/plans/YYYY-MM-DD-<feature>-research.md evidence document.
---

# Research Before Planning

## Overview

Use research to answer concrete design decisions, not to collect generic context.

Core principle: state-based progression. Move forward only when decision-critical unknowns are resolved.

## Required Output

For any feature with planning work, maintain a paired research document:

- `docs/plans/YYYY-MM-DD-<feature>-research.md`

This file is required before handoff to `superpowers:writing-plans`.

## Process

### 1) Enter from Brainstorming

When brainstorming reveals an unknown that can change architecture, contracts, interfaces, testing strategy, or rollout risk:

- Write the unknown as a decision question
- Mark it unresolved

### 2) Run Focused Research Loops

For each unresolved decision question:

- Gather only evidence needed to answer that question
- Record findings in `*-research.md`
- Record decision impact on architecture and implementation scope
- Mark question resolved or keep unresolved with explicit blocker

### 3) Return to Brainstorming

Use research findings to choose the design path. Continue loop until decision-critical unknowns are resolved or explicitly de-scoped.

### 4) Mandatory Hardening Pass (After Design Lock)

After design is locked, run a thorough hardening pass before planning:

- Validate file touchpoints and dependency surfaces
- Validate interface and contract constraints
- Validate edge cases and failure modes
- Validate migration/rollback and compatibility concerns
- Validate test and verification impact

If hardening reveals architecture-level conflicts, reopen brainstorming.

If no conflicts remain, planning can proceed.

## Red Flags

- Research that does not answer a specific decision question
- Moving to `writing-plans` without `*-research.md`
- Treating unresolved decision-critical unknowns as "good enough"
- Skipping hardening pass because design "looks right"

## Integration

- Called during `superpowers:brainstorming` whenever unknowns appear
- Required before `superpowers:writing-plans`
- Pairs with `superpowers:plan-annotation-cycle` to finalize executable plans
