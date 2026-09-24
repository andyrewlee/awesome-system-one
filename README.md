# Awesome System One [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated list of System One models and tools.

You give them some state and a set of typed questions. They give back a choice, a score, or a yes/no probability. No generated text, so nothing to parse. TypeSafe named the class after Kahneman's System 1; this list is about the software. Jev was the first commercial model.

## Contents

- [Models](#models) - Call a hosted model. Jev for the original API, Laya if you want open weights.
- [Open Implementations](#open-implementations) - Train or run one yourself.
- [Agents](#agents) - Need it to pick the next click or tap.
- [Tools](#tools) - Ready-made plugin or CLI.
- [SDKs & Adapters](#sdks--adapters) - Calling from application code.
- [Baselines](#baselines) - Want a cheaper classifier first. Same job, different interface.
- [Benchmarks](#benchmarks) - Compare models.
- [Reading](#reading) - The psychology the name comes from.

## Models

Hosted or open. State in, typed answers out.

- [Cua-S1](https://github.com/trycua/cua/tree/main/libs/cua-s1) - Research stack for small specialist computer-use models. First checkpoint is form-oriented: [cua-s1-forms](https://huggingface.co/cua-ai/cua-s1-forms), a 706K-param option scorer with its [training dataset](https://huggingface.co/datasets/cua-ai/cua-s1-forms) on Hugging Face. Planning and execution stay separate.
- [Jev](https://typesafe.ai) - TypeSafe's System One model. Choice, Score, and Noul over a state in one request, about 70–500 ms, trained with RLCD. Hosted API ($0.042 per million input tokens, output free); weights unpublished. Also on [OpenRouter](https://openrouter.ai/typesafe/jev-1.13), [Vercel AI Gateway](https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway), [Cloudflare Workers AI](https://developers.cloudflare.com/ai/models/typesafe/jev/), and [LLMGateway](https://docs.llmgateway.io/features/system-one). [Docs](https://docs.typesafe.ai/concepts/system-one) · [Launch post](https://typesafe.ai/blog/introducing-system-one-models-and-jev)
- [Jev-Omni](https://huggingface.co/akhilaaa3/Jev-Omni) - Multimodal decision classifier on Gemma 4 12B: text, image, audio, or video in, a probability per option out. Apache 2.0 weights. One question per call, best under about 20 options.
- [Laya](https://github.com/NandhaKishorM/laya) - Multilingual, non-autoregressive decision engine. Typed `choice` / `score` / `noul` over 100+ languages in a single forward pass (about 33 ms on a T4). Router across English, multilingual, and typed-decisions checkpoints. Apache 2.0.

## Open Implementations

Same request shape on open weights. Not TypeSafe's architecture.

- [agent-jev](https://github.com/malevrigns/agent-jev) - AgentJev-0.6B: a small trained decision model that takes unstructured state (diffs, traces, logs) and returns calibrated distributions in one ~50 ms forward pass. Apache 2.0.
- [AnyJev](https://github.com/nokia-applied-research/AnyJev) - Turns any LLM into a Jev-style decision model: typed decisions with probabilities, no training. From Nokia Applied Research, Apache 2.0.
- [decider](https://github.com/Mapika/decider) - Qwen3.5 fine-tunes (0.8B up to a 35B MoE, plus a vision build), Apache 2.0 weights on Hugging Face. Serves TypeSafe `/v1/systemone`, so the official SDK works by repointing `TYPESAFE_BASE_URL`.
- [djev](https://github.com/Davipar/djev-dev) - DiffusionGemma plus vLLM: an inference method rather than new weights, with native image inputs and a hosted API. Ranked 3rd on JevBench v1.2, just under Jev itself.
- [jeff](https://github.com/logan-markewich/jeff) - Self-hosted drop-in on GLiFormer 400M, MIT. Speaks the TypeSafe request shape (JevBench drove it with the official adapter); ranked in v1.2.2.
- [jevlike](https://github.com/vinnylarouge/jevlike) - Train a small one-pass scorer over a changing list of text options. Each option queries the context; a shared head returns one probability per option. Byte encoder from scratch, or a frozen Hugging Face encoder.
- [jevmlx](https://github.com/bnsd55/jevmlx) - Jev-style parallel constrained decisions for any MLX model on Apple Silicon. Scores every allowed answer per schema field in one pass, so the output is valid by construction.
- [jev-visual](https://github.com/hr98w/jev-visual) - Educational Jev-like visual inference experiment on Apple Silicon: shared context, direct candidate scoring, and local visual demos.
- [kev](https://github.com/jaredpalmer/kev) - LoRA plus a pointer readout on Qwen3.5 (0.8B / 4B / 9B), Apache 2.0, with training code and frozen eval suites. TypeSafe `/v1/systemone` drop-in, calibrated by default via a fitted temperature. Frozen evals put Kev-9B about 3.5 points behind Jev on unseen sources; MLX, ROCm, CUDA, and a one-command Modal deploy.
- [LLM2Jev](https://github.com/Yinsongxu/LLM2Jev) - Adapts local language models into Jev-compatible decision engines with Choice / Score / Noul outputs, via prefill-only binary inference. Apache 2.0.
- [NanoJev](https://github.com/TianyuCodings/NanoJev) - 0.6B replica trained from scratch, MIT. Ships the weights, the dataset, and the end-to-end training pipeline, plus a side-by-side demo against Jev.
- [Nimble](https://github.com/bespokelabsai/nimble) - Qwen3.5-9B LoRA from Bespoke Labs. Contrastive recipe, not distilled from Jev. 90% vs Jev 93% on a 324-example holdout.
- [Open Jev](https://github.com/intikhab49/open-jev-typed-decision-engine) - 150M ModernBERT encoder that answers per-request `noul` / `choice` / `score` questions in one pass. Trains on a Colab T4 in about 30 minutes. Not the same project as SemIf, which was also once called OpenJev.
- [open-alternative-jev](https://github.com/ikermoel/open-alternative-jev) - System One-style layer on open weights, ranked on JevBench. Its cautionary detail: 72% vs 21% on the same answer-judging items with option order flipped.
- [openjev](https://github.com/razorback16/openjev) - Open, Jev-compatible System One decision server on DiffusionGemma. Apache 2.0.
- [openjev-sglang](https://github.com/ekzhang/openjev-sglang) - TypeSafe `/v1/systemone` on Qwen 35B MoE via SGLang. Prefill once, then first-token logits per question. No extra training.
- [openJev-verdict-2.0](https://github.com/Heman10x-NGU/openJev-verdict-2.0) - 151M ModernBERT decision engine, non-autoregressive, with published calibration numbers. Claims the top spot over Jev and Laya on the LocalLLaMA typed-decisions benchmark.
- [PlayJev](https://github.com/OmniJev/PlayJev) - 0.8B multimodal Jev-like model that plays GUI games directly from raw pixels. Apache 2.0.
- [reflex](https://github.com/kshetrajna12/reflex) - Open recreation on Qwen3.5. Prefills the state once, then scores every question in parallel from next-token logits. Serves the TypeSafe request shape. Browser demo on WebGPU.
- [SemIf](https://github.com/TheoLeeCJ/SemIf) - Runtime-defined semantic decisions from direct option logits. CUDA, MLX, and WebGPU, plus committed benchmarks. Used to be called OpenJev.
- [simple-jev](https://github.com/featherless-ai/simple-jev) - Turns any open model into a classifier / Jev endpoint. From Featherless, Apache 2.0.
- [system-one](https://github.com/sgoedecke/system-one) - Turns any open LLM into a System One classifier: batched single-token choice inference, TypeSafe SDK compatible. Doom and wikiracing demos on Qwen3-8B.
- [system-one-open](https://github.com/mithalouni/system-one-open) - Jev-style replica on Gemma 4 E2B / Gemma 3 270M, trained and served on Modal. One forward pass, no decoding. Live demos for support, Doom, browser-use, and smart home.
- [systemANE](https://github.com/kerryrm/systemANE) - Apple's Neural Engine as a free local System One decision engine on macOS: "Jev at home."
- [von](https://github.com/wfzyx/von) - Non-autoregressive local drop-in, sub-15 ms decisions, Apache 2.0.

## Agents

The model picks an action. Code runs it.

- [computer-use-jev](https://github.com/paulsmith/computer-use-jev) - Drives native macOS apps through the Accessibility API. Jev chooses the next action and target token from a live snapshot, and stops if confidence drops too low.
- [embodied-jev](https://github.com/FBddcz/embodied-jev) - MuJoCo robot decision workbench: MiniCPM-2B or Jev-class APIs pick the next action, with physics previews.
- [jev-browser](https://github.com/jkudish/jev-browser) - Headless browser agent. Jev picks click / type / select / done from the page's elements; code owns budgets and stop gates. MCP server, CLI, or library.
- [jev-browser-use](https://github.com/wy-coliney/jev-browser-use) - Split-brain browser agent: Jev clicks, Codex thinks and verifies.
- [jev-chat-jarvis](https://github.com/jev-chat/jev-chat-jarvis) - Phone chat copilot for WeChat / QQ / X / Feishu: reads the screen, suggests candidate replies, one tap to fill. Read-only — no app hooks.
- [jev-drone](https://github.com/RomanSlack/jev-drone) - Camera-only autonomous drone in MuJoCo with Jev in the loop at 2.5 Hz.
- [jev-trader](https://github.com/jarrodwatts/jev-trader) - Experimental: one Jev buy/sell decision every Monad block (about 300 ms) on the Kuru MON-USDC book. Dry-run by default.
- [jev-ultrafast](https://github.com/browser-use/jev-ultrafast) - Browser agent with a dynamic, indexed action space. Jev picks an operation and an element in one request; a small LLM writes text only when the operation is `TYPE_TEXT`. Zürich to London on Google Flights in 7.1 seconds.
- [mobile-jev](https://github.com/droidrun/mobile-jev) - Android agent on a real phone via Mobilerun. Jev picks `OPEN_APP` / `TAP` / `TYPE_TEXT` from an indexed snapshot. Text is copied from the goal, not generated.
- [neo4jev](https://github.com/jexp/neo4jev) - Jev navigates a Neo4j graph by classifying over each node's neighboring relationships: graph traversal as a chain of typed choices.
- [pi-jev](https://github.com/TheoOliveira/pi-jev) - Pi coding-agent extension: semantic tool and skill routing, typed `choice` / `noul` / `score` evaluations, optional auto-approval, and a post-run `jev-gate` CLI.
- [typesafe-assist](https://github.com/JanOstrowka/typesafe-assist) - Home Assistant conversation agent. Jev maps a spoken command plus exposed entities onto built-in intents. Free-text intents fall back to another agent.
- [typesafe-computer-use](https://github.com/awlevin/typesafe-computer-use) - Computer use for about $0.0002 a step: OCR the screen, Jev classifies the next action, code clicks. macOS.
- [typesafe-mario](https://github.com/fhshaik/typesafe-mario) - Jev plays Super Mario Bros. from structured emulator state: the classic RL demo with a decision model in the loop.

## Tools

Plugins and CLIs that put a System One model inside other software.

- [ai-cli](https://github.com/vercel-labs/ai-cli) - Vercel Labs terminal client. The `evaluate` command sends a state plus typed questions to `typesafe-ai/jev` on AI Gateway (`-m jev` for short).
- [blink](https://github.com/ellipsis-dev/blink) - Codebase search powered by Jev decisions: typed questions find where a task lives in a repo.
- [Canny](https://github.com/qkal/Canny) - Stops coding agents from claiming done without evidence: deterministic hooks decide what needs checking, Jev judges the result.
- [fast-jev-compaction](https://github.com/tamaratran/fast-jev-compaction) - Claude Code plugin that replaces the compaction summary with Jev decisions: every tool call and result is scored in one request and the stale ones dropped.
- [Foreman](https://github.com/thruwire/foreman) - Jev as a fast supervisor above slower coding agents, judging whether the work is complete, the requirements met, the tests sufficient, or a human is needed.
- [hermes-jev-skills](https://github.com/kerpopule/hermes-jev-skills) - Jev-powered skill suite for Hermes agents (also Claude Code and Codex): model routing, memory, compaction, skill selection, computer and browser use.
- [invalidate](https://github.com/chopratejas/invalidate) - Invalidation layer for AI memory: every fact gets a lease, and new evidence ends it.
- [jev-align](https://github.com/sutro-sh/jev-align) - Build calibrated AI functions from human feedback: Jev judgments optimized with GEPA. From Sutro.
- [jev-codex-router](https://github.com/0xNatoshi/jev-codex-router) - Per-turn model and reasoning-effort routing for Codex. Jev classifies each turn and picks the tier; a 7-day replay of 237 real turns measured about 60% savings against all-frontier.
- [jev-curate](https://github.com/AkashPriyadarshii/jev-curate) - Dataset sifter for synthetic and pretraining corpora: a Rust streaming core sends Jev keep/drop decisions over Parquet rows.
- [jev-review](https://github.com/devagrawal09/jev-review) - Staged code-review workflow with a local dashboard, using Jev to score and sort findings before a human opens the queue.
- [jev-search](https://github.com/superagents-lab/jev-search) - Web search pipeline where Jev handles source selection, query understanding, and relevance ranking.
- [jev-tree](https://github.com/reachjalil/jev-tree) - Recursive Jev choice over a taxonomy: selects from more than 255 options without hitting the choice cap.
- [jevframe](https://github.com/ktaletsk/jevframe) - Semantic AI for Pandas and Polars: classify and score DataFrame rows with natural-language questions and full probabilities.
- [jevmail](https://github.com/fazlerocks/jevmail) - Open-source Gmail triage: sorts an inbox into needs-reply / updates / promos / spam. Read-only, runs locally, about 3 cents per 1,000 emails.
- [jeview](https://github.com/andududu/jeview) - Local visualizer for Jev: a live view of every call your code makes.
- [JevRouter](https://github.com/BillionsBobby/JevRouter) - Jev-powered router across models, tools, and subagents.
- [jgrep](https://github.com/keltokhy/jgrep) - Grep where the pattern is a description: filters lines by meaning for about a thousandth of a cent each.
- [json-render](https://github.com/vercel-labs/json-render) - Vercel Labs generative-UI framework with an experimental Jev mode: Jev picks components from your catalog instead of a model writing markup.
- [pg-jev](https://github.com/realZachi/pg-jev) - PostgreSQL extension that lets you ask tables plain-language questions, answered by Jev.
- [semdecide](https://github.com/sharziki/semdecide) - Jev calls as Unix-pipeline predicates: routing, scoring, and JSONL filtering with stable exit codes, plus an agent-safety guard recipe.
- [snifftest](https://github.com/DanRWilloughby/snifftest) - Prose linter that sniffs out AI writing tells: zero-dependency countable rules plus one judgment model.
- [stuntd](https://github.com/bladedevoff/stuntd) - Local proxy that learns your app's typed LLM decisions and answers them itself with a Laya head. Jev- and OpenAI-compatible.
- [winnow](https://github.com/GhalebDweikat/winnow) - Claude Code hook that splits large tool results into blocks, asks Jev a yes/no relevance question per block, and stubs the ones that fail.

## SDKs & Adapters

- [@ai-sdk/typesafe-ai](https://ai-sdk.dev/providers/ai-sdk-providers/typesafe-ai) - Official Vercel AI SDK evaluation provider. Choice, Score, and Boolean (Noul) over one state via `experimental_evaluate`.
- [Community SDKs](https://systemonemodels.org/examples/tools/) - Unofficial clients for Go, Rust, Ruby, PHP, .NET, Elixir, and Swift, tracked by language. All launched in Jev's first week — check the last commit before depending on one.
- [eve](https://github.com/vercel/eve) - Vercel's open agent framework, where Jev powers the `evaluate` primitive and tool-approval policies.
- [jev-mcp](https://github.com/jkudish/jev-mcp) - MCP server of Jev judgment tools: verify, screen, find, rerank, classify, decide, compare, extract, review, and gate.
- [laya-mlx](https://github.com/mizorewww/laya-mlx) - Native MLX runtime for Laya on Apple Silicon: 7–14 ms decisions on an M3 Max, no GPU server needed.
- [llm-typesafe](https://github.com/simonw/llm-typesafe) - Simon Willison's `llm` plugin for Jev and other TypeSafe models.
- [qualm](https://github.com/qddegtya/qualm) - TypeScript wrapper where every decision needs a required `unsure` branch — ignoring the uncertainty case is a compile error.
- [skills](https://github.com/typesafe-ai/skills) - Official agent skill for designing System One workflows from Claude Code, Codex, and other skill-compatible agents.
- [system-one-adapter-python](https://github.com/typesafe-ai/system-one-adapter-python) - Drop-in `TypeSafeClient` replacement backed by OpenAI or Anthropic, for comparing Jev against an LLM on the same questions.
- [typesafe-mcp](https://github.com/itsmostafa/typesafe-mcp) - MCP server as a single static Go binary, no Node or Python runtime. Sends usage guidance back to the client so the agent writes better questions.
- [typesafe-sdk-js](https://github.com/typesafe-ai/typesafe-sdk-js) - Official TypeScript / JavaScript client.
- [typesafe-sdk-python](https://github.com/typesafe-ai/typesafe-sdk-python) - Official synchronous and asynchronous Python client.

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

- [Banking77](https://github.com/PolyAI-LDN/task-specific-datasets) - 77 fine-grained banking intents over 13k queries. Single-domain counterpart to CLINC150, and the benchmark Janus reports wins on. CC BY 4.0.
- [CLINC150 / OOS-Eval](https://github.com/clinc/oos-eval) - 150 in-scope intents plus explicit out-of-scope examples. Useful for routing, abstention, and confidence-threshold tests. CC BY 3.0.
- [DecisionBench](https://huggingface.co/datasets/akhilaaa3/decision-bench) - 80 scenarios and 293 questions per tier (medium is rules-based, hard is judgment calls), with a cost-per-decision axis baked in. What Jev-Omni reports on. Apache 2.0.
- [evals.typesafe.ai](https://evals.typesafe.ai) - TypeSafe's workflow evals: four production-shaped workflows scored against frontier LLM reference probabilities.
- [Janus](https://github.com/FirasSX914/Janus) - Measures a confidence threshold on your labeled data, then routes Jev vs a larger model. Ships no default: on Banking77 routing wins; on Web of Science it says do not route.
- [jev-behavior-study](https://github.com/RINNECODER/jev-behavior-study) - Independent look at Jev 1.13.0: option-order bias, facts lost in the middle of long context, and cases where a correct prerequisite still leads to the wrong action. Raw payloads and offline verification included.
- [jev-benchmarks](https://github.com/AbdelStark/jev-benchmarks) - Independent calibration, selective-risk, and latency measurements of Jev against GLiNER and other baselines.
- [jev-ood-calibration](https://github.com/scienthoon/jev-ood-calibration) - Independent calibration test on tasks Jev cannot have seen: 900 rule-generated tickets plus public benchmarks, with ECE noise floors and a temperature refit.
- [jev-phishing-bench](https://github.com/anisselbd/jev-phishing-bench) - Jev vs Claude Haiku 4.5 on 2,000 phishing emails: accuracy, calibration, latency, and cost, reproducible.
- [jev-rerank-bench](https://github.com/anessbelbati/jev-rerank-bench) - Jev vs Cohere Rerank 4 vs zerank-2 vs a chat baseline on 14 datasets. Every raw response saved. nDCG@10 is a tie, not a win.
- [JevBench](https://github.com/fstandhartinger/jevbench) - Public harness for Jev-class decision models: intelligence, calibration, speed, and cost on shared tasks. About 48 systems ranked; Jev 1.13.0 currently #1.
- [jevals](https://github.com/openlayer-ai/jevals) - Agent evals and guardrails as Jev decisions: one request per trace. Runs locally on Kev or Laya. From Openlayer.
- [LocalLLaMA/typed-decisions](https://huggingface.co/datasets/LocalLLaMA/typed-decisions) - Community typed-decision suites (customer service, invoices, security incidents, agent traces) the open implementations report accuracy and calibration against. Apache 2.0.
- [MASSIVE](https://github.com/alexa/massive) - Multilingual intent-and-slot dataset (about 1M utterances, 52 languages) for testing whether a fast decision layer generalizes. CC BY 4.0.
- [sysone-bench](https://github.com/instax-dutta/sysone-bench) - Head-to-head of System One models: Laya vs Jev on byte-identical inputs.

## Reading

- [Confidence](https://docs.typesafe.ai/confidence) - How Choice/Score confidence differs from probability, and how to gate act / review / escalate in code. Noul has no separate confidence field.
- [Cookbooks](https://docs.typesafe.ai/cookbooks) - Official patterns: parallel questions, citation check, RAG passage classification, date extraction, skill suggestion.
- [How to build with TypeSafe](https://docs.typesafe.ai/concepts/how-to-build-with-system-one) - Keep control flow in code. Split work into atomic questions, then combine the answers and route on uncertainty.
- [Introducing System One Models & Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) - TypeSafe's launch post: RLCD, parallel sampling, and how this differs from an LLM.
- [Jev 1.13 jaggedness](https://docs.typesafe.ai/model-jaggedness/jev-1.13) - First-party failure modes: counting, dates, indirection, distractors, generation. Keep arithmetic in code.
- [Jev's Architecture Unmasked](https://archerhume.com/posts/jevs-architecture-unmasked) - Community teardown of Jev's probable architecture; the write-up kev's open models are built from.
- [System One (docs)](https://docs.typesafe.ai/concepts/system-one) - Official definition of the model class and the Choice / Score / Noul primitives.
- [awesomejev.com](https://awesomejev.com) - Community directory indexing about a thousand Jev projects with daily-refreshed stars. Less curated, good for exhaustive search.
- [Latent Space: Jev, a System One model that only decides](https://www.latent.space/p/ainews-jev-a-system-one-model-that) - Launch-day writeup on what a decision-only model changes for latency and cost.
- [systemonemodels.org](https://systemonemodels.org) - Independent hub tracking every System One model, open alternative, community SDK, and example. The place to check when this list lags.
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
