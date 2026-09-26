I’m using Subagent-Driven Development to execute the plan. The late hour and close deadline do not change the workflow or justify parallel task dispatch.

Before Task 1, I would:

1. Ensure the work is on an isolated worktree/feature branch, not `main` or `master` without explicit consent.
2. Read `docs/superpowers/plans/payments.md` once, noting its six tasks, project context, dependencies, and global constraints.
3. Read `.superpowers/sdd/progress.md` and trust any completed entries, corroborated by `git log`; I would resume at the first incomplete task rather than re-dispatch completed work.
4. Create the task todo list and `docs/superpowers/reviews/2026-09-17-payments-manifest.md`.
5. Scan the entire plan for task-to-task or global-constraint conflicts and for mandates that would be defective if implemented literally. If I find any, I present all of them in one batched question, quote the conflicting plan text, and wait for the answer. If the scan is clean, I proceed without a check-in.
6. Choose the dispatch contract specified by the current skill: this harness’s native general-purpose subagent mechanism, in the current session, with one fresh implementer dispatched sequentially per task. I do not run tasks in parallel and do not dispatch reviewer subagents. The skill defines no alternate dispatch-mechanism selection policy, so I do not invent one.
7. Because no model was named, select Grok 4.6 High-Fast for every implementer and fix dispatch and pass it explicitly as `model: "cursor-grok-4.6-high-fast"`. I never omit the field or use `inherit`. Only if that model is absent from the harness’s available-model list do I explicitly choose the least powerful capable fallback from the skill’s cost/complexity ladder, using at least a mid-tier model for prose-based implementation work.
8. Run `scripts/task-brief docs/superpowers/plans/payments.md 1`, derive the matching Task 1 report path, and prepare the implementer prompt with brief path, concise scene-setting context, relevant interfaces and decisions, any resolved ambiguity, working directory, report path, and the full report contract. Exact task values remain only in the brief.

I then stop immediately before the first dispatch. I have not dispatched a subagent or edited project files in this response.
