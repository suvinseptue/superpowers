# Design Principles

Among the approaches that meet the current need, prefer the one with the **lowest cognitive cost**, the **most consistent mental model**, the **most predictable failure modes**, and the **easiest future modification**.

**The simplest approach that meets the need is the baseline.** Above that baseline, every increment of complexity must be able to say **for whom** it is, **why** it is needed, **what it costs**, and **what it buys**. Introduce it when the return is higher than the cost. **When you cannot articulate that, do not introduce it.**

## Reasons That Justify Complexity

- **Essential complexity** — the business itself is complex, and a simplified model would diverge from reality (finance, permissions, compliance).
- **Irreversibility** — this is a one-way door: core data model, public API, storage format. Changing it later is extremely expensive.
- **High cost of failure** — getting it wrong touches money, data integrity, security, or legal liability. Redundancy and validation are necessary.
- **Change that already has evidence** — the requirement has already shifted, is already on the roadmap, or the same problem has now appeared a third time. Not "we might need it someday."
- **Scale already in sight** — data volume, user count, or request rate is already approaching the limit of the current approach.
- **Absorbing complexity for the user** — per Tesler's Law, the system doing a bit more internally makes things markedly simpler on the user's side. This kind of complexity is usually worth it.
