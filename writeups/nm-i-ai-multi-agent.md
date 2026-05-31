← [Portfolio](../README.md)

# NM i AI 2026 — multi-agent coordination & rapid problem-solving

*Norwegian Championship in AI 2026 · entered solo, as Team Alexeev Digital Lab. A qualification round on multi-agent pathfinding, then a 69-hour final across three very different AI problems.*

## Qualification — the "Grocery Bot" multi-agent pathfinding problem

The task: coordinate a fleet of warehouse robots on a shared grid — a **Multi-Agent Path Finding (MAPF)** problem — routing every robot to pick up and deliver without collisions or deadlocks, maximising throughput. Scored against a hidden evaluator; you iterate on your controller and watch the score move.

I worked solo and treated it as an **optimization ladder**: start with the simplest thing that works, measure it, and only add complexity that pays for itself.

```
  baseline         greedy nearest-target routing
     │
     ├─► one-way corridors        +129%   turn the grid into a directed flow,
     │                                     kill head-on contention
     │
     ├─► PIBT collision resolver    +47%   priority inheritance + backtracking —
     │                                     local, near-complete, real-time
     │
     ├─► central dispatcher         +38%   assign targets globally instead of
     │                                     each robot greedily grabbing the nearest
     │
     └─► anti-deadlock layer        +53%   detect & break standstills before
                                           they cascade
```

Each step is a **measured** percentage gain over the previous controller — not a guess. After 50+ ideas I reached a **local optimum** where hand-engineered heuristics stopped paying, and began moving toward a **learned policy (RL / NN)** to push past it.

**Result: 15th of 380 teams — entered solo.** A single-person entry in roughly the top 4% on a hard MAPF problem.

### Why this ladder

- **One-way corridors first (+129%).** The biggest win was the cheapest. Most collisions on a dense grid are head-on contention; imposing a directed flow removes the hardest conflicts *before* any clever resolver runs. **Structure beats search.**
- **PIBT over a global MAPF solver.** PIBT (priority inheritance with backtracking) resolves conflicts locally each step; it's near-complete in practice and runs in real time at fleet scale, where a centralized optimal solver would not.
- **Central dispatcher (+38%).** Once movement was solved, the bottleneck moved to *assignment* — deciding targets globally beat per-robot greedy pickup.
- **Anti-deadlock (+53%).** At density the failure mode isn't collisions, it's standstills. A detect-and-break layer recovered most of the lost throughput.

## Final — three problems, 69 hours, solo

The final put **3000+ entrants** in front of **three simultaneous, unrelated tasks** (1M NOK prize pool). There was no time to over-engineer any one of them; the whole game was triage and knowing when *simple* was the right answer.

**1 · NorgesGruppen — shelf product detection (computer vision).**
Object detection on retail-shelf images. Took the score from **0.39 → 0.88** with a **model ensemble + Weighted Box Fusion + multi-scale inference** — no exotic training, just combining complementary detectors and fusing their boxes well.

**2 · Tripletex — AI accountant over an API.**
An agent performing accounting operations through Tripletex's API, across **7 languages**, covering **18 of 30 task types**. The hard part was reliable tool-use and language-robust intent parsing — not the accounting itself.

**3 · Astar Island — black-box Norse simulation.**
A black-box environment you can only poke and observe. Score **15 → 75 over 21 rounds**. The winning insight was data over cleverness: **replaying recorded runs beat live querying**, and a **lookup table beat a more complex model**. Understanding the environment paid more than modelling it.

## What generalizes

**Under a hard clock, simplicity beats complexity — and you have to measure to know which is which.** Every gain above is a number, not an intuition: one-way corridors (+129%) outperformed every clever resolver I tried; WBF beat retraining; a lookup table beat a model. The discipline that carried both rounds was the same loop:

1. **Ship the simplest controller; measure it.**
2. **Add one technique; measure the delta.** Keep it only if it pays.
3. **Recognise the local optimum** — know when heuristics are spent and the next gain needs a different class of method (RL), or when a different *task* deserves the hour more.

That's the same loop I run in production agent systems: structure the problem so the cheap fix lands first, instrument everything, and spend complexity only where the numbers justify it.

---

*Happy to walk through the MAPF ladder, the PIBT resolver, or any of the three final tasks in more detail. — Nikita Alexeev*
