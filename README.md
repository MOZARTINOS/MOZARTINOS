# Nikita Alexeev

**AI / Agentic Systems Architect & ML Engineer** · Stavanger, Norway 🇳🇴

I design, build and **ship AI systems to production** — with version control, evaluation and observability, not notebook prototypes. Two years delivering production-grade AI across the full stack: agent systems, RAG pipelines, computer vision, neural-model training, AI voice agents, MCP tool servers and n8n automation. Strongest in **industrial & regulated domains** (computer-vision inspection, compliance) and **multi-agent systems**.

📍 Stavanger, Norway · 🗣️ Norwegian · English
💼 [linkedin.com/in/nikita-alexeev](https://linkedin.com/in/nikita-alexeev)

---

### 🧠 How I work
**Evaluation-driven, observable, reproducible.** I measure before I ship (precision/recall/F1, cost-per-unit), put a kill-switch on anything autonomous, and treat governance + guardrails as part of the architecture — not an afterthought. → **[How I architect agentic AI systems](writeups/agentic-architecture.md)**

---

## 🚀 Selected work

> Several systems below run in production and stay under closed development. Where the code isn't public, I've written up the **architecture and trade-offs** — and I'm glad to do a **live walkthrough**.

### Agentic & multi-agent systems
- **Tilsyn.AI — AI inspection platform** *(in production)* — A new approach to automated inspection in a regulated industrial domain. Built on a VLM + RAG architecture with an orchestrator, eval-driven quality and a kill-switch. **Core idea is novel and kept private** — live walkthrough on request.
- **NM i AI 2026 — multi-agent coordination** *(15th of 380, solo)* — Norwegian AI Championship. Qualification: a MAPF warehouse-robot controller built as a measured optimization ladder — one-way corridors (+129%) → PIBT collision resolution (+47%) → central dispatcher (+38%) → anti-deadlock layer (+53%). Final (69 h, 3 tasks): shelf-detection CV 0.39→0.88 (ensemble + WBF + multi-scale), an API accounting agent (7 languages), and a black-box simulation. → **[write-up](writeups/nm-i-ai-multi-agent.md)** · [repo](https://github.com/MOZARTINOS/ainm-2026)
- **Orkestrator AI — agentic orchestration product** — Visual workflow editor over a typed agent-coordination backend (Next.js + tRPC + Claude SDK), oklch design system, NO/RU localization. → [sanitized demo](https://github.com/MOZARTINOS/agentic-orchestrator-demo)

### AI voice agents *(live — Norwegian + English)*
Conversational voice agents I built on **ElevenLabs** — bilingual (NO/EN), demoable in the browser:
- **SIMO — in-cabin voice agent** · [talk to it ↗](https://elevenlabs.io/app/talk-to?agent_id=agent_6401krp54rbfeh1b80qxbbc3h4da&branch_id=agtbrch_0301krp54rz6ffb9rwbs0fy6hg05) — voice assistant for the SIMO motion-simulator platform (Heimdall Innovation).
- **Stavanger Kulturskole — secretary** · [talk to it ↗](https://elevenlabs.io/app/talk-to?agent_id=agent_8201ks81rskme5sam0956xse84q9&branch_id=agtbrch_2801ks81rvmyf6sswmdc0yec0yq1) — front-desk voice agent for a municipal music school: inquiries + booking-style requests.
- **Visam AI agent** · [talk to it ↗](https://elevenlabs.io/app/talk-to?agent_id=agent_3201kbrn3354fmwvmz3r7tabrkmf&branch_id=agtbrch_7501kcd4xzwvfwys63sb2k5v1fpk) — CRM-connected phone voice agent for Visam AS. It acts, not just talks: ask it to **email you the price list** or **book a consultation** (Innovation Park Stavanger or online) and it captures the lead end-to-end. Bilingual NO/EN.
- **VoxBridge — live call translation** *(in production · novel)* — real-time two-way phone-call translation: each caller hears the other in their own language, synchronously. Twilio Media Streams + OpenAI Realtime API.

### AI automation & integrations
- **FinnFunn — AI-driven social-media automation** · [on Facebook ↗](https://www.facebook.com/FinnFunnNorge) — A social-automation system driven by AI agents with an **n8n** backend: scrapes, scores, generates and schedules content daily, with a **Telegram human-in-the-loop** approval step before anything publishes. Runs in production, publishing daily to its Facebook community. Multi-model (Claude/Gemini/GPT), Deepgram, Supabase, Hetzner scraper. → **[write-up](writeups/finnfunn-orchestration.md)**
- **BRO Norge — AI-matched work brigades** · [live demo ↗](https://bro-norge.netlify.app) — A Norwegian service company that employs Ukrainian refugees in managed micro-teams (*brigades*): AI matches team composition, runs onboarding and sales outreach, and handles NO/UA/EN translation across the platform. The client buys a service (one invoice, a Norwegian-speaking lead on site, no HR overhead); the worker gets a route into employment. Two-audience site (clients + workers), Next.js on Netlify.
- **n8n automation at scale** — 200+ production workflows across products: lead scraping & enrichment (Google Places, BRREG), cold-email outreach (Brevo), video generation (Shotstack/Pexels) and booking chatbots (Google Calendar) — with retries, throttling, idempotency and webhook routing. → **[patterns write-up](writeups/n8n-automation-architecture.md)**

### AI tooling, MCP & open source *(MIT)*
- **[claude-prompt-engineering](https://github.com/MOZARTINOS/claude-prompt-engineering)** — Context-aware prompt-generation framework: 36 techniques synthesized from Anthropic docs + research, model-specific adaptations.
- **[mirofish-guide](https://github.com/MOZARTINOS/mirofish-guide)** ⭐ — Operator workflow, evaluation rubric & troubleshooting for a multi-agent simulation engine (GraphRAG, knowledge graph, prompt engineering).
- **[openclaw-roborock-cli](https://github.com/MOZARTINOS/openclaw-roborock-cli)** — Agent-skill backend + CLI for scriptable robot control; room-aware, ADB fallback, CI.
- **[openclaw-tune-codex-plus](https://github.com/MOZARTINOS/openclaw-tune-codex-plus)** — Cost-aware agentic ops: subscription-tier routing, quota-lifecycle tuning, security/isolation trade-off docs.
- **[claude-code-skill-tldraw-diagram](https://github.com/MOZARTINOS/claude-code-skill-tldraw-diagram)** — VS Code skill generating schema-valid tldraw architecture diagrams.

### Shipped products
- **Viskr — real-time AI call coaching** *(novel — iOS + Android, in production)* — An app that rides a live phone call and feeds you the next line on screen: real-time transcription, speaker separation, AI prompts mid-conversation. For sales calls and interviews, where the right line a second too late is the wrong line. Nobody else ships this. Approach kept private — live walkthrough on request.
- **PanteMania** *(pantemania.no)* — Norway-first mobile game with an original real-world-to-game mechanic; full-stack React Native + Express + Prisma. Collect-and-battle gameplay tied to a real national system.
- **VibeVox** *([vibevox.io](https://vibevox.io))* — Windows voice dictation/translation; **on-device** ASR — choice of NVIDIA Parakeet (NeMo, INT8) or OpenAI Whisper — packaged installer.
- **SIMO** *(Heimdall Innovation)* — Unity 6 motion-simulator platform: DJI O4 video → washout-filter motion cueing → motorized chair, with an in-cabin voice agent (above). Shipping as [SIMO — Simulation Redefined ↗](https://www.linkedin.com/company/simo-simulation-redefined/). → **[video of the rig in motion ↗](https://www.linkedin.com/posts/nikita-alexeev_unity-3d-motionsimulator-activity-7464652941753307136-J2RC)**
- **PrisSnap** *([prissnap.no](https://prissnap.no))* — AI pricing for FINN.no (web + Android): Gemini Vision → price estimate + ready-to-post Norwegian listing in seconds.
- **SkiAi** — Privacy-first crowd estimation for ski resorts; CPU object-detection with ROI filters, **frame discarded after inference** (no facial recognition). FastAPI + Postgres + Next.js + Docker.
- **JobSwap — commute-matching ML platform** *(Visam AS · R&D approved under SkatteFUNN / Research Council of Norway)* · **[myjobswap.com](https://myjobswap.com)** · [my v2 build ↗](https://jobswap.netlify.app) — An AI platform that cuts commutes by matching "mirror twins" and chaining job/home swaps (*Exchange Jobs, Shorten Commutes, Save the Planet* — launching on Kickstarter). I built the ML system: **21 ONNX-exported models** — a spatio-temporal traffic **GNN**, a Siamese compatibility network, an XGBoost **LambdaMART** ranker and an **ILP chain solver (kidney-exchange algorithm)** — trained on 1M synthetic profiles (privacy by design), plus a **v2 React + Mapbox demo app** over a Python backend. → **[ML write-up](writeups/jobswap-ml-platform.md)** · [growth pipeline](writeups/jobswap-growth-pipeline.md)

### Data, infra & integrations
- **GlobalPrice** · **[live demo ↗](https://globalprice.netlify.app/v2/)** — Electricity/fuel/transport price tracker across 36 countries on an interactive WebGL globe with **markerless head-tracked perspective** — webcam face-tracking shifts the 3D viewpoint in real time (head-coupled "fish-tank" parallax, no markers). Node/Express backend with resilient per-source scrapers + a validated API.

---

## 🛠️ Stack
**Languages** Python · TypeScript/JS · SQL · Bash · Kotlin · C#/Unity · Go · R
**AI/ML** Claude · OpenAI · Gemini/Gemma · VLM/multimodal · RAG (pgvector) · NN training/fine-tuning · GNNs · learning-to-rank (LambdaMART) · object detection · STT (Parakeet, Whisper, Deepgram) · OpenAI Realtime · quantization (FP8/INT8) · ONNX · vLLM · eval (P/R/F1)
**Agents** MCP servers & tool-use · multi-agent orchestration · voice agents (ElevenLabs) · n8n orchestration · human-in-the-loop · ReAct / plan-execute · guardrails · multi-provider LLM gateway
**Backend/data** FastAPI · Next.js · Node/Express · REST/WebSocket · Twilio · Brevo · Google APIs · Supabase/Postgres (pgvector, RLS, Alembic) · n8n
**Infra** Docker/Compose · GPU orchestration (vast.ai) · Vercel/Netlify/Hetzner · GitHub Actions

---

*Project Lead & Developer @ [Visam AS](https://visam.no), Innovation Park Stavanger — industrial, maritime & energy clients.*
*Certified: Anthropic — Claude Code in Action (2026).*

<sub>Made with ♥ by Alexeev Digital Lab</sub>
