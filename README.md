# Awesome System One [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated list of System One models and tools.

System One models take a state and typed questions and return decisions your code can use — a choice, a score, or a yes/no probability — without generating text. TypeSafe coined the name after Kahneman's System 1; the software class and the psychology literature are related by analogy, not identity. [Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) is the first commercial model in the class.

## How to choose

- **Call a hosted model.** [Models](#models) — Jev if you want the original API; Laya if you want open weights and multilingual routing.
- **Train or run one yourself.** [Open Implementations](#open-implementations) copy the state-in, typed-answers-out shape on open encoders.
- **Need the model to pick an action, not just classify.** [Agents](#agents) — browser, desktop, computer-use, and gating loops.
- **Wire it into application code.** [SDKs & Adapters](#sdks--adapters).
- **Need a simpler routing or classification baseline.** [Baselines](#baselines) — embedding routers and trained classifiers, not the same interface.
- **Compare models or reproduce claims.** [Benchmarks](#benchmarks).
- **Want the namesake in psychology.** [Reading](#reading).

## Models

Hosted or open System One models: state in, typed answers and probabilities out.

- [Cua-S1](https://github.com/trycua/cua/tree/main/libs/cua-s1) - Research stack for small specialist computer-use models. First checkpoint is form-oriented (`cua-s1-form-v0`); planning and execution stay separate, and weights are not distributed yet.
- [Jev](https://typesafe.ai) - TypeSafe's flagship System One model. Choice, Score, and Noul over a state in one request, ~70–500 ms, trained with RLCD. Hosted API; weights unpublished. Also on [OpenRouter](https://openrouter.ai/typesafe/jev-1.13), [Vercel AI Gateway](https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway), and [Cloudflare Workers AI](https://developers.cloudflare.com/ai/models/typesafe/jev/). [Docs](https://docs.typesafe.ai/concepts/system-one) · [Launch post](https://typesafe.ai/blog/introducing-system-one-models-and-jev)
- [Laya](https://github.com/NandhaKishorM/laya) - Multilingual, non-autoregressive decision engine. Typed `choice` / `score` / `noul` over 100+ languages in a single forward pass (~33 ms on a T4), with a router across English, multilingual, and typed-decisions checkpoints. Apache 2.0.

## Open Implementations

Community models that match the System One *interface* on open weights. None of these are TypeSafe's architecture.

- [jevlike](https://github.com/vinnylarouge/jevlike) - Starter for training a small one-pass scorer over a changing list of text options. Each option queries the context; a shared head returns one probability per option. Byte encoder from scratch, or a frozen Hugging Face encoder.
- [kev](https://github.com/jaredpalmer/kev) - LoRA plus a pointer readout on Qwen (0.5B–8B). One prefill, many typed questions, TypeSafe `/v1/systemone` drop-in, released weights and frozen evals against Jev.
- [Open Jev](https://github.com/intikhab49/open-jev-typed-decision-engine) - 150M ModernBERT encoder that answers per-request `noul` / `choice` / `score` questions in one pass. Trains on a Colab T4 in about 30 minutes. Distinct from SemIf, which was formerly also called OpenJev.
- [reflex](https://github.com/kshetrajna12/reflex) - Open recreation on Qwen3.5. Prefills the state once, then scores every question in parallel from next-token logits. Serves the TypeSafe request shape; browser demo on WebGPU.
- [SemIf](https://github.com/TheoLeeCJ/SemIf) - Open implementation of runtime-defined semantic decisions using direct option logits, with CUDA, MLX, and WebGPU paths plus committed benchmarks. Formerly OpenJev. Independent of TypeSafe.
- [system-one](https://github.com/sgoedecke/system-one) - Turns any open LLM into a System One classifier: batched single-token choice inference, TypeSafe SDK compatible. Includes Doom and wikiracing demos on Qwen3-8B.
- [system-one-open](https://github.com/mithalouni/system-one-open) - Jev-style replica on Gemma 4 E2B / Gemma 3 270M, trained and served on Modal. One forward pass, no decoding; live demos for support, Doom, browser-use, and smart home.

## Agents

Loops that ask a System One model which action or target to take, then execute in code.

- [computer-use-jev](https://github.com/paulsmith/computer-use-jev) - Drives native macOS apps through the Accessibility API, with Jev choosing the next action and target token from a live snapshot. Stops if confidence drops below a threshold.
- [jev-browser](https://github.com/jkudish/jev-browser) - Headless browser agent. Jev picks click / type / select / done from the page's elements; code owns budgets and stop gates. MCP server, CLI, or library.
- [jev-trader](https://github.com/jarrodwatts/jev-trader) - Experimental loop: one Jev buy/sell decision every Monad block (~300 ms) on the Kuru MON-USDC book. Dry-run by default.
- [jev-ultrafast](https://github.com/browser-use/jev-ultrafast) - Browser agent with a dynamic, indexed action space. Jev picks an operation and an element in one request; a small LLM writes text only when the operation is `TYPE_TEXT`. Zürich → London on Google Flights in 7.1 seconds.
- [mobile-jev](https://github.com/droidrun/mobile-jev) - Android agent on a real phone via Mobilerun. Jev picks `OPEN_APP` / `TAP` / `TYPE_TEXT` from an indexed snapshot; code copies text from the goal rather than generating it.
- [pi-jev](https://github.com/TheoOliveira/pi-jev) - Pi coding-agent extension: semantic tool/skill routing, typed `choice` / `noul` / `score` evaluations, optional auto-approval, and a post-run `jev-gate` CLI.
- [typesafe-assist](https://github.com/JanOstrowka/typesafe-assist) - Home Assistant conversation agent. Jev maps a spoken command plus exposed entities onto built-in intents; free-text intents fall back to another agent.

## SDKs & Adapters

- [jev-mcp](https://github.com/jkudish/jev-mcp) - MCP server of Jev judgment tools: verify, screen, find, rerank, classify, decide, compare, extract, review, and gate.
- [skills](https://github.com/typesafe-ai/skills) - Official agent skill for designing System One workflows from Claude Code, Codex, and other skill-compatible agents.
- [system-one-adapter-python](https://github.com/typesafe-ai/system-one-adapter-python) - Drop-in `TypeSafeClient` replacement backed by OpenAI or Anthropic, for comparing Jev against an LLM on the same questions.
- [typesafe-sdk-js](https://github.com/typesafe-ai/typesafe-sdk-js) - Official TypeScript / JavaScript client.
- [typesafe-sdk-python](https://github.com/typesafe-ai/typesafe-sdk-python) - Official synchronous and asynchronous Python client.

## Baselines

Adjacent fast-decision software. Useful for comparison; they do not implement the same runtime-defined typed-question interface.

- [fastText](https://github.com/facebookresearch/fastText) - Classic non-generative text classification library. Upstream archived in 2024.
- [GLiNER](https://github.com/urchade/GLiNER) - Lightweight zero-shot structured extraction: you name the entity types at inference time. Often the comparison point in Jev classification benches.
- [RouteLLM](https://github.com/lm-sys/RouteLLM) - Trains and serves routers that pick *which LLM* should handle a query from cost/performance trade-offs, rather than answering the application question.
- [semantic-router](https://github.com/aurelio-labs/semantic-router) - Embedding-space route layer for LLMs and agents. Chooses a path from utterance similarity without a generative call.
- [SetFit](https://github.com/huggingface/setfit) - Prompt-free few-shot text classification on Sentence Transformers. Strong baseline when labels are fixed at train time.

## Benchmarks

- [CLINC150 / OOS-Eval](https://github.com/clinc/oos-eval) - 150 in-scope intents plus explicit out-of-scope examples. Useful for routing, abstention, and confidence-threshold tests. CC BY 3.0.
- [evals.typesafe.ai](https://evals.typesafe.ai) - TypeSafe's workflow evals: four production-shaped workflows scored against frontier LLM reference probabilities.
- [jev-benchmarks](https://github.com/AbdelStark/jev-benchmarks) - Independent calibration, selective-risk, and latency measurements of Jev against GLiNER and other baselines.
- [JevBench](https://github.com/fstandhartinger/jevbench) - Public harness for Jev-class decision models: intelligence, calibration, speed, and cost on shared tasks.
- [MASSIVE](https://github.com/alexa/massive) - Multilingual intent-and-slot dataset (~1M utterances, 52 languages) for testing whether a fast decision layer generalizes. CC BY 4.0.

## Reading

- [Cookbooks](https://docs.typesafe.ai/cookbooks) - Official patterns: parallel questions, citation check, RAG passage classification, date extraction, skill suggestion.
- [Introducing System One Models & Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) - TypeSafe's launch post: RLCD, parallel sampling, and the System One / LLM split.
- [Jev 1.13 jaggedness](https://docs.typesafe.ai/model-jaggedness/jev-1.13) - First-party failure modes: counting, dates, indirection, distractors, generation. Keep arithmetic in code.
- [System One (docs)](https://docs.typesafe.ai/concepts/system-one) - Official definition of the model class and the Choice / Score / Noul primitives.
- [Dual-processing accounts of reasoning, judgment, and social cognition](https://pubmed.ncbi.nlm.nih.gov/18154502/) - Evans (2008). Review of dual-process theories; System 1 is a family of accounts, not one algorithm.
- [Judgment under Uncertainty: Heuristics and Biases](https://www.science.org/doi/10.1126/science.185.4157.1124) - Tversky & Kahneman (1974). Canonical source on heuristic judgment under uncertainty.
- [Reasoning the Fast and Frugal Way](https://pubmed.ncbi.nlm.nih.gov/8888650/) - Gigerenzer & Goldstein (1996). Simple heuristics as computationally explicit alternatives under bounded information.
- [System-1.x: Learning to Balance Fast and Slow Planning with Language Models](https://arxiv.org/abs/2407.14414) - Learns when to use fast direct planning versus slower search, rather than picking one globally.
- *Thinking, Fast and Slow* — Daniel Kahneman. Popular synthesis of the System 1 / System 2 framing; pair it with the papers above.

## Contribute

Open a PR with one line in the matching section:

```
- [name](url) - One sentence on what it does. Extra tags if they help choose.
```

Alphabetical within a section. Prefer a working repo over a landing page.
