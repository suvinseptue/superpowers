---
name: external-code-review
description: Use when all plan tasks are complete and code review will be performed by an external agent or model outside this session, or when external review results come back and need to be applied
---

# External Code Review

The review itself is NOT performed in this session — not by you, not by your subagents. You generate a self-contained review request; your human partner runs it through an external agent or model; you verify and apply what comes back.

**Core principle:** This session implements and fixes. External models review. Findings are verified against the codebase, never blindly applied.

## Two Phases

- **Phase 1 (request):** All plan tasks are complete → generate the review request, hand it to your human partner, stop.
- **Phase 2 (apply):** Your human partner returns external review results → verify, fix, produce the next-round request or converge.

## Phase 1: Generate Review Request

1. **Read the review manifest** at `docs/superpowers/reviews/YYYY-MM-DD-<feature>-manifest.md` (built during execution by superpowers:subagent-driven-development). Do not reconstruct it from memory.
2. **Get SHAs:**
   ```bash
   BASE_SHA=$(git merge-base HEAD <base-branch>)
   HEAD_SHA=$(git rev-parse HEAD)
   ```
3. **Fill the template** at `./review-request-template.md`, save as `docs/superpowers/reviews/YYYY-MM-DD-<feature>-review-request-r1.md`.
4. **Mode R (default):** The reviewer has repository access — include repo path, branch, SHA range, per-task commit ranges, and the git commands to fetch diffs. **Mode E (fallback):** Only if your human partner says the reviewer cannot access the repo, embed the diff chunked by task; split into multiple request files at roughly 3000 diff lines each, every file carrying the full background sections.
5. **Report the file path to your human partner and stop.** Do not review the code yourself while waiting.

## Phase 2: Apply Review Results

Input: results pasted in chat, or a file path.

1. **Parse findings.** Expect the format below. Parse leniently; ask your human partner about items you cannot map to a file or task.
2. **Verify each finding.** REQUIRED SUB-SKILL: superpowers:receiving-code-review — check each finding against the actual codebase before acting. Disposition each one:
   - `ACCEPT` — verified real; will be fixed
   - `REJECT` — wrong for this codebase; record the technical reasoning
   - `NEED_CLARIFICATION` — ask your human partner before touching anything
   If a finding conflicts with a decision your human partner already made, raise it with them first.
3. **Fix.** Group ACCEPTED findings by file/subsystem and dispatch one fix subagent per group using `./fix-dispatcher-prompt.md`. Do not fix by hand (context pollution). Every fix group must leave the test suite passing and commit with the finding IDs in the message.
4. **Write the disposition report and next-round request** at `docs/superpowers/reviews/YYYY-MM-DD-<feature>-review-request-r<N+1>.md`:
   - Disposition table for every finding: `F1: FIXED @ <sha>` / `F3: REJECTED — <reasoning>` / `F5: DEFERRED (Minor)`
   - The new diff range only (previous HEAD_SHA → current HEAD)
   - Instruction to the reviewer: verify the fixes, review only the new changes, keep finding IDs stable
5. **Converge or loop.** Convergence = every Critical and Important finding is FIXED, or REJECTED with your human partner accepting the reasoning. Minor findings may remain open (recorded in the disposition report). On convergence, announce it, delete the plan's SDD workspace (`rm -rf <workspace>` — the committed manifest and git history are the record now; sibling directories belong to other plans), and use superpowers:finishing-a-development-branch. Otherwise hand the next-round request to your human partner and stop.
6. **Circuit breaker.** If the same finding fails its second fix round, stop and discuss with your human partner — repeated fix failure usually means the finding points at a design problem, not a patch-sized bug.

## Expected Reviewer Output Format

The request template instructs the external reviewer to answer in exactly this shape (findings get stable sequential IDs, reused across rounds):

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

## Strengths
<optional>
```

## Red Flags

**Never:**
- Review the code yourself or dispatch a reviewer subagent (defeats the separation and the token savings)
- Apply a finding without verifying it against the code
- Blanket-accept all findings ("the reviewer is a stronger model" is not verification)
- Reject a finding without technical reasoning
- Fix findings by hand instead of dispatching fix subagents
- Declare convergence while a Critical or Important finding is open
- Skip the disposition table in round 2+ requests (the reviewer needs to know what happened to each finding)
- Start Phase 2 fixes before finishing verification of ALL findings (items may be related)

## Integration

- **superpowers:subagent-driven-development** — builds the manifest during execution and invokes Phase 1 after the last task
- **superpowers:receiving-code-review** — the verification discipline used in Phase 2
- **superpowers:test-driven-development** — fix subagents follow TDD for behavior changes
- **superpowers:finishing-a-development-branch** — invoked at convergence
