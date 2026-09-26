# Review Request Template

Use this template in Phase 1 of external-code-review. Fill every placeholder — the document must be self-contained, because the external reviewer has none of this session's context.

Save as: `docs/superpowers/reviews/YYYY-MM-DD-<feature>-review-request-r<N>.md`

**Placeholders:**
- `{FEATURE}` — feature name
- `{ROUND}` — review round number (1, 2, ...)
- `{BACKGROUND}` — what the project is, what this change does, tech stack
- `{REQUIREMENTS}` — spec summary or full text, plus plan file path
- `{REPO_PATH}` / `{BRANCH}` — where the code lives
- `{BASE_SHA}` / `{HEAD_SHA}` — full review range
- `{TASK_BREAKDOWN}` — per-task sections copied from the review manifest
- `{DIFF_ACCESS}` — Mode R git commands (default) or Mode E embedded diff

````markdown
# Code Review Request: {FEATURE} (Round {ROUND})

You are a senior code reviewer with expertise in software architecture, design
patterns, and best practices. Review this implementation independently.

**Do not trust the implementer's reports.** They may be incomplete, inaccurate,
or optimistic. Verify everything by reading the actual code.

## Project Background

{BACKGROUND}

## Requirements

{REQUIREMENTS}

## Scope of Changes

- Repository: {REPO_PATH}
- Branch: {BRANCH}
- Full range: {BASE_SHA}..{HEAD_SHA}

### Task Breakdown

{TASK_BREAKDOWN — for each task, from the manifest:}

#### Task N: <name>
- Commits: <range>
- Files: <files changed>
- Requirements: <full task text from the plan>
- Implementer's claims: <report summary — verify, don't trust>

## Getting the Code

{DIFF_ACCESS — Mode R (default):}

You have repository access. Use:

```bash
git -C {REPO_PATH} log --oneline {BASE_SHA}..{HEAD_SHA}
git -C {REPO_PATH} diff --stat {BASE_SHA}..{HEAD_SHA}
git -C {REPO_PATH} diff {BASE_SHA}..{HEAD_SHA}
# Per-task diffs: use the commit ranges in the Task Breakdown above
```

{Mode E (only when the reviewer has no repo access): replace this section with
the embedded diff, chunked per task. Split into multiple request files at
roughly 3000 diff lines; repeat the background sections in every file.}

## What to Review

1. **Spec compliance (per task):** Compare code to each task's requirements
   line by line. Missing requirements? Extra, unrequested work? Requirements
   interpreted differently than written?
2. **Code quality:** Separation of concerns, error handling, type safety,
   DRY without premature abstraction, edge cases.
3. **Architecture:** Sound design decisions, clean integration with
   surrounding code, one clear responsibility per file.
4. **Testing:** Tests verify real behavior (not mocks), edge cases covered,
   all tests passing.
5. **Cross-task consistency:** Naming, types, and signatures consistent
   across tasks (e.g. Task 3 calling it `clearLayers()` while Task 7 calls
   `clearFullLayers()`). Per-task review cannot see this — you can.
6. **Production readiness:** Migration strategy for schema changes, backward
   compatibility, documentation, obvious bugs.

## Calibration

Categorize by actual severity — not everything is Critical. Be specific
(file:line). Acknowledge what was done well. If you find issues with the plan
itself rather than the implementation, say so. Give a clear verdict.

## Output Format (MANDATORY — results are parsed programmatically)

Respond in exactly this structure:

```markdown
## Verdict
READY | READY_WITH_FIXES | NOT_READY

## Findings

### F1
- Severity: Critical | Important | Minor
- Task: <task number, or "cross-cutting">
- Location: <file:line>
- Issue: <what is wrong>
- Why: <why it matters>
- Fix: <suggested fix, optional>

### F2
...

## Strengths
<what was done well — optional but valued>
```

One block per finding, sequential IDs. Severity meanings: Critical = bugs,
security issues, data loss, broken functionality (must fix). Important =
architecture problems, missing requirements, poor error handling, test gaps
(should fix). Minor = style, optimization, polish (nice to have).
````

## Round 2+ Additions

For incremental rounds, insert after "Scope of Changes":

````markdown
## Previous Round Dispositions

| Finding | Disposition |
|---------|-------------|
| F1 | FIXED @ <sha> |
| F2 | FIXED @ <sha> |
| F3 | REJECTED — <technical reasoning> |
| F5 | DEFERRED (Minor) |

## This Round

Review range: {PREV_HEAD_SHA}..{HEAD_SHA} (fixes only).

1. Verify each FIXED finding is actually resolved — reuse its ID in your response.
2. Review only the new changes for regressions or new issues — continue the
   ID sequence for new findings (do not restart at F1).
3. If you disagree with a REJECTED disposition, say why under its original ID.
````
