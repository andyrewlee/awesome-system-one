# Awesome System One [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated list of System One models and tools.

You give them some state and a set of typed questions. They give back a choice, a score, or a yes/no probability. No generated text, so nothing to parse. TypeSafe named the class after Kahneman's System 1; this list is about the software. Jev was the first commercial model.

## Contents

- [Models](#models) - Call a hosted model. Jev for the original API, CLM or Laya for open weights.
- [Open Implementations](#open-implementations) - Train or run one yourself.
- [Agents](#agents) - Need it to pick the next click or tap.
- [Tools](#tools) - Ready-made plugin or CLI.
- [SDKs & Adapters](#sdks--adapters) - Calling from application code.
- [Baselines](#baselines) - Want a cheaper classifier first. Same job, different interface.
- [Benchmarks](#benchmarks) - Compare models.
- [Reading](#reading) - The psychology the name comes from.

## Models

Hosted or open. State in, typed answers out.

### Open weights

- [Clef](https://huggingface.co/Cloudflare/clef) - Cloudflare's open decision models (Clef and Clef-flash), Apache 2.0: frozen Qwen 27B / 9B backbones with a trained routing head, prefill-only non-autoregressive scoring, plus a vision encoder Jev lacks and a 64K context. Cloudflare's runs put Clef first on the Jev Decision Index; Clef-flash runs ~39 ms median. Hosted on Workers AI, with a new RL platform for fine-tuning it on your own data. Already in production on Cloudflare's Threat Intelligence team.
- [CLM](https://github.com/Contrastive-LM/CLM) - Contrastive Language Model: dual state/action encoders (frozen Qwen3-8B + 20M heads) where candidates embed once and cache, rather than re-reading the state. Apache 2.0, TypeSafe wire-compatible. Claims Jev parity on computer-use, gaming, and tool-calling at up to 9× lower latency, plus SOTA held-out verifier results (Terminal-Bench 2.1, DeepSWE).
- [Cua-S1](https://github.com/trycua/cua/tree/main/libs/cua-s1) - Research stack for small specialist computer-use models. First checkpoint is form-oriented: [cua-s1-forms](https://huggingface.co/cua-ai/cua-s1-forms), a 706K-param option scorer with its [training dataset](https://huggingface.co/datasets/cua-ai/cua-s1-forms) on Hugging Face. Planning and execution stay separate.
- [d1](https://www.liquid.ai/blog/d1-open) - Liquid AI's open decision-model family: d1-3B (text + vision, from LFM2.5-VL) and d1-omni-600M (adds audio). Single forward pass; d1-3B scores 48.57 on the Decision Index public split — best under 10B, on par with Decider 35B-A3B at 12× its size — at ~8 ms on an RTX 4090, 16 ms on Jetson AGX Thor. Open weights and GGUF builds on Hugging Face; also hosted on OpenRouter.
- [GLiNER2.5-Decide](https://huggingface.co/fastino/GLiNER2.5-Decide) - Decision specialist from the GLiNER team: any label set at call time — intent, routing, multi-label tags, ordinal scores — answered in one forward pass with no generated tokens. Apache 2.0, in 340M / 1B / multilingual builds. Claims 60.2% vs JevK5's 57.6% on Fastino's own 17-domain suite.
- [Jev-Omni](https://huggingface.co/akhilaaa3/Jev-Omni) - Multimodal decision classifier on Gemma 4 12B: text, image, audio, or video in, a probability per option out. Apache 2.0 weights. One question per call, best under about 20 options. Currently #1 on Image JevBench.
- [Laya](https://github.com/NandhaKishorM/laya) - Multilingual, non-autoregressive decision engine. Typed `choice` / `score` / `noul` over 100+ languages in a single forward pass (about 33 ms on a T4). Router across English, multilingual, and typed-decisions checkpoints. Apache 2.0.
- [NeuDecide](https://huggingface.co/spaces/neuphonic/neudecide) - Neuphonic's ~40 MB voice-action model: speech in, an action decision out, running entirely on WebAssembly in the browser. From the neuTTS/neucodec audio lab; model repo is gated, the Space is the public demo.
- [Vela](https://huggingface.co/vllm-sr/Vela-2.0-0.3B) - KR Labs × vLLM Semantic Router's open decision-model family (0.3B–9B, bidirectional ModernBERT encoder, 17 languages, Apache 2.0). Choice, Noul, and Score plus two new question types: Span — labeled text spans with offsets for PII and unsupported claims — and Set multi-label. SystemOne-compatible HTTP and Python APIs, ONNX runtime, and a Core ML port with a Swift runtime for Apple silicon. Trained for router work: routing, prompt-attack, PII, and hallucination screening.

### Hosted

- [Decider](https://openrouter.ai/perplexity/pplx-decider-v1.1-27b) - Perplexity's decision model (Decider V1.1 27B is the current checkpoint), on the System One request shape. On OpenRouter.
- [Hanzo Kai](https://docs.hanzo.ai/docs/decisions/) - Hanzo's hosted decision model on the OpenRouter Decisions wire: `model: "kai"` at `api.hanzo.ai/v1/decisions`, with Jev itself routable on the same endpoint. A Jev client works by repointing the base URL.
- [Jev](https://typesafe.ai) - TypeSafe's System One model. Choice, Score, and Noul over a state in one request, about 70–500 ms, trained with RLCD. Hosted API ($0.042 per million input tokens, output free); weights unpublished. Also on [OpenRouter](https://openrouter.ai/typesafe/jev-1.13), [Vercel AI Gateway](https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway), [Cloudflare Workers AI](https://developers.cloudflare.com/ai/models/typesafe/jev/), and [LLMGateway](https://docs.llmgateway.io/features/system-one). [Docs](https://docs.typesafe.ai/concepts/system-one) · [Launch post](https://typesafe.ai/blog/introducing-system-one-models-and-jev)
- [Jev Router](https://openrouter.ai/typesafe/jev-router) - TypeSafe's meta-application of Jev: a model that picks the best model and reasoning effort for each request, balancing quality, speed, and cost.
- [Mercury Decide](https://openrouter.ai/inception/mercury-decide) - Inception's structured decision model on the System One schema, from the diffusion-LLM lab behind Mercury. A free route exists on OpenRouter.
- [OpenAI Decisions API](https://devday.openai.com/) - OpenAI's entry in the class, announced at DevDay 2026 as a limited preview: text or image context plus a fixed answer set, returning one answer with a confidence score around 150–230 ms. Powered by a Luna variant. Now also routable on OpenRouter as `openai/gpt-6-luna-decisions`.
- [Solar Decide](https://console.upstage.ai/api/systemone) - Upstage's structured decision model on Solar Mini 4 (beta). Same `/v1/systemone` schema as Jev, calibrated probability per answer, and a 512K context — a whole document can be the state. `choice` is limited to 26 single-token letter options. On OpenRouter, with a low-latency Flash variant.
- [Span-01](https://openrouter.ai/respan/span-01) - Respan's behavioral-monitoring decision model: describe a behavior (user frustration, tool misuse) and get present / absent / not-observable probabilities on a conversation. A free Lite tier exists for high-volume monitoring. Vendor-run benchmarks put it ahead of Jev overall but behind GPT-6 Sol per-domain.

## Open Implementations

Same request shape on open weights. Not TypeSafe's architecture.

### Trained replicas

- [agent-jev](https://github.com/malevrigns/agent-jev) - AgentJev-0.6B: a small trained decision model that takes unstructured state (diffs, traces, logs) and returns calibrated distributions in one ~50 ms forward pass. Apache 2.0.
- [decider](https://github.com/Mapika/decider) - Qwen3.5 fine-tunes (0.8B up to a 35B MoE, plus a vision build), Apache 2.0 weights on Hugging Face. The vision build ranks #2 on Image JevBench. Serves TypeSafe `/v1/systemone`, so the official SDK works by repointing `TYPESAFE_BASE_URL`.
- [jevlike](https://github.com/vinnylarouge/jevlike) - Train a small one-pass scorer over a changing list of text options. Each option queries the context; a shared head returns one probability per option. Byte encoder from scratch, or a frozen Hugging Face encoder.
- [jevos](https://github.com/feder-cr/jev) - MiniCPM5-1B cut to 17 layers with a one-logit head, quantized to GGUF and run on CPU via llama.cpp. Serves TypeSafe `/v1/systemone` for `noul` (yes/no) questions only, so the official SDK works by repointing `TYPESAFE_BASE_URL`. MIT.
- [kev](https://github.com/jaredpalmer/kev) - LoRA plus a pointer readout on Qwen3.5 (0.8B / 4B / 9B), Apache 2.0, with training code and frozen eval suites. TypeSafe `/v1/systemone` drop-in, calibrated by default via a fitted temperature. Frozen evals put Kev-9B about 3.5 points behind Jev on unseen sources; MLX, ROCm, CUDA, and a one-command Modal deploy. Also the only open-weights route on OpenRouter's Decisions API.
- [NanoJev](https://github.com/TianyuCodings/NanoJev) - 0.6B replica trained from scratch, MIT. Ships the weights, the dataset, and the end-to-end training pipeline, plus a side-by-side demo against Jev.
- [Nimble](https://github.com/bespokelabsai/nimble) - Qwen3.5-9B LoRA from Bespoke Labs. Contrastive recipe, not distilled from Jev. 90% vs Jev 93% on a 324-example holdout.
- [Open Jev](https://github.com/intikhab49/open-jev-typed-decision-engine) - 150M ModernBERT encoder that answers per-request `noul` / `choice` / `score` questions in one pass. Trains on a Colab T4 in about 30 minutes. Not the same project as SemIf, which was also once called OpenJev.
- [openJev-verdict-2.0](https://github.com/Heman10x-NGU/openJev-verdict-2.0) - 151M ModernBERT decision engine, non-autoregressive, with published calibration numbers. Claims the top spot over Jev and Laya on the LocalLLaMA typed-decisions benchmark.
- [PlayJev](https://github.com/OmniJev/PlayJev) - 0.8B multimodal Jev-like model that plays GUI games directly from raw pixels. Apache 2.0.
- [ReJev](https://github.com/Joe-rq/ReJev) - Independent reproduction of Jev-style post-training on MiniCPM5-2B via LoRA: 51% → 80.5% on a sealed, leakage-checked holdout for about $5 of compute. Stage 1 covers single-letter `choice` only; the rigor is the point — significance testing, baselines, and a technical report that voids its own figure after finding contamination. MIT.
- [system-one-open](https://github.com/mithalouni/system-one-open) - Jev-style replica on Gemma 4 E2B / Gemma 3 270M, trained and served on Modal. One forward pass, no decoding. Live demos for support, Doom, browser-use, and smart home.
- [Tev1](https://github.com/togethercomputer/tev1) - Together AI's Jev-inspired experiment: Qwen3.5 fine-tunes that take state + question + 2–24 options and return one letter — openly next-token, not a non-autoregressive runtime. MIT, with full data recipe and a "train your own for $17" writeup. A 4B experimental build is also served on OpenRouter.
- [Valen](https://github.com/Liuziyu77/Valen) - Train-your-own multimodal System One model: evaluates text, images, and video against task instructions and returns probabilities over supplied candidates. Ships the model code, data pipeline, SFT plus experimental RLCD training, and evals.
- [WaterSheep](https://github.com/SamratDuttaOfficial/WaterSheep) - ModernBERT-base fine-tune with a decision head that answers `noul` / `choice` / `score` and also multi-label questions, with a fitted temperature per question type. Publishes its held-out numbers: 61.2% accuracy and 0.043 ECE on datasets it never trained on. Apache 2.0 code and weights; serves TypeSafe `/v1/systemone`, so the official SDK works by repointing `TYPESAFE_BASE_URL`, and an ONNX build runs in the browser.

### Serve any open model

- [AnyJev](https://github.com/nokia-applied-research/AnyJev) - Turns any LLM into a Jev-style decision model: typed decisions with probabilities, no training. From Nokia Applied Research, Apache 2.0.
- [djev](https://github.com/Davipar/djev-dev) - DiffusionGemma plus vLLM: an inference method rather than new weights, with native image inputs and a hosted API. Ranked 3rd on JevBench v1.2, just under Jev itself.
- [jeff](https://github.com/logan-markewich/jeff) - Self-hosted drop-in on GLiFormer 400M, MIT. Speaks the TypeSafe request shape (JevBench drove it with the official adapter); ranked in v1.2.2.
- [jevmlx](https://github.com/bnsd55/jevmlx) - Jev-style parallel constrained decisions for any MLX model on Apple Silicon. Scores every allowed answer per schema field in one pass, so the output is valid by construction.
- [jev-visual](https://github.com/hr98w/jev-visual) - Educational Jev-like visual inference experiment on Apple Silicon: shared context, direct candidate scoring, and local visual demos.
- [LLM2Jev](https://github.com/Yinsongxu/LLM2Jev) - Adapts local language models into Jev-compatible decision engines with Choice / Score / Noul outputs, via prefill-only binary inference. Apache 2.0.
- [ollaya](https://github.com/ollaya-dev/ollaya) - Ollama for decision models: `ollaya pull` / `serve` / `run` manage Laya, decider, NLI, and GLiClass locally behind a wire-identical TypeSafe `/v1/systemone` (and `/v1/decisions`) endpoint.
- [open-alternative-jev](https://github.com/ikermoel/open-alternative-jev) - System One-style layer on open weights, ranked on JevBench. Its cautionary detail: 72% vs 21% on the same answer-judging items with option order flipped.
- [openjev](https://github.com/razorback16/openjev) - Open, Jev-compatible System One decision server on DiffusionGemma. Apache 2.0.
- [openjev-sglang](https://github.com/ekzhang/openjev-sglang) - TypeSafe `/v1/systemone` on Qwen 35B MoE via SGLang. Prefill once, then first-token logits per question. No extra training.
- [reflex](https://github.com/kshetrajna12/reflex) - Open recreation on Qwen3.5. Prefills the state once, then scores every question in parallel from next-token logits. Serves the TypeSafe request shape. Browser demo on WebGPU. #3 on Image JevBench in its released stable configuration.
- [SemIf](https://github.com/TheoLeeCJ/SemIf-OpenJev) - Runtime-defined semantic decisions from direct option logits. CUDA, MLX, and WebGPU, plus committed benchmarks. Repo recently renamed from SemIf.
- [simple-jev](https://github.com/featherless-ai/simple-jev) - Turns any open model into a classifier / Jev endpoint. From Featherless, Apache 2.0.
- [system-one](https://github.com/sgoedecke/system-one) - Turns any open LLM into a System One classifier: batched single-token choice inference, TypeSafe SDK compatible. Doom and wikiracing demos on Qwen3-8B.
- [systemANE](https://github.com/kerryrm/systemANE) - Apple's Neural Engine as a free local System One decision engine on macOS: "Jev at home."
- [von](https://github.com/wfzyx/von) - Non-autoregressive local drop-in, sub-15 ms decisions, Apache 2.0.

## Agents

The model picks an action. Code runs it.

### Screen & device control

- [computer-use-jev](https://github.com/paulsmith/computer-use-jev) - Drives native macOS apps through the Accessibility API. Jev chooses the next action and target token from a live snapshot, and stops if confidence drops too low.
- [jev-browser](https://github.com/jkudish/jev-browser) - Headless browser agent. Jev picks click / type / select / done from the page's elements; code owns budgets and stop gates. MCP server, CLI, or library.
- [jev-browser-use](https://github.com/wy-coliney/jev-browser-use) - Split-brain browser agent: Jev clicks, Codex thinks and verifies.
- [jev-ultrafast](https://github.com/browser-use/jev-ultrafast) - Browser agent with a dynamic, indexed action space. Jev picks an operation and an element in one request; a small LLM writes text only when the operation is `TYPE_TEXT`. Zürich to London on Google Flights in 7.1 seconds.
- [mobile-jev](https://github.com/droidrun/mobile-jev) - Android agent on a real phone via Mobilerun. Jev picks `OPEN_APP` / `TAP` / `TYPE_TEXT` from an indexed snapshot. Text is copied from the goal, not generated.
- [tiptour-macos](https://github.com/milind-soni/tiptour-macos) - Local computer-use app for macOS where Jev is the default engine: it picks from locally detected controls and the app executes and validates each action.
- [typesafe-computer-use](https://github.com/awlevin/typesafe-computer-use) - Computer use for about $0.0002 a step: OCR the screen, Jev classifies the next action, code clicks. macOS.

### Domain agents

- [embodied-jev](https://github.com/FBddcz/embodied-jev) - MuJoCo robot decision workbench: MiniCPM-2B or Jev-class APIs pick the next action, with physics previews.
- [jev-chat-jarvis](https://github.com/jev-chat/jev-chat-jarvis) - Phone chat copilot for WeChat / QQ / X / Feishu: reads the screen, suggests candidate replies, one tap to fill. Read-only — no app hooks.
- [jev-drone](https://github.com/RomanSlack/jev-drone) - Camera-only autonomous drone in MuJoCo with Jev in the loop at 2.5 Hz.
- [jev-trader](https://github.com/jarrodwatts/jev-trader) - Experimental: one Jev buy/sell decision every Monad block (about 300 ms) on the Kuru MON-USDC book. Dry-run by default.
- [neo4jev](https://github.com/jexp/neo4jev) - Jev navigates a Neo4j graph by classifying over each node's neighboring relationships: graph traversal as a chain of typed choices.
- [QuantDinger](https://github.com/OpenByteInc/QuantDinger) - Open-source AI trading OS with a Jev decision gate in front of live entry orders: typed Choice plus an auditable decision timeline, fail-open, and exits that always bypass AI.
- [typesafe-assist](https://github.com/JanOstrowka/typesafe-assist) - Home Assistant conversation agent. Jev maps a spoken command plus exposed entities onto built-in intents. Free-text intents fall back to another agent.
- [typesafe-mario](https://github.com/fhshaik/typesafe-mario) - Jev plays Super Mario Bros. from structured emulator state: the classic RL demo with a decision model in the loop.

## Tools

Plugins and CLIs that put a System One model inside other software.

### Context & memory

- [distill](https://github.com/samuelfaj/distill) - Token-saver for agent sessions with a documented Jev routing layer.
- [fast-jev-compaction](https://github.com/tamaratran/fast-jev-compaction) - Claude Code plugin that replaces the compaction summary with Jev decisions: every tool call and result is scored in one request and the stale ones dropped.
- [invalidate](https://github.com/chopratejas/invalidate) - Invalidation layer for AI memory: every fact gets a lease, and new evidence ends it.
- [memsearch](https://github.com/zilliztech/memsearch) - Zilliz's agent memory layer, with optional Jev reranking and a published Chinese/English reranking evaluation.
- [stuntd](https://github.com/bladedevoff/stuntd) - Local proxy that learns your app's typed LLM decisions and answers them itself with a Laya head. Jev- and OpenAI-compatible.
- [winnow](https://github.com/GhalebDweikat/winnow) - Claude Code hook that splits large tool results into blocks, asks Jev a yes/no relevance question per block, and stubs the ones that fail.

### Routing & supervision

- [agentconnect](https://github.com/agentconnect-md/agentconnect) - Multi-agent collaboration across Slack, Telegram, and GitHub, using Jev for agent routing, model selection, and support triage.
- [Astra-Ares](https://github.com/miuuyy/Astra-Ares) - Adaptive reasoning effort: Jev judges each Codex step and turns GPT-6's thinking effort up or down mid-task, so easy steps stop burning tokens.
- [Foreman](https://github.com/thruwire/foreman) - Jev as a fast supervisor above slower coding agents, judging whether the work is complete, the requirements met, the tests sufficient, or a human is needed.
- [hermes-jev-skills](https://github.com/kerpopule/hermes-jev-skills) - Jev-powered skill suite for Hermes agents (also Claude Code and Codex): model routing, memory, compaction, skill selection, computer and browser use.
- [Intent-Router](https://github.com/angel291592/Intent-Router) - Intent compiler that converges vague requests into typed `IntentSpec` contracts — probe, ask, or halt — before routing. The input layer in front of routers and decision models like Jev or Laya.
- [jev-codex-router](https://github.com/0xNatoshi/jev-codex-router) - Per-turn model and reasoning-effort routing for Codex. Jev classifies each turn and picks the tier; a 7-day replay of 237 real turns measured about 60% savings against all-frontier.
- [JevRouter](https://github.com/BillionsBobby/JevRouter) - Jev-powered router across models, tools, and subagents.
- [pi-jev](https://github.com/TheoOliveira/pi-jev) - Pi coding-agent extension: semantic tool and skill routing, typed `choice` / `noul` / `score` evaluations, optional auto-approval, and a post-run `jev-gate` CLI.
- [vLLM Semantic Router](https://github.com/vllm-project/semantic-router) - Open, programmable decision layer for models and compute: routes requests across models, enforces safety checks, and picks reasoning effort — with the Vela decision models as its routing brain. Apache 2.0.

### Review & verification

- [Canny](https://github.com/qkal/Canny) - Stops coding agents from claiming done without evidence: deterministic hooks decide what needs checking, Jev judges the result.
- [jev-review](https://github.com/devagrawal09/jev-review) - Staged code-review workflow with a local dashboard, using Jev to score and sort findings before a human opens the queue.
- [perch](https://github.com/lakeday-org/perch) - Semantic code linting: rules written in plain English, judged by Jev.
- [reticle](https://github.com/reticlehq/reticle) - Verification harness for agent-built code; the `jev` driver explores a page by choosing among DOM-enumerated candidates — the model chooses, never composes.
- [sedum](https://github.com/sedum-dev/sedum) - Plain-English browser tests on Playwright where Jev only picks targets and next actions (Choice) and judges claims (holds/contradicted Nouls), and code owns every action and verdict. TypeScript, MIT.
- [snifftest](https://github.com/DanRWilloughby/snifftest) - Prose linter that sniffs out AI writing tells: zero-dependency countable rules plus one judgment model.

### Search & data

- [blink](https://github.com/ellipsis-dev/blink) - Codebase search powered by Jev decisions: typed questions find where a task lives in a repo.
- [docjev](https://github.com/jerryjliu/docjev) - Very fast document classifier and PDF splitter from the creator of LlamaIndex: plain-English rules, Jev assigns each page a category and finds document boundaries.
- [jev-curate](https://github.com/AkashPriyadarshii/jev-curate) - Dataset sifter for synthetic and pretraining corpora: a Rust streaming core sends Jev keep/drop decisions over Parquet rows.
- [jev-search](https://github.com/superagents-lab/jev-search) - Web search pipeline where Jev handles source selection, query understanding, and relevance ranking.
- [jev-tree](https://github.com/reachjalil/jev-tree) - Recursive Jev choice over a taxonomy: selects from more than 255 options without hitting the choice cap.
- [jevframe](https://github.com/ktaletsk/jevframe) - Semantic AI for Pandas and Polars: classify and score DataFrame rows with natural-language questions and full probabilities.
- [jevgrep](https://github.com/dzhng/jevgrep) - Codebase search for coding agents: Jev judges folders, files, and declarations, then returns reading leads and verbatim source excerpts. npm CLI plus an agent-skill installer; matched the baseline 8/10 on a ten-task SWE-bench at ~30% lower cost.
- [jgrep](https://github.com/keltokhy/jgrep) - Grep where the pattern is a description: filters lines by meaning for about a thousandth of a cent each.
- [pg-jev](https://github.com/realZachi/pg-jev) - PostgreSQL extension (on PGXN): `jev(rows, 'plain-language condition')` filters, ranks, and classifies table rows. The clever part — it batches 20 rows into one shared-state request with a `noul` each, amortizing Jev's request overhead ~2.5×, with read-ahead and per-row answer caching.

### Train & adapt

- [bandits](https://github.com/bandr-ai/bandits) - Trace mining for post-training: reads agent traces (OTel, Langfuse, Claude Code), judges each step by the tool's reaction rather than the agent's claim, distills the LLM judge into deterministic Python checks a human signs off on, and exports labeled SFT rows with full lineage. Its bundled Jev recipe trains a decision model on those labels with an honest scorecard — their Qwen3.5-4B reports 79.1% vs Jev's 66.8% on 1,920 held-out AgentProcessBench steps.
- [jev-align](https://github.com/sutro-sh/jev-align) - Build calibrated AI functions from human feedback: Jev judgments optimized with GEPA. From Sutro.
- [jimothy](https://github.com/AndrewPrifer/jimothy) - Turns Jev usage into small local classifiers: `--teacher typesafe-ai/jev` labels the data, then a MiniLM model runs it in Node or the browser.

### Apps & utilities

- [ai-cli](https://github.com/vercel-labs/ai-cli) - Vercel Labs terminal client. The `evaluate` command sends a state plus typed questions to `typesafe-ai/jev` on AI Gateway (`-m jev` for short).
- [classifier.dev](https://github.com/mrmps/classifier-dev) - Zero-shot text classification as a URL: `classifier.dev/spam,not+spam/your+text` returns a label with calibrated confidence — no key, no account, up to 1,000 texts per call. One Cloudflare Worker on Jev with an LLM fallback chain, plus a CLI and MCP server. MIT.
- [jevmail](https://github.com/fazlerocks/jevmail) - Open-source Gmail triage: sorts an inbox into needs-reply / updates / promos / spam. Read-only, runs locally, about 3 cents per 1,000 emails.
- [jeview](https://github.com/andududu/jeview) - Local visualizer for Jev: a live view of every call your code makes.
- [json-render](https://github.com/vercel-labs/json-render) - Vercel Labs generative-UI framework with an experimental Jev mode: Jev picks components from your catalog instead of a model writing markup.
- [semdecide](https://github.com/sharziki/semdecide) - Jev calls as Unix-pipeline predicates: routing, scoring, and JSONL filtering with stable exit codes, plus an agent-safety guard recipe.
- [unclutter](https://github.com/kitze/unclutter) - Browser extension that removes page clutter: Jev scores elements against reusable rules, the extension hides the junk.

## SDKs & Adapters

### Official

- [@ai-sdk/typesafe-ai](https://ai-sdk.dev/providers/ai-sdk-providers/typesafe-ai) - Official Vercel AI SDK evaluation provider. Choice, Score, and Boolean (Noul) over one state via `experimental_evaluate`.
- [skills](https://github.com/typesafe-ai/skills) - Official agent skill for designing System One workflows from Claude Code, Codex, and other skill-compatible agents.
- [typesafe-sdk-js](https://github.com/typesafe-ai/typesafe-sdk-js) - Official TypeScript / JavaScript client.
- [typesafe-sdk-python](https://github.com/typesafe-ai/typesafe-sdk-python) - Official synchronous and asynchronous Python client.

### Community clients

- [Community SDKs](https://systemonemodels.org/examples/tools/) - Unofficial clients for Go, Rust, Ruby, PHP, .NET, Elixir, and Swift, tracked by language. All launched in Jev's first week — check the last commit before depending on one.
- [jev-foundation-models](https://github.com/peterfriese/jev-foundation-models) - Native Swift 6 bridge that puts Jev behind Apple's Foundation Models framework interface.
- [llm-typesafe](https://github.com/simonw/llm-typesafe) - Simon Willison's `llm` plugin for Jev and other TypeSafe models.
- [qualm](https://github.com/qddegtya/qualm) - TypeScript wrapper where every decision needs a required `unsure` branch — ignoring the uncertainty case is a compile error.

### Frameworks & integrations

- [eve](https://github.com/vercel/eve) - Vercel's open agent framework, where Jev powers the `evaluate` primitive and tool-approval policies.
- [hono-jev-router](https://github.com/yusukebe/hono-jev-router) - Semantic HTTP routing middleware for Hono: route requests by meaning, from Hono's creator.
- [jev-mcp](https://github.com/jkudish/jev-mcp) - MCP server of Jev judgment tools: verify, screen, find, rerank, classify, decide, compare, extract, review, and gate.
- [laya-mlx](https://github.com/mizorewww/laya-mlx) - Native MLX runtime for Laya on Apple Silicon: 7–14 ms decisions on an M3 Max, no GPU server needed.
- [receptron/laya](https://github.com/receptron/laya) - Run Laya from Node.js / TypeScript via ONNX Runtime; the ~1.7 GB weights download from Hugging Face on first use.
- [system-one-adapter-python](https://github.com/typesafe-ai/system-one-adapter-python) - Drop-in `TypeSafeClient` replacement backed by OpenAI or Anthropic, for comparing Jev against an LLM on the same questions.
- [typesafe-mcp](https://github.com/itsmostafa/typesafe-mcp) - MCP server as a single static Go binary, no Node or Python runtime. Sends usage guidance back to the client so the agent writes better questions.

## Baselines

Cheaper classifiers for comparison. They do not share Jev's request shape.

- [fastText](https://github.com/facebookresearch/fastText) - Classic non-generative text classification library. Upstream archived in 2024.
- [GLiNER](https://github.com/urchade/GLiNER) - Lightweight zero-shot structured extraction: you name the entity types at inference time. Often the comparison point in Jev classification benches.
- [GLiNER2](https://github.com/fastino-ai/GLiNER2) - Fastino successor to GLiNER. Schema-conditioned encoder for classification, extraction, and relations in one pass.
- [ModernBERT](https://github.com/AnswerDotAI/ModernBERT) - Modernized BERT encoder under most fast classifiers, including Laya's English checkpoint. The non-generative baseline Jev's cost and speed usually get measured against.
- [RouteLLM](https://github.com/lm-sys/RouteLLM) - Trains and serves routers that pick which LLM should handle a query, based on cost and expected quality, rather than answering the question itself.
- [semantic-router](https://github.com/aurelio-labs/semantic-router) - Embedding-space route layer for LLMs and agents. Chooses a path from utterance similarity without a generative call.
- [SetFit](https://github.com/huggingface/setfit) - Prompt-free few-shot text classification on Sentence Transformers. Strong when labels are fixed at train time.

## Benchmarks

### Suites & harnesses

- [Amazon ESCI](https://github.com/amazon-science/esci-data) - Shopping-query relevance classification (exact / substitute / complement / irrelevant) at Amazon scale. A Jev Decision Index component.
- [API-Bank](https://github.com/AlibabaResearch/DAMO-ConvAI/tree/main/api-bank) - Alibaba's tool-use benchmark for whether a model picks the right API call. A Jev Decision Index component.
- [ARC](https://allenai.org/data/arc) - AI2's grade-school science MCQs in Easy and Challenge splits. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [Banking77](https://github.com/PolyAI-LDN/task-specific-datasets) - 77 fine-grained banking intents over 13k queries. Single-domain counterpart to CLINC150, and the benchmark Janus reports wins on. CC BY 4.0.
- [BFCL](https://gorilla.cs.berkeley.edu/leaderboard) - Berkeley Function Calling Leaderboard: the field's standard for function/tool-call accuracy (ICML 2025). A Jev Decision Index component.
- [BRIGHT](https://arxiv.org/abs/2407.12883) - Reasoning-intensive retrieval benchmark scored by nDCG — the Jev Decision Index's reranking component, and one Jev still leads.
- [CLINC150 / OOS-Eval](https://github.com/clinc/oos-eval) - 150 in-scope intents plus explicit out-of-scope examples. Useful for routing, abstention, and confidence-threshold tests. CC BY 3.0.
- [DecisionBench](https://huggingface.co/datasets/akhilaaa3/decision-bench) - 80 scenarios and 293 questions per tier (medium is rules-based, hard is judgment calls), with a cost-per-decision axis baked in. What Jev-Omni reports on. Apache 2.0.
- [evals.typesafe.ai](https://evals.typesafe.ai) - TypeSafe's workflow evals: four production-shaped workflows scored against frontier LLM reference probabilities.
- [fast-decisions](https://huggingface.co/datasets/fastino/fast-decisions) - Fastino's held-out suite: 17 operational domains × 300 examples each, scoring Jev, SemIf, Laya, and GLiFormer head-to-head on the same inputs. First-party to GLiNER2.5-Decide, which tops it.
- [GPQA](https://github.com/idavidrein/gpqa) - Graduate-level, Google-proof MCQs; the Diamond subset is the hard tier. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [GSM8K](https://github.com/openai/grade-school-math) - Grade-school math word problems; repurposed as multiple choice with random distractors (4- and 10-option conditions in the Jev vs Luna comparison).
- [HellaSwag](https://rowanzellers.com/hellaswag) - Commonsense sentence-completion MCQs. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [Image JevBench](https://benchmarkheaven.com/image-jev-bench) - Held-out image-decision suite: 684 items (228 public / 456 sealed) with frozen hashes and contamination tracking — it flagged Kev's Mind2Web training overlap itself. Jev-Omni, decider-vision, and Reflex top it; frontier chat APIs score near zero.
- [Jev Decision Index](https://huggingface.co/spaces/multimodalart/jev-decision-index) - Community index aggregating ~43 decision-model evals — intent classification, function calling, tool retrieval, reranking — plus latency. The benchmark surface Clef's launch numbers report on.
- [JevBench](https://github.com/fstandhartinger/jevbench) - Public harness for Jev-class decision models: intelligence, calibration, speed, and cost on shared tasks. About 48 systems ranked; Jev 1.13.0 currently #1.
- [jevals](https://github.com/openlayer-ai/jevals) - Agent evals and guardrails as Jev decisions: one request per trace. Runs locally on Kev or Laya. From Openlayer.
- [LocalLLaMA/typed-decisions](https://huggingface.co/datasets/LocalLLaMA/typed-decisions) - Community typed-decision suites (customer service, invoices, security incidents, agent traces) the open implementations report accuracy and calibration against. Apache 2.0.
- [MASSIVE](https://github.com/alexa/massive) - Multilingual intent-and-slot dataset (about 1M utterances, 52 languages) for testing whether a fast decision layer generalizes. CC BY 4.0.
- [MMLU](https://github.com/hendrycks/test) - The standard 57-subject multitask MCQ benchmark. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [MMLU-Pro](https://github.com/TIGER-AI-Lab/MMLU-Pro) - Harder MMLU successor with 10-option questions and more reasoning. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [MuSR](https://github.com/Zayne-sprague/MuSR) - Multistep soft-reasoning MCQs (murder mysteries, object placements, team allocation). Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.
- [PhishNChips](https://huggingface.co/datasets/AreLit/PhishNChips) - 2,000-email phishing benchmark plus a 220k-run adjudicated grid showing how system-prompt config swings detection. A Jev Decision Index component.
- [sysone-bench](https://github.com/instax-dutta/sysone-bench) - Head-to-head of System One models: Laya vs Jev on byte-identical inputs.
- [ToolRet](https://github.com/mangopy/tool-retrieval-benchmark) - ACL 2025 tool-retrieval benchmark: 7.6k retrieval tasks over a 43k-tool corpus, scored by nDCG@10. A Jev Decision Index component.
- [When2Call](https://aclanthology.org/2025.naacl-long.174/) - Evaluates tool-calling *decisions* rather than accuracy: when to call, when to ask a follow-up, when to admit the tools can't answer. A Jev Decision Index component — and one Jev still leads.
- [WinoGrande](https://winogrande.allenai.org) - Pronoun-resolution commonsense MCQs, adversarially filtered. Part of the standard MCQ battery used in Jev vs Luna Decisions comparisons.

### Probes & studies

- [Janus](https://github.com/FirasSX914/Janus) - Measures a confidence threshold on your labeled data, then routes Jev vs a larger model. Ships no default: on Banking77 routing wins; on Web of Science it says do not route.
- [jev-behavior-study](https://github.com/RINNECODER/jev-behavior-study) - Independent look at Jev 1.13.0: option-order bias, facts lost in the middle of long context, and cases where a correct prerequisite still leads to the wrong action. Raw payloads and offline verification included.
- [jev-benchmarks](https://github.com/AbdelStark/jev-benchmarks) - Independent calibration, selective-risk, and latency measurements of Jev against GLiNER and other baselines.
- [jev-ood-calibration](https://github.com/scienthoon/jev-ood-calibration) - Independent calibration test on tasks Jev cannot have seen: 900 rule-generated tickets plus public benchmarks, with ECE noise floors and a temperature refit.
- [jev-phishing-bench](https://github.com/anisselbd/jev-phishing-bench) - Jev vs Claude Haiku 4.5 on 2,000 phishing emails: accuracy, calibration, latency, and cost, reproducible.
- [jev-rerank-bench](https://github.com/anessbelbati/jev-rerank-bench) - Jev vs Cohere Rerank 4 vs zerank-2 vs a chat baseline on 14 datasets. Every raw response saved. nDCG@10 is a tie, not a win.

## Reading

### Docs & ecosystem

- [Confidence](https://docs.typesafe.ai/confidence) - How Choice/Score confidence differs from probability, and how to gate act / review / escalate in code. Noul has no separate confidence field.
- [Cookbooks](https://docs.typesafe.ai/cookbooks) - Official patterns: parallel questions, citation check, RAG passage classification, date extraction, skill suggestion.
- [How to build with TypeSafe](https://docs.typesafe.ai/concepts/how-to-build-with-system-one) - Keep control flow in code. Split work into atomic questions, then combine the answers and route on uncertainty.
- [Introducing System One Models & Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) - TypeSafe's launch post: RLCD, parallel sampling, and how this differs from an LLM.
- [Jev in the Wild](https://arxiv.org/abs/2609.30216) - A data-driven survey and analysis of 2,170 public GitHub Jev projects, mapping early ecosystem growth, application domains, and decision-use patterns.
- [System One (docs)](https://docs.typesafe.ai/concepts/system-one) - Official definition of the model class and the Choice / Score / Noul primitives.
- [awesomejev.com](https://awesomejev.com) - Community directory indexing about a thousand Jev projects with daily-refreshed stars. Less curated, good for exhaustive search.
- [jev.store](https://www.jev.store) - Community store for Jev apps, extensions, and tools: a distribution surface with a submit flow, distinct from the firehose directories.
- [Latent Space: Jev, a System One model that only decides](https://www.latent.space/p/ainews-jev-a-system-one-model-that) - Launch-day writeup on what a decision-only model changes for latency and cost.
- [OpenRouter Decisions API](https://openrouter.ai/docs/api/api-reference/alphadecisions/submit-a-decisions-questions-and-answers-request) - Alpha multi-provider surface for the class: ~10 routes from nine publishers (Jev, Solar Decide, Span-01, Kev-4B, Clef, Luna Decisions, d1, Decider, Mercury Decide, Tev1) under one `state` + `questions` request shape. Browse the [full decision-model catalog](https://openrouter.ai/models?fmt=cards&output_modalities=decisions), or check the [live usage rankings](https://openrouter.ai/rankings/decisions) — Jev is still over 95% of real traffic.
- [systemonemodels.org](https://systemonemodels.org) - Independent hub tracking every System One model, open alternative, community SDK, and example. The place to check when this list lags.

### Jev under the microscope

- [JEV vs. LLMs as Rubric Judges: Cheaper, Faster, and Wrong in the Same Places](https://arxiv.org/abs/2609.29769) - Rao & Callison-Burch (2026). The counterpoint to the cascade story: Jev is 29–325× cheaper and competitive on binary criteria, but LLM judges repeat nearly all of its most confident errors, capping escalation gains at ~1.5 points.
- [JEV-as-a-Judge: Accept When Confident, Escalate When Unsure](https://arxiv.org/abs/2609.26550) - Li, Miao, Krishnan & Padman / CMU (2026). First independent study of Jev as an eval judge: within ~3 points of the strongest LLM judge on ordinary preference at 0.36% of the fee, weaker on derivation-checking. A confidence-gated cascade keeps 99% of the big judge's accuracy at ~57% of the cost.
- [Jev 1.13 jaggedness](https://docs.typesafe.ai/model-jaggedness/jev-1.13) - First-party failure modes: counting, dates, indirection, distractors, generation. Keep arithmetic in code.
- [Jev's Architecture Unmasked](https://archerhume.com/posts/jevs-architecture-unmasked) - Community teardown of Jev's probable architecture; the write-up kev's open models are built from.

### Foundations

- [Agents Thinking Fast and Slow: A Talker-Reasoner Architecture](https://arxiv.org/abs/2410.08328) - Christakopoulou, Mourad & Matarić / DeepMind (2024). Splits an agent into a fast conversational Talker (System 1) and a slow planning Reasoner (System 2).
- [Distilling System 2 into System 1](https://arxiv.org/abs/2407.06023) - Yu, Xu, Weston & Kulikov / Meta (2024). Compiles intermediate-thought techniques into single-pass outputs; the research framing closest to what Jev is.
- [Dual-processing accounts of reasoning, judgment, and social cognition](https://pubmed.ncbi.nlm.nih.gov/18154502/) - Evans (2008). Review of dual-process theories. System 1 is a family of accounts, not one algorithm.
- [Judgment under Uncertainty: Heuristics and Biases](https://www.science.org/doi/10.1126/science.185.4157.1124) - Tversky & Kahneman (1974). Heuristic judgment under uncertainty.
- [MDLM](https://github.com/kuleshov-group/mdlm) - Masked diffusion language model (NeurIPS 2024): parallel, non-autoregressive generation. The open research line nearest to Jev's parallel-sampler claims.
- [Reasoning the Fast and Frugal Way](https://pubmed.ncbi.nlm.nih.gov/8888650/) - Gigerenzer & Goldstein (1996). Simple heuristics that work with incomplete information.
- [System-1.x: Learning to Balance Fast and Slow Planning with Language Models](https://arxiv.org/abs/2407.14414) - Learns when to use fast direct planning and when to search, instead of always picking one.
- [System 2 Attention](https://arxiv.org/abs/2311.11829) - Weston & Sukhbaatar / Meta (2023). The LLM regenerates the context it should attend to before answering: deliberate filtering on top of fast attention.
- [Thinking, Fast and Slow](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow) - Daniel Kahneman. The book TypeSafe cites for the System 1 / System 2 framing.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
