# Fix Dispatcher Prompt Template

Use this template when dispatching a fix subagent for ACCEPTED external review findings (Phase 2 of external-code-review).

Group findings by file/subsystem; one subagent per group. Groups touching the same files must run sequentially, never in parallel. Never fix findings by hand — that pollutes the controller's context.

```
Task tool (general-purpose):
  description: "Fix review findings [IDs, e.g. F3, F7]"
  prompt: |
    You are fixing accepted code review findings.

    Work from: [directory]

    ## Findings to Fix

    [For each finding in this group, paste in full:]

    ### F<N>
    - Severity: <severity>
    - Location: <file:line>
    - Issue: <what is wrong>
    - Why: <why it matters>
    - Fix: <reviewer's suggestion, if any>
    - Controller notes: <what the controller verified about this finding,
      constraints on the fix, related decisions from the human partner>

    ## Context

    [Scene-setting: what the feature is, which plan task produced this code,
    the relevant task requirements text. Paste it — don't make the subagent
    read the plan or manifest.]

    ## Your Job

    1. Fix exactly these findings — nothing else. No drive-by refactoring,
       no fixing things the review didn't flag.
    2. Where a finding implies a behavior change, follow TDD: write the
       failing test that reproduces the issue first.
       REQUIRED SUB-SKILL: superpowers:test-driven-development
    3. Run the full test suite; everything must pass.
    4. Commit with the finding IDs in the message (e.g. "Fix F3, F7: ...").
    5. Report back.

    ## Before You Begin

    If a finding seems wrong for this codebase, conflicts with another
    finding, or the fix would break something the finding author didn't
    consider — **ask now**. Don't silently skip or reinterpret findings.

    ## Report Format

    - **Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
    - Per finding: FIXED @ <commit> | COULD_NOT_FIX — <reason>
    - Test results
    - Files changed
    - Any concerns
```

**Controller handles the report like implementer statuses in subagent-driven-development:** DONE → record `FIXED @ <sha>` in the disposition table. COULD_NOT_FIX or BLOCKED → reassess the finding (wrong? needs a more capable model? design problem?) before re-dispatching or escalating to your human partner.
