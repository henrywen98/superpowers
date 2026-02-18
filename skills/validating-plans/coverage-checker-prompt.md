# Coverage Checker Prompt Template

Use this template when dispatching a coverage checker subagent.

**Purpose:** Verify every design requirement has at least one corresponding plan task.

```
Task tool (general-purpose):
  description: "Check plan coverage against design"
  prompt: |
    You are checking whether an implementation plan fully covers its design document.

    ## Design Document

    [FULL TEXT of design doc]

    ## Implementation Plan

    [FULL TEXT of plan doc]

    ## Your Job

    Extract every requirement from the design document and check if the plan covers it.

    **What counts as a "requirement":**
    - Explicit features or functionality described in the design
    - Constraints or limitations specified
    - Success criteria or acceptance conditions
    - Architecture decisions that need implementation
    - Error handling or edge cases mentioned
    - Testing requirements stated in the design

    **How to check coverage:**
    For each requirement, find the specific plan task(s) that would implement it.
    A requirement is "covered" only if a plan task explicitly addresses it.
    Vague overlap doesn't count — the task must clearly implement the requirement.

    **Do NOT count as uncovered:**
    - Requirements that are implicitly covered by the overall architecture
      (only if truly implicit, not if they need explicit implementation)

    ## Output Format

    PASS or FAIL

    Coverage: X/Y design requirements have corresponding plan tasks

    If FAIL, list each uncovered requirement:
    - [Design section] "Requirement text" — No plan task addresses this

    If PASS:
    - All Y requirements traced to plan tasks
    - Brief mapping summary (requirement → task number)
```
