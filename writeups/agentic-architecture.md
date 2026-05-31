# How I architect agentic AI systems

*Patterns I use to take LLM/agent systems from prototype to production in regulated, industrial domains — distilled from systems I've shipped (regulated-domain inspection, multi-agent coordination, growth pipelines, MCP tool servers).*

The hard part of agentic AI in production isn't the model — it's everything around it: bounding non-determinism, proving correctness, controlling cost, and keeping a human able to stop and audit the system. Below are the principles and patterns I design around, each tied to where I've actually used them.

---

## Principles

1. **Evaluation before deployment.** A model behavior isn't "done" until it has a metric and a baseline. On a production inspection platform I drove F1 from **0.17 → 0.80** purely through eval-driven iteration against a labelled set — the architecture made that loop cheap to run.
2. **Design for model uncertainty, not around it.** Models are wrong a predictable fraction of the time. The system must degrade safely: confidence thresholds, human-in-the-loop on low confidence, and *never* an irreversible action without a verification step.
3. **Governance is structural.** Guardrails, allowlists, and approval policies are part of the architecture diagram — not config bolted on later.
4. **Everything autonomous needs a kill-switch and an audit trail.** If I can't stop it and explain afterwards what it did, it doesn't ship.
5. **Cost is an architectural constraint.** Token spend and GPU-hours are designed for (routing, batching, quantization), not discovered on the invoice.

---

## Reference architecture (agent in a regulated workflow)

```
                ┌─────────────── Governance plane ───────────────┐
                │  policy / allowlist · approval policy · budget   │
                │  caps · audit log · kill-switch                  │
                └───────────────────────┬─────────────────────────┘
                                         │ (every step checked)
  event / job  ┌───────────┐   plan   ┌──┴────────┐  validated  ┌──────────┐
  ───────────► │ Orchestr. │ ───────► │  Agent /   │ ─ actions ► │  Tools / │
   (queue)     │  daemon   │ ◄─────── │  LLM step  │ ◄─ results  │  MCP srv │
               └─────┬─────┘  result  └─────┬──────┘             └──────────┘
                     │                       │ low-confidence / high-impact
                     │ metrics, traces       ▼
                     ▼                  ┌──────────┐
               observability           │  human   │  approve / reject
               (eval, cost, P/R/F1)    │  review  │
                                       └──────────┘
```

Three planes I keep separate: **execution** (orchestrator + agent steps + tools), **governance** (policy, budget, kill-switch, audit), and **observability** (eval, cost, traces). Decoupling them is what lets the system scale to more agents and more workflows without the controls eroding.

---

## Patterns

### 1. Separate interpretation from grounding for auditable answers
A general pattern for when an answer must be traceable to an authority, not just plausible. I split the work so that *what the model interprets* and *what the source record says* are decided in separate, independently-evaluable steps — the interpretation is constrained to be backed by a verified reference set rather than free-form recall. The payoff is that every output traces to a real source and each step can be measured on its own — essential in regulated domains where "the model thought so" is not an acceptable justification.

### 2. Orchestrator daemon + job queue + kill-switch
*Used in a production inspection platform and in JobSwap.* Long-running/expensive work goes through a queue, not a request thread. The orchestrator owns lifecycle (start/stop/retry), enforces budget caps, and exposes a single kill-switch. This is also where idempotency lives (idempotent Postgres upserts in JobSwap → safe retries over ~15,600 records).

### 3. Plan-ahead + re-planning for robustness under failure
*Used in NM i AI 2026 (multi-agent coordination).* A foreknowledge planner computes 500 rounds ahead; a state-injection middleware lets the system recover when **~30% of actions are dropped** by re-planning from observed state rather than assuming the plan held. The lesson generalizes: assume the environment will reject your actions, and make re-planning a first-class path.

### 4. Safe-mode tool design (plan-before-act)
*Used in my MCP tool servers.* Destructive or irreversible tools return a **plan/preview** first and require confirmation before executing. The agent proposes; the governance plane (or a human) disposes. Tools are designed minimal and typed so the model can't express an unsafe call.

### 5. Multi-provider routing & cost control
*Used across my agent ops (LLM gateway, openclaw-tune-codex-plus).* Route by task to the cheapest capable tier; pin subscription vs. per-token billing deliberately; keep a free fallback as a safety net, not a silent paid downgrade. Quantization (FP8/INT8) and vLLM serving cut inference cost — a production inspection workload runs at a few cents per processed unit because cost was designed in.

### 6. Orchestrating multiple coding agents in parallel
*Used in NM i AI.* I ran Claude, Codex and Gemini agents concurrently on a shared problem with clear task boundaries and a merge/verify step — the same coordination discipline (typed contracts, isolation, a verifier) that applies to any multi-agent production system.

---

## Mapping to industrial / regulated operations

- **Model uncertainty in operations** → confidence thresholds + human-in-the-loop on low confidence; honest, published error rates (I report false-positive rate, not just accuracy).
- **Governance-by-design / policy-as-code** → the governance plane above: allowlists, approval policy, budget caps and audit log evaluated on *every* step, expressible as declarative policy (OPA/Rego, Cedar) rather than scattered `if`-checks.
- **Event-driven scale** → queue/event-driven orchestration so workflows compose and scale horizontally without coupling.
- **Auditability** → structured outputs cite their source; traces + eval metrics are retained per run.

---

*Happy to walk through any of these systems in depth, including the parts not public. — Nikita Alexeev*
