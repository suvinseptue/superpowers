# Presenting the Design

Read this after the spec is written and self-reviewed, before you present anything.

**The spec and the presentation have different readers.** The spec is written to be implemented from — it carries the detail an implementer needs. The presentation is written to be *reviewed* — it is how a reviewer decides whether the approach is right at all. Same design, two audiences.

## Rules

- **Restate from the spec.** Everything you present must be traceable to what is actually in the spec file. If you catch yourself explaining something that isn't in there, the spec has a hole — fix the spec first, then present.
- **Pure product language.** No file names, class names, function names, table names, module paths, or framework names. Describe what people do and what the system records. "When the reviewer approves, the request moves to Pending Payment and the approval time is recorded" — not "the `ApprovalService` writes to `review_log`."
- **Do NOT write any of this into the spec.** These are the reviewer's questions, not the implementer's. Answering them inside the spec bloats the document the implementer has to work from.
- **Present it in the conversation**, in the four parts below, in order.
- **Scale each part to its actual complexity.** If a part genuinely does not apply (no UI change, no new fields), say so in one line rather than padding it.

## 1. Approach Summary

1. **The core idea in a few sentences.**
2. **What that core idea has to cover:** the key changes the approach makes, the whole surrounding flow it sits in, and how the new thing actually gets used. **Thread the entire flow with one concrete example** — walk it step by step, and at each step say *what the person does* and *what the system records*.
3. **The design of the key branch flows** — what happens when it doesn't go the straight-line way.

## 2. Design Rationale

- **What new concepts does this introduce?** For each one, what does it correspond to in the real world?
- **Which single design decision is the most central?** What other ways did you consider, and why did you choose this one?
- **How does this relate to concepts already in the system?** Is there any overlap or conflict with them?
- **What assumptions and constraints does it rest on?**
- **Are there business scenarios this approach cannot cover?**

## 3. Implementation Approach

- **The key points for carrying it out — list the five most important rules.**
- **Which existing modules and flows does the change touch?** List them.
- **Is the new logic implemented in one place, or spread across many?**

## 4. Change Inventory

1. **Page-level:** pages added, existing pages modified, pages removed. (Drawers and modals count as sub-pages.)
2. **Interaction-level:** what are the operating steps? What gets added, what gets removed?
3. **Fields:** fields added, existing fields modified, fields removed, entity structure changes.
4. **One concrete example** threading through the expected effect *after* the change.

## Then Ask for Approval

Point the user at the spec file path as well, and ask for approval to proceed. If they request changes, change the **spec**, re-run the spec self-review, and present again — the presentation is always a restatement of the current spec, never a document that drifts from it.

**Offer the quick prototype in the same message.** Whenever the design touches pages or interactions, add one line to the approval ask:

> "Want me to spin up a quick prototype so you can see the pages before approving?"

If the design has no UI surface at all, skip the offer. If they decline, proceed with approval as normal.

## Quick Prototype

Only on the user's yes. Dispatch **one subagent** to build it — a fast, execution-oriented model is the right fit here (Grok 4.6 High Fast, DeepSeek V4.1 Flash, or Sonnet 5, depending on what your harness offers). This is throwaway visualization, not implementation: do not build it yourself in the main session, and do not let it become the real thing.

**The subagent starts cold.** It does not inherit this conversation, so the prompt MUST carry what it needs: the spec file path, and the design rationale from part 2 of your presentation pasted in. A prototype built from the spec alone misses the reasoning behind the screens.

Prompt to send:

> Build a frontend-only prototype web from the context of our conversation, the design rationale below, and the full spec document at `<spec path>`.
>
> - You may use the frontend frameworks the project already depends on
> - The prototype should include as many of the fields as possible — field and content density is what drives the judgment about page layout
> - If this is not a new page, get the prototype's Base Page File from the project's route map (`route-map.md`)
> - Reuse the project's existing menu structure
>
> Design rationale: `<paste part 2 of the presentation>`
>
> When you are done, report the path and how to run it.

Relay the path and run command to the user, then wait for their approval as normal. **Feedback on the prototype changes the spec**, not the prototype — the prototype is a picture of the design, not a source of truth.

This is a different thing from the Visual Companion: the companion answers a design question mid-brainstorm, while the prototype shows a finished design before approval.
