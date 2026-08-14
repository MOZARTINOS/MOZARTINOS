# Nikita Alexeev

**AI / Agentic Systems Architect & ML Engineer** · Stavanger, Norway 🇳🇴

I design, build and **ship AI systems to production** — with version control, evaluation and observability, not notebook prototypes. Two years delivering production-grade AI across the full stack: agent systems, RAG pipelines, computer vision, neural-model training, AI voice agents, MCP tool servers and n8n automation. Strongest in **industrial & regulated domains** (computer-vision inspection, compliance) and **multi-agent systems**.

🌐 **[aiforalle.no](https://aiforalle.no)** — live portfolio: talk to the voice agents, open the demos
📍 Stavanger, Norway · 🗣️ Norwegian · English
💼 [linkedin.com/in/nikita-alexeev](https://linkedin.com/in/nikita-alexeev)

---

### 🧠 How I work
**Evaluation-driven, observable, reproducible.** I measure before I ship (precision/recall/F1, cost-per-unit), put a kill-switch on anything autonomous, and treat governance + guardrails as part of the architecture — not an afterthought. → **[How I architect agentic AI systems](writeups/agentic-architecture.md)**

**AI-assisted development is the method, not a footnote.** Claude Code, Cursor and Codex are my daily tools, from first prompt to tested code in production. I own the architecture and infrastructure decisions and the guarantee that what ships can be maintained by someone else.

---

## 🚀 Selected work

> Several systems below run in production and stay under closed development. Where the code isn't public, I've written up the **architecture and trade-offs** — and I'm glad to do a **live walkthrough**.

### Agentic & multi-agent systems
- **Tilsyn.AI — AI inspection platform** *(own venture · runs on my own infrastructure, not publicly available)* — A different approach to automated inspection in a regulated industrial domain: VLM + RAG behind an orchestrator, eval-driven quality gates and a kill-switch. The core idea is kept private, but the **pattern is published as open code below** — and I'm glad to do a live walkthrough of the architecture and the evaluation setup.
- **[vlm-rag-pipeline-demo](https://github.com/MOZARTINOS/vlm-rag-pipeline-demo)** — The readable, sanitized version of that pattern: a **two-pass VLM + RAG pipeline** where the model may only cite what retrieval actually returned. Grounding is enforced **by the code, not by the prompt**, so an unsupported claim can't survive the second pass. MIT, tested with pytest.
- **NM i AI 2026 — multi-agent coordination** *(15th of 380, solo)* — Norwegian AI Championship. Qualification: a MAPF warehouse-robot controller built as a measured optimization ladder — one-way corridors (+129%) → PIBT collision resolution (+47%) → central dispatcher (+38%) → anti-deadlock layer (+53%). Final (69 h, 3 tasks): shelf-detection CV 0.39→0.88 (ensemble + WBF + multi-scale), an API accounting agent (7 languages), and a black-box simulation. → **[write-up](writeups/nm-i-ai-multi-agent.md)** · [competition repo](https://github.com/MOZARTINOS/ainm-2026) · [MAPF coordination code](https://github.com/MOZARTINOS/mapf-warehouse-coordination)
- **Orkestrator AI — planning cockpit** *(built for [Stavanger kulturskole](https://www.stavangerkulturskole.no/) · pilot autumn 2026)* · **[live demo ↗](https://orkai.no)** *(access code on [aiforalle.no](https://aiforalle.no) or on request)* — Teachers, ensembles, students and rooms as a **drag-and-drop graph board** instead of a spreadsheet, with a conflict engine that flags eight kinds of scheduling clash. This is what governance looks like in practice: the AI sits behind a **pseudonymization boundary** so raw names and addresses never leave the process, its auto-solver may only propose fixes the **deterministic engine has already verified** reduce conflicts, and every edit lands in a **sandbox draft** a human applies or discards. Next.js 16 · Postgres · Claude (pseudonymized) · Mapbox. → [sanitized orchestration demo](https://github.com/MOZARTINOS/agentic-orchestrator-demo) (typed agent-coordination backend, Next.js + tRPC + Claude SDK)
- **Research-built writing skill + editorial pipeline — published in print** — Deep research across several LLMs on how the best magazines actually construct an explainer, distilled into a **reusable writing skill** (structure, nut graf, controlling analogy, anti-hype discipline). That skill plus **Claude Fable 5** produced the draft; an **n8n pipeline** then stripped the generative tells — a **Judge** agent scores the text against a living editorial policy and flags unverifiable claims, a **Reviser** fixes what the Judge caught, and a **score gate** decides whether a second pass is needed. A second workflow diffs my hand-edits against the AI original and folds the lessons back into the policy, so it converges on my voice rather than a generic one. Fact-checking stays human. The output ran as a feature explainer on MiroFish in the *AI Rising* issue of **Sooo. Magazine** (July 2026) — evaluation validated by someone else's editorial acceptance, not by my own score. → **[read the article ↗](https://www.zinio.com/no/article/sooo-magazine/ai-risingsummer-2026-i709632/mirofish-a-simulation-of-the-future-a23)** · [pipeline code](https://github.com/MOZARTINOS/n8n-write-human)

### AI voice agents *(live — Norwegian + English)*
Conversational voice agents I built on **ElevenLabs** — bilingual (NO/EN), demoable in the browser:
- **SIMO — in-cabin voice agent** · [talk to it ↗](https://elevenlabs.io/app/talk-to?agent_id=agent_6401krp54rbfeh1b80qxbbc3h4da&branch_id=agtbrch_0301krp54rz6ffb9rwbs0fy6hg05) — voice assistant for the SIMO motion-simulator platform (Heimdall Innovation).
- **Stavanger Kulturskole — AI secretary** · [talk to it ↗](https://elevenlabs.io/app/talk-to?agent_id=agent_8201ks81rskme5sam0956xse84q9&branch_id=agtbrch_2801ks81rvmyf6sswmdc0yec0yq1) — front-desk voice agent for a municipal music school: inquiries + booking-style requests.
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
- **[n8n-write-human](https://github.com/MOZARTINOS/n8n-write-human)** — The editorial pipeline above, as runnable code: draft → **judge** (a separate model scores against a living policy) → revise → deterministic cleanup of the AI tells. Judging and enforcement are deliberately split.
- **[davinci-resolve-mcp-v2](https://github.com/MOZARTINOS/davinci-resolve-mcp-v2)** — MCP server for DaVinci Resolve, rebuilt modular with a **confirm-gate safe-mode layer** over destructive operations. Extends `samuelgursky/davinci-resolve-mcp`.
- **[screenreader-retrofit](https://github.com/MOZARTINOS/screenreader-retrofit)** — Agent skill that retrofits an existing web app for blind screen-reader users (NVDA/VoiceOver/TalkBack): semantics, live commentary, focus management, sonification, haptics. Written while making cablegoal.com actually usable without sight.
- **[notebooklm-mastery-guide](https://github.com/MOZARTINOS/notebooklm-mastery-guide)** — Beginner-to-expert NotebookLM guide (EN/NO) plus a 27-workflow agent playbook of paste-ready prompts.
- **[claude-kahoot-skill](https://github.com/MOZARTINOS/claude-kahoot-skill)** — Agent skill for designing and safely importing Kahoot quizzes.

### Shipped products
- **Cable Goal — forensic 3D replay** *([cablegoal.com](https://cablegoal.com))* — A browser 3D reconstruction of the disputed Norway–England World Cup goal, the shot that appeared to clip an overhead TV cable before England's equalizer. Re-kick it yourself and watch the trajectory branch on whether it hits the wire, then vote. Built solo end to end: physics, rigged 3D players, a rate-limited voting API and a live per-country dashboard. 1,800+ votes from 35+ countries, and featured in the Norwegian weekly **Morgenbladet** (19 July 2026). Three.js · Netlify Functions · WebAudio · Node. → **[Morgenbladet article ↗](https://www.morgenbladet.no/kultur/vaieren-som-sysselsatte-folket/10445227)** · [repo](https://github.com/MOZARTINOS/cablegoal)
- **Viskr — real-time AI call coaching** *(novel — iOS + Android, in production)* — An app that rides a live phone call and feeds you the next line on screen: real-time transcription, speaker separation, AI prompts mid-conversation. For sales calls and interviews, where the right line a second too late is the wrong line. Nobody else ships this. Approach kept private — live walkthrough on request.
- **PanteMania** *(pantemania.no)* — Norway-first mobile game with an original real-world-to-game mechanic; full-stack React Native + Express + Prisma. Collect-and-battle gameplay tied to a real national system.
- **Sodarr** *([sodarr.com](https://sodarr.com))* — **Coca-Cola vs Pepsi, tasted properly.** A tasting app: scan the bottle or can in front of you and rate it *while drinking it* — five recipe axes (sweetness, bitterness, aftertaste, aroma, body), flavour tags, 1–5 verdict — and every verdict feeds a **weekly Coca-Cola vs Pepsi battle scored city by city**. Each tasting is also compared against your own history ("sweeter than your usual"), privately. The interesting modelling problem: three further axes (temperature, carbonation, freshness) are kept **brand-independent** so the same event yields a second signal — how well the bottle was *stored* — without preference contamination (Norway is an extreme Pepsi Max market; raw scores would drown it). Geo queries and score aggregation run **inside Postgres** (RPC + scheduled jobs), so it runs on a free tier. React 19 + TypeScript + Supabase + MapLibre GL, 11 languages; built solo.
- **VibeVox** *([vibevox.io](https://vibevox.io))* — Windows voice dictation/translation; **on-device** ASR — choice of NVIDIA Parakeet (NeMo, INT8) or OpenAI Whisper — packaged installer.
- **SIMO** *(Heimdall Innovation)* — Unity 6 motion-simulator platform: DJI O4 video → washout-filter motion cueing → motorized chair, with an in-cabin voice agent (above). Shipping as [SIMO — Simulation Redefined ↗](https://www.linkedin.com/company/simo-simulation-redefined/). → **[video of the rig in motion ↗](https://www.linkedin.com/posts/nikita-alexeev_unity-3d-motionsimulator-activity-7464652941753307136-J2RC)**
- **PrisSnap** *([prissnap.no](https://prissnap.no))* — AI pricing for FINN.no (web + Android): Gemini Vision → price estimate + ready-to-post Norwegian listing in seconds.
- **Byggekostnad Pro** · [live demo ↗](https://byggekostnadpro-marjan-demo.netlify.app/) — Norwegian construction-cost estimator: a guided step-by-step flow (project, dimensions, ground, structure, technical, quality) updates the budget live as you pick timber vs concrete, insulation class, finish level — with saved calculations, company profiles, exportable PDF cost reports and an inline **AI advisor** for building-cost questions. React + TypeScript + Supabase.
- **SkiPuls.AI** *([skipuls.no](https://skipuls.no/))* — Privacy-first crowd estimation for ski resorts: lightweight CPU object-detection at the edge counts people on a slope or in a lift queue, each frame masked to a region of interest and **discarded on the spot** — no facial recognition, no stored images, only the headcount feeding a live dashboard. FastAPI + Postgres + Next.js + Docker.
- **JobSwap — commute-matching ML platform** *(Visam AS · R&D approved under SkatteFUNN / Research Council of Norway)* · **[myjobswap.com](https://myjobswap.com)** · [my v2 build ↗](https://jobswap.netlify.app) — An AI platform that cuts commutes by matching "mirror twins" and chaining job/home swaps (*Exchange Jobs, Shorten Commutes, Save the Planet* — launching on Kickstarter). I built the ML system: **21 ONNX-exported models** — a spatio-temporal traffic **GNN**, a Siamese compatibility network, an XGBoost **LambdaMART** ranker and an **ILP chain solver (kidney-exchange algorithm)** — trained on 1M synthetic profiles (privacy by design), plus a **v2 React + Mapbox demo app** over a Python backend. → **[ML write-up](writeups/jobswap-ml-platform.md)** · [growth pipeline](writeups/jobswap-growth-pipeline.md)

### Data, infra & integrations
- **GlobalPrice** · **[costglobe.com ↗](https://costglobe.com/)** — Electricity/fuel/transport price tracker across 36 countries on an interactive WebGL globe with **markerless head-tracked perspective** — webcam face-tracking shifts the 3D viewpoint in real time (head-coupled "fish-tank" parallax, no markers). Node/Express backend with resilient per-source scrapers + a validated API.

---

## 🛠️ Stack
*What I build with — using AI-assisted development as the working method (see above), so read this as the stack I ship and maintain, not as hand-written-from-scratch fluency.*

**Languages & runtimes** Python · TypeScript/JS · SQL · Bash · Kotlin · C#/Unity · Go · R
**AI-assisted dev** Claude Code · Cursor · Codex · agent skills · MCP
**AI/ML** Claude · OpenAI · Gemini/Gemma · VLM/multimodal · RAG (pgvector, Pinecone) · NN training/fine-tuning · GNNs · learning-to-rank (LambdaMART) · object detection · STT (Parakeet, Whisper, Deepgram) · OpenAI Realtime · quantization (FP8/INT8) · ONNX · vLLM · eval (P/R/F1)
**Agents** MCP servers & tool-use · multi-agent orchestration · voice agents (ElevenLabs) · n8n orchestration · human-in-the-loop · ReAct / plan-execute · guardrails · multi-provider LLM gateway
**Backend/data** FastAPI · Next.js · Node/Express · REST/WebSocket · Twilio · Brevo · Google APIs · Supabase/Postgres (pgvector, RLS, Alembic) · n8n
**Infra** Docker/Compose · GPU orchestration (vast.ai) · Vercel/Netlify/Hetzner · GitHub Actions

---

*Project Lead & Developer @ [Visam AS](https://visam.no), Innovation Park Stavanger — industrial, maritime & energy clients.*
*Certified: Anthropic — Claude Code in Action (2026).*

<sub>Made with ♥ by Alexeev Digital Lab</sub>
