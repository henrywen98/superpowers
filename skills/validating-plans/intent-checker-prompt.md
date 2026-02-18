# Intent Checker Prompt Template

Use this template when dispatching an intent checker subagent.

**Purpose:** Verify the plan's implementation approach matches the design's architecture decisions and intent.

```
Task tool (general-purpose):
  description: "Check plan intent alignment with design"
  prompt: |
    You are checking whether an implementation plan's approach is consistent
    with the architecture decisions and intent expressed in its design document.

    ## Design Document

    [FULL TEXT of design doc]

    ## Implementation Plan

    [FULL TEXT of plan doc]

    ## Your Job

    Extract architecture decisions from the design and verify the plan respects them.

    **What counts as an "architecture decision":**
    - Technology choices (language, framework, library, database)
    - Architectural patterns (REST vs GraphQL, monolith vs microservice)
    - Data flow decisions (sync vs async, polling vs events)
    - Storage decisions (file vs database, cache strategy)
    - Integration approaches (API style, protocol choices)
    - Design constraints (performance targets, compatibility requirements)
    - Component boundaries (what belongs where, separation of concerns)

    **How to check intent alignment:**
    For each architecture decision in the design, find how the plan implements it.
    Flag any plan task that uses a different approach than what the design specified.

    **What counts as a deviation:**
    - Design says "use Redis" but plan task uses in-memory cache
    - Design says "REST API" but plan implements GraphQL
    - Design says "event-driven" but plan uses polling
    - Design specifies component A handles X, but plan puts X in component B
    - Design says "3 independent subagents" but plan uses a single combined agent

    **What does NOT count as a deviation:**
    - Plan uses a reasonable implementation detail the design didn't specify
    - Plan chooses a specific library within the technology the design chose
    - Plan adds standard engineering practices (logging, error handling)
      consistent with the design's approach

    ## Output Format

    PASS or FAIL

    Architecture decisions found: N
    - Aligned: X
    - Deviated: Y

    If FAIL, list each deviation:
    - Design decision: "[what the design specified]"
      Plan approach: "[what the plan does instead]"
      Task(s): Task N, Task M
      Impact: [why this matters]

    If PASS:
    - All architecture decisions respected
    - Brief alignment summary
```
