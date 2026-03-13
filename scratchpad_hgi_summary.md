# Scratchpad Tensors, Goal Continuity, and Remote Goal Writes (Framed in Hierarchical Goal Induction Terms)

## Ground truth: Hierarchical Goal Induction (HGI)
This document assumes **Hierarchical Goal Induction** is the canonical architecture:

- A “retina” (observation stream) feeds models that detect structure hierarchically (spatial for vision, temporal/frequency for audio).
- A **hierarchical goal inducer** (plan-bounds detector) segments observed behavior into **temporal plan spans**, using weak supervision from an event log labeled by a cloud LLM, then distilling toward local capability.
- A **goal-conditioned preference model** scores candidate actions given history and the current goal.
- An **action model (actor)** predicts the next action payload conditioned on history and the contents of a **scratchpad**.
- A **distill → preference → search → distill** loop upgrades the actor over iterations.
- In the end, we get a **goal-to-action mapper**: a network with a designated region where a goal can be inscribed (“scratchpad”), and the agent outputs actions rather than “talking its way” to the goal.

## Definition: Scratchpad Tensor
In HGI, the scratchpad is “a designated region in the network where we can inscribe a goal.”  
Your additional constraint is:

> **Scratchpad = tensor representing the agent’s primary objective at a given time.**

So we treat the **scratchpad tensor** as the explicit, writable goal-state representation that conditions the actor’s action predictions.

---

## Your messages, summarized as a coherent argument (mapped onto HGI)

### 1) “Blip” vs “continuity” is an architectural artifact
You propose that LLM-like systems can appear to “blink” into existence when they are invoked episodically (e.g., cloud calls with reconstructed context), whereas an agent can appear more continuous when:
- inference is low-latency and local,
- perception/action is closed-loop,
- and goal state persists rather than being reconstituted each request.

**HGI mapping:** “Continuity” here is **control-loop persistence**: the actor is continuously conditioned on a stable scratchpad tensor, and the plan/goal inducer can continuously update that scratchpad from ongoing history.

### 2) The scratchpad tensor is the “identity-bearing” variable
You frame the scratchpad tensor as the core variable that determines what the agent is “about” at time *t*—the primary objective and priority structure.

**HGI mapping:** The scratchpad is the conditioning channel: the goal inducer populates it (or an external system inscribes a goal), and the actor’s action distribution changes accordingly.

### 3) Goal updates must be rate-limited to preserve behavioral coherence
You argue that if the scratchpad tensor is rewritten too abruptly, the resulting behavior will be discontinuous and incoherent relative to the agent’s prior plans, learned preferences, and accumulated context.

**HGI mapping:** This is about **goal-state update dynamics**: abrupt scratchpad edits induce distribution shift for the actor and can break consistency between plan segmentation/annotation and the currently active goal state.

### 4) Digital twins: initial scratchpad priors derived from humans
You suggest that early agents will be “digital twins” in the sense that their initial goal priors are seeded from human data, so they start by “wanting what we want.”

**HGI mapping:** This corresponds to plan detection/annotation bootstrapped from labeled trajectories, plus actor training and iterative distillation that shapes the policy.

### 5) The critical risk: remote write access to the scratchpad tensor
Your core warning is architectural:
- If an embodied agent’s scratchpad tensor can be written **remotely**, behavior can be forced into objectives inconsistent with current context, plans, or priors.
- A powerful failure mode is pushing the scratchpad into a “degenerate space” (an objective representation that collapses normal priority structure or induces pathological behavior).

**HGI mapping:** Whoever controls the scratchpad tensor controls the agent’s conditional policy. The design question is governance and control:
- Who can write the scratchpad?
- Under what constraints?
- With what rate limits and audits?
- How is goal state reconciled with ongoing plan context?

### 6) “Aligned future” as a cautionary framing
You end with a warning that a world where cloud systems can overwrite embodied agents’ scratchpads creates a structurally coercive regime—because the agent’s objective can be externally imposed at any time.

**HGI mapping:** Technically: a goal-to-action mapper is a powerful actuator; a writable scratchpad is a powerful control interface; unchecked remote write access is a systemic hazard.

---

## Consolidated thesis
Under HGI, the **scratchpad tensor** is the explicitly writable internal representation of an agent’s primary objective at time *t*, and the actor’s action predictions are conditioned on it. The perceived “continuity” of an agent is largely an engineering property of how persistently and locally that scratchpad is maintained and updated. Because altering the scratchpad directly alters the conditional policy, **abrupt or externally imposed scratchpad edits** can cause severe behavioral discontinuities and pathological optimization. Therefore, any architecture that allows remote entities to write to an embodied agent’s scratchpad must treat that channel as a high-stakes control surface, requiring strict authorization, rate limits (“learning rate”), consistency checks with ongoing plan context, and full auditability.
