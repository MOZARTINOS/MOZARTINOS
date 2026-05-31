← [Portfolio](../README.md)

# JobSwap — AI-driven growth & data pipeline

*Main project at Visam AS — the production growth pipeline behind a sustainability-driven swap product.*

**The problem:** cold-start growth for a new marketplace. With no existing audience, the system has to *find* the right businesses, *qualify* them before spending a cent on outreach, and *reach* them at scale — across multiple languages and time zones, without tripping deliverability or compliance limits. That's a data problem and an LLM problem wired into a sending pipeline that has to stay safe under repeated runs.

---

## Architecture

```
  ┌──────────────────────┐   raw places      ┌──────────────────┐
  │  Google Maps Places  │ ────────────────► │  Dedup + lead    │
  │  scrapers            │                    │  scoring         │
  │  (multi-location)    │                    │  (qualify before │
  └──────────────────────┘                    │   spend)         │
                                              └────────┬─────────┘
  ┌──────────────────────┐   enriched orgs            │ scored leads
  │  OpenVC investor     │ ───────────────────────────┤
  │  enrichment          │                            ▼
  └──────────────────────┘            ┌───────────────────────────────┐
                                       │  Idempotent Postgres upsert   │
                                       │  (Supabase · 7 tables)        │
                                       │  safe re-runs, no duplicates  │
                                       └───────────────┬───────────────┘
                                                       │ qualified records
                                                       ▼
                                       ┌───────────────────────────────┐
                                       │  LLM personalization (OpenAI) │
                                       │  5 languages · prompts tuned   │
                                       │  against reply rate            │
                                       └───────────────┬───────────────┘
                                                       │ ready-to-send copy
                                                       ▼
            ┌──────────────────────────────────────────────────────────┐
            │  Scheduled sending  (n8n orchestration + Brevo)           │
            │  timezone-aware send windows · rate limiting              │
            └───────────────────────────┬──────────────────────────────┘
                                         │ delivery events
                                         ▼
            ┌──────────────────────────────────────────────────────────┐
            │  Bounce / unsubscribe feedback loop  ──► suppression list │
            │  (feeds back into scoring + send eligibility)             │
            └──────────────────────────────────────────────────────────┘
```

Two ingestion sources feed one qualified store; one personalization stage feeds a controlled sending stage; deliverability events loop back to keep the list clean. The store is the durable boundary — everything upstream is re-runnable, everything downstream reads qualified state.

---

## Key decisions

- **Idempotent upserts, so re-runs are safe.** Scrapers and enrichment jobs are expected to run repeatedly and overlap. Writes are keyed and idempotent, so a re-run over the corpus (~15,600 records) updates rather than duplicates — the same retry-safety discipline I apply to any long-running pipeline.
- **Lead scoring before spend.** Records are deduped and scored *before* they reach personalization or sending. Outreach cost and reputation are spent only on qualified leads, not on whatever the scraper happened to return.
- **Reply-rate-driven prompt iteration.** Personalization prompts are tuned against an outcome metric (reply rate), not vibes — the same eval-before-deploy loop I run on my model work, applied to copy.
- **Timezone-aware send windows.** Sending is scheduled per recipient's local time rather than blasted, which protects both engagement and sender reputation.
- **Deliverability and compliance handled structurally.** Bounce and unsubscribe handling, suppression, and rate limiting are part of the pipeline — a recipient who bounces or opts out is removed from eligibility, not handled after the fact.

---

## Trade-offs

- **Scraping fragility vs. coverage.** Source-page changes break scrapers; the multi-location, multi-source design buys coverage but carries ongoing maintenance. The idempotent store absorbs the churn — a failed or partial run is just re-run.
- **Personalization quality vs. cost.** Per-record LLM generation in five languages is the expensive stage, which is exactly why scoring gates it: better-qualified inputs mean spend goes where it can convert.
- **Compliance/deliverability as a hard constraint.** Send windows, rate limits and suppression cap raw throughput on purpose. Slower-but-clean beats fast-but-blocklisted — sender reputation is the asset that actually compounds.

---

## Results / scale

- **~15,600 B2B leads** collected, scored and stored across **7 Supabase tables**.
- **4,073 investors** enriched from OpenVC into the same qualified store.
- **5-language LLM personalization** in production, with prompts iterated against reply rate.
- Production sending via **n8n + Brevo** with timezone-aware windows, rate limiting, and a bounce/unsubscribe feedback loop.

---

*Data and code are private; happy to walk through the architecture. — Nikita Alexeev*
