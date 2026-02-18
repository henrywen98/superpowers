# Plan-Design Validation

## Problem

When `writing-plans` generates an implementation plan from a design doc, there is no formal validation that the plan is consistent with the design. Three types of mismatches occur:

1. **Coverage gaps** — Design features/constraints omitted from the plan
2. **Scope creep** — Plan introduces tasks not in the design
3. **Intent drift** — Plan's implementation approach diverges from design decisions

The existing spec compliance review in `subagent-driven-development` only catches these post-execution — too late if the plan itself is wrong.

## Solution

A new `validating-plans` skill that runs between `writing-plans` and execution. It dispatches three independent subagents to check plan-design alignment, auto-fixes mismatches, and gates execution until validation passes.

## Workflow Position

```
brainstorming → writing-plans → [NEW] validating-plans → executing-plans
```

- `writing-plans` invokes `validating-plans` automatically after saving the plan
- Can also be invoked manually via `/superpowers:validating-plans`

## Input/Output

**Input:**
- Design doc path (`docs/plans/YYYY-MM-DD-<topic>-design.md`)
- Plan doc path (`docs/plans/YYYY-MM-DD-<topic>-plan.md`)

**Output (hard gate):**
- All checks pass → proceed to execution
- Any check fails → auto-fix → re-validate → loop (max 3 rounds)
- 3 rounds exceeded → report to user with remaining issues

## Three Subagents

### 1. Coverage Checker

Ensures every design requirement has corresponding plan tasks.

- Parse design doc, extract all features, constraints, success criteria
- Scan plan tasks, mark each design element as covered or not
- Output: PASS/FAIL, coverage ratio, list of uncovered items

### 2. Scope Checker

Ensures plan contains no tasks beyond design scope.

- Parse each plan task's objective
- Check traceability to a specific design requirement
- Whitelist: testing, documentation, project setup (necessary engineering steps)
- Output: PASS/FAIL, task counts, list of out-of-scope tasks

### 3. Intent Checker

Ensures plan implementation approach matches design architecture decisions.

- Extract design's architecture choices, technology decisions, constraints
- Check plan's implementation methods against these decisions
- Output: PASS/FAIL, list of deviations with design-expected vs plan-actual

## Auto-Fix Mechanism

When any checker returns FAIL:

1. Collect all three checker reports
2. Main agent (not subagent) performs a single consolidated fix:
   - Coverage FAIL → add missing tasks to plan
   - Scope FAIL → remove out-of-scope tasks
   - Intent FAIL → modify task implementation to align with design
3. Save modified plan
4. Re-dispatch all three subagents for re-validation
5. Repeat until all pass or 3 rounds exhausted

**Why main agent fixes (not subagent):** Fixing requires holistic understanding of both design and plan. A subagent would need to load both full documents anyway, providing no context savings. Checking benefits from separation of concerns; fixing benefits from unified context.

## File Structure

New file:
```
skills/validating-plans/SKILL.md
```

## Integration Changes

**writing-plans skill:**
- After plan is saved, invoke `validating-plans` as final step
- Execution options (subagent-driven / parallel session) only shown after validation passes

**No changes needed:**
- `brainstorming` — only calls `writing-plans`
- `executing-plans` / `subagent-driven-development` — downstream of validation
