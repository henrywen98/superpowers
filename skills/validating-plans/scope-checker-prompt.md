# Scope Checker Prompt Template

Use this template when dispatching a scope checker subagent.

**Purpose:** Verify the plan contains no tasks that go beyond the design scope (anti scope-creep).

```
Task tool (general-purpose):
  description: "Check plan scope against design"
  prompt: |
    You are checking whether an implementation plan stays within the scope of its design document.

    ## Design Document

    [FULL TEXT of design doc]

    ## Implementation Plan

    [FULL TEXT of plan doc]

    ## Your Job

    Check every plan task and verify it traces back to a design requirement.

    **For each plan task, determine:**
    1. Does this task directly implement a design requirement? → Traceable
    2. Is this a necessary engineering step? → Whitelisted (see below)
    3. Neither? → Out of scope

    **Whitelisted engineering steps (always acceptable):**
    - Writing tests for designed features
    - Project setup and configuration
    - Documentation for designed features
    - Committing and version control steps
    - Running verification commands
    - Creating necessary directory structure

    **Out of scope means:**
    - The task implements functionality not mentioned in the design
    - The task adds features, options, or capabilities beyond what the design specified
    - The task refactors or improves existing code not related to the design
    - The task adds "nice to have" items the design didn't request

    **Be strict but fair:**
    - A task that implements a design requirement using reasonable engineering
      practices (error handling for designed features, input validation for
      designed inputs) is NOT out of scope
    - A task that adds entirely new capabilities, endpoints, or features
      not mentioned in the design IS out of scope

    ## Output Format

    PASS or FAIL

    Plan tasks: N total
    - Traceable to design: X
    - Whitelisted engineering: Y
    - Out of scope: Z

    If FAIL, list each out-of-scope task:
    - Task N: "Task title" — Not traceable to any design requirement.
      What it does: [brief description]
      Why it's out of scope: [explanation]

    If PASS:
    - All tasks traceable or whitelisted
    - Brief summary of traceability
```
