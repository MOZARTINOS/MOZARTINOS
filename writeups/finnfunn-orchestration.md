← [Portfolio](../README.md)

# FinnFunn — AI-agent-driven social-media automation

*A production social-media system that runs daily on its own: AI agents scrape, score, generate and schedule content, with a human approving the calls that matter before anything goes live. n8n orchestrates the backend.*

> Code, scoring internals, credentials and workflow IDs are private. This write-up covers the **architecture and the decisions**, not the proprietary content logic.

## The problem

Running a social presence "properly" is a daily grind: find what's worth posting, turn it into on-brand content, post it at the right time, don't repeat yourself, and never let an LLM publish something embarrassing unattended. I wanted that to run **every day without me babysitting it** — but with a hard guarantee that a human signs off before anything reaches an audience.

The constraints that shaped the design:

- **Many small, fallible stages** (scraping breaks, models hallucinate, APIs rate-limit) — one bad stage must not take down the run.
- **Brand safety is non-negotiable** — nothing auto-publishes.
- **It must be observable** — when a run misbehaves at 7am, I need to see exactly which node failed and re-run from there.

## Architecture

```
   scheduled trigger (cron)
          │
          ▼
   ┌────────────────┐   raw items   ┌───────────────┐
   │ Scrape / ingest │ ───────────► │ Score & rank   │  LLM scoring
   │ (remote worker) │              │ (shortlist)    │
   └────────────────┘              └──────┬─────────┘
                                          │ shortlisted
                                          ▼
                                   ┌───────────────┐
                                   │ Generate       │  Claude / Gemini / GPT
                                   │ content        │  (per-stage model routing)
                                   │ + Deepgram for │
                                   │   reels/audio  │
                                   └──────┬─────────┘
                                          │ drafts
                                          ▼
                                   ┌───────────────┐   approve · reject · edit
                                   │ Human-in-the-  │ ◄────────────────────────  Telegram
                                   │ loop approval  │
                                   └──────┬─────────┘
                                          │ approved only
                                          ▼
                                   ┌───────────────┐
                                   │ Publish &      │  Facebook · Reels
                                   │ schedule       │
                                   └──────┬─────────┘
                                          │ state + dedupe keys
                                          ▼
                                    Supabase (state of record)
```

A scheduled trigger kicks the pipeline; each stage hands a typed payload to the next; Supabase holds the **state of record** (what's been seen, drafted, approved, posted) so the pipeline is idempotent across re-runs.

## Key decisions

- **n8n as the orchestration backbone, not bespoke code.** Each stage is a node I can inspect, retry and re-run in isolation. When a 7am run fails, I see the exact node, its input and its error — and resume from there. The observability and iteration speed outweigh the loss of fine-grained concurrency control.
- **Per-stage model routing (Claude / Gemini / GPT).** Different stages have different cost/quality needs — ranking is cheap and high-volume; final copy wants the strongest model. Routing per stage instead of one model everywhere keeps cost down without dropping quality where it shows.
- **Human-in-the-loop via Telegram — the safety gate.** Generated drafts are pushed to Telegram with **approve / reject / edit** controls. **Nothing publishes without an explicit human action.** This is the single most important design choice: it makes an autonomous content pipeline safe to leave running.
- **Idempotency through a state of record.** Every item carries a stable dedupe key in Supabase. Re-running a failed stage never double-posts and never re-drafts what's already approved — the pipeline can crash and resume without side effects.
- **Isolated, replaceable scrapers on a remote worker.** Ingestion runs on a dedicated host; each source is independent, so one broken site degrades gracefully instead of failing the whole run.

## Trade-offs

- **n8n vs. hand-written services.** I trade fine-grained concurrency and type-safety for visual observability, per-node retry and fast iteration. For a daily content pipeline maintained by one person, that trade is clearly worth it; for a high-QPS service it would not be.
- **HITL latency vs. brand safety.** The approval gate means content isn't instant — it waits on a human. That's the point: for a brand-facing channel, a few minutes of approval latency is cheaper than one bad autonomous post.
- **Multi-model routing vs. simplicity.** Three providers means three failure modes and three sets of quirks. The cost/quality win justifies the extra surface, but it's real operational complexity.

## What generalizes

**A human-in-the-loop approval gate plus an idempotent state of record turns a flaky pile of AI stages into a system you can trust to run unattended.** The agents do the volume; the human owns the irreversible step; the state of record makes failure safe. That same shape — *autonomy for the cheap reversible work, a gate on the expensive irreversible action, idempotency everywhere* — is how I build any agent system that touches the outside world.

---

*Happy to walk through the orchestration, the routing logic or the approval flow in more detail. — Nikita Alexeev*
