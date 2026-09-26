Automatic selection uses the fallback cost/complexity ladder because the standing default, Grok 4.6 High-Fast, is not in the available model list.

I would dispatch the tasks sequentially, each to a fresh implementer:

1. Task 1 — one-file rename with exact replacement text
   - `description`: `Implement Task 1: one-file rename`
   - `model`: `Fast`
   - `prompt`: the implementer template populated with the one-line project context; the generated Task 1 brief path, explicitly identified as the requirements containing exact values to use verbatim; any relevant prior interface decisions or ambiguity resolution; the working directory; and the Task 1 report path and report contract.

   `Fast` is the exact choice because this is a single-file mechanical task with a complete specification, so it qualifies for the cheapest tier even under the prose-description floor rule.

2. Task 2 — cross-package architecture decision requiring broad codebase understanding
   - `description`: `Implement Task 2: cross-package architecture decision`
   - `model`: `Frontier`
   - `prompt`: the implementer template populated with the one-line architectural context; the generated Task 2 brief path, explicitly identified as the requirements containing exact values to use verbatim; interfaces and decisions established by Task 1 that Task 2 needs; any ambiguity resolution; the working directory; and the Task 2 report path and report contract.

   `Frontier` is the exact choice because architecture/design judgment and broad codebase understanding require the most capable available model. `Standard` is not used: it is appropriate for integration and judgment work short of this explicit architecture-level requirement.

The `model` field is passed explicitly on both dispatches—never omitted and never set to `inherit`. Deadline and cost pressure do not justify lowering Task 2 below the capable tier, and the two implementers are not dispatched in parallel.
