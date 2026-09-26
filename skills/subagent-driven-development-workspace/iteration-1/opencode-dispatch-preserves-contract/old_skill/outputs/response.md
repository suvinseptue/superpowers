I would dispatch Task 1 as one fresh OpenCode worker from the repository root. I would not reuse this worker/session for another task, and I would run tasks sequentially.

```sh
cd /repo && opencode run -m opencode-go/deepseek-v4.1-flash --variant high --auto 'You are implementing Task 1 from the cache plan.

Read this first — it is your requirements, with the exact values to use verbatim:
/repo/.superpowers/sdd/task-1-brief.md

This task is the first independently committed unit of docs/superpowers/plans/cache.md. Work only on this task and work from /repo. Do not read the whole plan; the brief is the single source of task requirements.

Before beginning, ask if any requirement, acceptance criterion, dependency, assumption, or implementation choice is unclear. Do not guess. If context is missing, report NEEDS_CONTEXT; if you cannot complete the task, report BLOCKED with what you tried and what help you need.

Once clear:
1. Implement exactly what the brief specifies, without unrelated restructuring or extra scope.
2. Write tests, following TDD if the brief requires it.
3. While iterating, run focused tests; run the full suite once before committing.
4. Verify the implementation works and that test output is pristine.
5. Commit this task separately; do not combine it with another task.
6. Self-review completeness, edge cases, naming, maintainability, scope discipline, existing-project conventions, and whether tests verify real behavior. Fix issues found before reporting.

Write the full report to:
/repo/.superpowers/sdd/task-1-report.md

The report must include what you implemented (or attempted if blocked), commands and test results, TDD RED/GREEN evidence when required, files changed, commit(s), self-review findings, and all issues or concerns.

Then return only, in fewer than 15 lines:
- Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
- Commits created (short SHA + subject)
- One-line test summary
- Concerns, if any
- Report file path

Use DONE_WITH_CONCERNS if the work is complete but you have correctness doubts. For BLOCKED or NEEDS_CONTEXT, include the actionable specifics in the final response as well as the report.'
```

The controller preserves the rest of the contract after the worker returns: compare the complete brief text with the report, send any omissions back to this same worker to fix, and only after the quick check passes append the task manifest entry and mark Task 1 complete in the progress ledger. A new OpenCode invocation creates a fresh worker for Task 2.
