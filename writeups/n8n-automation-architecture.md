← [Portfolio](../README.md)

# n8n automation architecture — patterns for reliable AI workflows

*Patterns I reuse across 200+ production n8n workflows — lead scraping & enrichment, cold-email outreach, content generation, video pipelines and booking chatbots. The theme: make automation survive the real world, where APIs rate-limit, sources break and the same trigger fires twice.*

> Generic patterns only — no client data, credentials or workflow internals.

## The problem

A workflow that works once in the editor is easy. A workflow that runs **every hour for months**, talks to half a dozen third-party APIs, processes batches of records and never double-charges, double-emails or double-posts is a different thing. The hard parts are never the happy path:

- third-party APIs **rate-limit** and time out;
- a trigger **fires twice** (retries, webhook redelivery) and you must not act twice;
- one record in a batch is malformed and must not **poison the whole run**;
- a source **changes shape** and the workflow must degrade, not crash;
- something irreversible (an email, a charge, a post) needs a **gate**.

## A reliable workflow, shaped

```
  trigger (cron | webhook)
        │
        ▼
  ┌──────────────┐   dedupe key
  │ Idempotency   │ ──────────────►  seen?  ──► yes ──► skip (no-op)
  │ check (state) │                            no
  └──────┬───────┘                             │
         ▼                                      ▼
  ┌──────────────┐  batch + throttle   ┌────────────────┐
  │ Validate &    │ ──────────────────►│ Per-item work  │  retry w/ backoff
  │ normalise     │   (rate-limit safe) │ (API / LLM)    │  on transient errors
  └──────────────┘                     └──────┬─────────┘
         ▲ malformed item                      │ ok / failed-isolated
         │ routed to dead-letter               ▼
         └──────────────────────────────  ┌────────────────┐
                                           │ Commit to state│  mark done
                                           │ of record      │  (idempotent)
                                           └──────┬─────────┘
                                                  │ irreversible action?
                                                  ▼
                                           ┌────────────────┐
                                           │ Human gate      │ (when it matters)
                                           └────────────────┘
```

## The patterns

- **Idempotency via a state of record.** Every item gets a stable dedupe key persisted in a database (Supabase/Postgres). The first node checks "have I done this already?" and no-ops if so. Retries and double-fires become harmless. This single pattern is what makes a flaky pipeline safe to re-run.
- **Retry with backoff on transient errors only.** Distinguish *transient* (429, timeout, 5xx) from *permanent* (400, auth) failures. Retry the first with exponential backoff; fail the second loudly. Blind retries on a permanent error just waste quota and hide the bug.
- **Batch + throttle to respect rate limits.** Process in bounded batches with deliberate spacing so a downstream API is never hammered. Throughput is shaped to the *slowest* dependency, not the fastest.
- **Source isolation + dead-lettering.** Each external source / each item is handled independently; a malformed record is routed aside (dead-letter) for inspection instead of aborting the batch. One bad input never sinks the run.
- **Webhook routing over polling where possible.** Event-driven triggers (webhooks) instead of tight polling loops — cheaper, lower-latency, and they don't burn API quota checking "anything new yet?".
- **Scheduled triggers for the rest.** Cron-style triggers for genuinely periodic work, with the idempotency layer underneath so an overlapping or duplicated run is a no-op.
- **Human-in-the-loop gates on irreversible actions.** Anything that reaches the outside world unrecoverably — a cold email, a published post, a booking — passes a human (or a hard rule) before commit. Autonomy for the cheap reversible work; a gate on the expensive irreversible step.

## Where these show up

- **Lead scraping & enrichment** — ingest from public business sources, normalise into one schema, enrich, dedupe against state. Source isolation + idempotency carry it.
- **Cold-email outreach** — batch + throttle + per-recipient dedupe so no one is contacted twice; the send is the gated irreversible action.
- **Content & video pipelines** — multi-step generation (LLMs, media APIs) with retries and a human approval gate before publish.
- **Booking chatbots** — webhook-driven, integrated with a calendar; idempotent so a redelivered message never double-books.

## Trade-offs

- **n8n vs. application code.** Visual nodes give observability, per-node retry and fast iteration, at the cost of fine-grained concurrency control and static typing. For integration-heavy, human-maintained automation this is the right trade; for a high-QPS hot path it isn't.
- **State of record adds a dependency.** Idempotency needs durable state — one more thing that can fail. But the alternative (best-effort, hope-it-doesn't-double-fire) is not acceptable once money, email or posts are involved.
- **Human gates add latency.** Deliberately. The latency is the safety margin for irreversible actions.

## What generalizes

**The reliability of an automation is decided by how it behaves when something goes wrong, not when everything works.** Idempotency, transient-vs-permanent retry, batch throttling, dead-lettering and a gate on irreversible actions are the same primitives whether the orchestrator is n8n, a queue worker or an agent loop. I design the failure behaviour first; the happy path takes care of itself.

---

*Happy to walk through any of these patterns against a concrete workflow. — Nikita Alexeev*
