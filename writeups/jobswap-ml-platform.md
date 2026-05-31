← [Portfolio](../README.md)

# JobSwap — applied ML platform for commute matching

*Visam AS · an AI platform that cuts commutes by matching "mirror twins" — people who live where others work, and vice versa — then chaining those matches into job- and home-swaps. I built the ML system: 21 trained models (neural networks, learning-to-rank and a combinatorial optimiser), all ONNX-exported for production, plus a v2 demo app. The R&D project is **approved under SkatteFUNN, Norway's national R&D scheme administered by the Research Council of Norway (Norges forskningsråd).***

**Live:** main site [myjobswap.com](https://myjobswap.com) · my **v2 build** [jobswap.netlify.app](https://jobswap.netlify.app) · *Exchange Jobs, Shorten Commutes, Save the Planet* — launching on Kickstarter.

> Code and data are private (real user/employer data, GDPR). This write-up covers the **model architecture and the engineering decisions**, sanitised.

## The problem

Reducing commutes isn't a search problem, it's a **matching** problem. The ideal isn't "find a closer job" — it's "find the person whose home is next to your work and whose work is next to your home", then realise the swap. At scale that becomes finding **cycles and chains** in a graph of candidate swaps, under many soft constraints (skills, salary, housing, working hours, seniority, sustainability) — and it has to feel instant in a consumer app.

## Architecture

```
  onboarding  ── Gemini 2.5 Flash + function calling ──►  structured profile
       │
       ▼
  multilingual embeddings (mpnet, 768-d)  ──►  vector store (ScaNN / pgvector)
       │ candidate generation
       ▼
  learning-to-rank  ── XGBoost LambdaMART, 13 features ──►  ranked candidates
       │  (geo · skill · salary · housing · commute · hours · seniority · …)
       ▼
  chain solver  ── ILP column generation (kidney-exchange algorithm) ──►  swap cycles/chains
       │
       ▼
  scoring  ── Valhalla routing + spatio-temporal traffic GNN + CO₂ ──►  sustainable matches
       │
       ▼
  21 models → ONNX  ──►  Python / .NET backend  ──►  React + Mapbox demo app (v2)
```

## Key decisions

- **Mirror-twin matching as a kidney-exchange problem.** The standout choice: swap-matching is structurally the same as **kidney paired-exchange** — find cycles/chains in a compatibility graph that maximise realised matches. I solved it with **ILP column generation** (the kidney-exchange formulation) rather than a generic OR-Tools model, because it scales to large candidate pools and naturally expresses chains. Borrowing a *solved* algorithm from an adjacent domain beat inventing one.
- **Learning-to-rank, not classification.** Candidate quality is ranking, so the ranker is **XGBoost LambdaMART** over 13 engineered features (geo, skill, salary, housing, commute, hours, seniority, education, preference-similarity + interactions) — it optimises the order, which is what the chain solver consumes.
- **Multilingual embeddings for a multilingual workforce.** `paraphrase-multilingual-mpnet-base-v2` (768-d) so profiles in Norwegian, English and other languages embed into one space — candidate generation works cross-language by design.
- **Spatio-temporal GNN for traffic-aware scoring.** A graph neural network (STGFormer-style) models traffic over the road network so commute scores reflect *real* travel time, not straight-line distance.
- **Synthetic data, privacy by design.** Models were trained on **1M synthetic user profiles and 4M synthetic preferences** generated with a self-hosted LLM (Qwen 2.5 via vLLM) plus rule-based preference models — so the system could be built and evaluated **without touching real personal data**, with explicit geodata obfuscation for GDPR / EU AI Act compliance.
- **Train in Python, serve anywhere — via ONNX.** Every one of the 21 models exports to **ONNX**, giving a clean seam between training (Python) and serving (Python or .NET). The model registry exposes a single `predict` / `batch_predict` interface over thread-safe ONNX sessions.
- **A real product, not a notebook.** The v2 demo app is a full React + Vite + TypeScript front end (Mapbox maps, Redux, onboarding, commute and swap flows) over a Python backend serving the ONNX models — end-to-end, so the matching is demoable, not theoretical.

## Trade-offs

- **Synthetic vs. real training data.** Synthetic data removes the privacy and cold-start problems but introduces a distribution gap. The mitigation is hybrid generation (rule-based preferences + LLM variety) and treating the real pilot as the true evaluation set — synthetic to build, real to validate.
- **ILP optimality vs. latency.** Exact chain optimisation is expensive; column generation keeps it tractable on large pools, trading guaranteed global optimality for solutions that are optimal-enough fast enough for a consumer flow.
- **ONNX portability vs. export friction.** ONNX buys language-agnostic serving and a clean train/serve split, at the cost of having to keep every model inside the exportable-op set — a constraint I designed the models around rather than fought later.

## What generalizes

**The biggest wins came from recognising the shape of the problem and borrowing a solved solution — not from a bigger model.** Commute-swapping *is* kidney exchange; multilingual matching *is* a shared embedding space; "serve the model in .NET" *is* an ONNX boundary. The same instincts I bring to any applied-ML system: model the problem honestly, reuse battle-tested algorithms across domains, train without real PII where you can, and put a clean seam between training and serving so research and production don't block each other.

---

*Companion: the pre-launch growth pipeline → [JobSwap — growth & data pipeline](jobswap-growth-pipeline.md). Happy to walk through the chain solver, the ranker or the ONNX serving path. — Nikita Alexeev*
